---
title: 精进 JavaScript 技术，享受乐趣，收获利润[译]
categories:
  - 翻译
tags:
  - 前端
  - JavaScript
  - 代码优化
date: 2024-09-13 10:33:15
---

---

万字长文吐血整理，建议点赞收藏，细细品味。

今天翻译的文章是 [《Optimizing Javascript for fun and for profit》](https://romgrk.com/posts/optimizing-javascript#8-use-strings-carefully)，作者 Rom Grk。文中深入探讨了 13 个不为人知的 JavaScript 高级优化技巧。通过实际案例和详细的解释，揭示了如何通过深入理解 JavaScript 引擎的工作原理来提升代码性能。同时，强调了基准测试的重要性，并提供了一些工具和策略，帮助开发者进行基准测试。以下是文章的全文翻译：

我常常感觉，如果得到适当的优化，JavaScript 代码通常可以运行得更快。我在这里总结了一些有用的常见优化技术。需要注意的是，性能提升往往以可读性为代价，因此我把在性能和可读性之间做选择的问题留给读者。我还要指出，谈论优化就必然要谈论基准测试。如果一个函数在整体运行时间中只占很小一部分，那么花几个小时对其进行微调，使其运行速度提高 100 倍是没有意义的。如果正在进行性能优化，第一步也是最重要的一步是基准测试。我将在后面的要点中介绍这个话题。同时请注意，微基准测试通常是有缺陷的，这里介绍的也可能包括在内。我已经尽力避免这些陷阱，但在没有基准测试的情况下，不要盲目使用此处提出的任何要点。

<!-- more -->

我已经为所有可能的情况提供了可运行的示例。它们默认显示的是我使用 Arch Linux 系统的 Brave 浏览器（版本 122）[^1]上得到的结果。尽管我不愿这么说，但 Firefox 在优化方面已经有点落后了，[目前](https://foundation.mozilla.org/en/?form=donate-header)它在浏览器在市场份额（用户使用量）方面只占很小一部分，所以我不推荐将 Firefox 上得到的结果作为评估网站或应用性能的有效指标。

# 0. 避免工作

这虽然显而易见但是至关重要，因为在优化中没有比这更早的步骤了：如果你正在尝试优化，你应该首先考虑避免工作（译者注：避免工作意味着在可能的情况下，不去执行不必要的计算或操作。这可能涉及到减少函数调用、避免重复计算、或者延迟计算直到真正需要结果的时候）。这包括记忆化（通过存储函数的中间结果，当同样的输入再次出现时，可以直接返回之前计算的结果，避免重复计算。）、惰性（延迟计算，直到结果真正需要时才进行。这样可以避免在某些情况下根本不需要的计算。）和增量计算（只对改变的部分进行计算，而不是重新计算整个结果，从而节省资源。）等概念。这些概念的应用会根据上下文的不同而有所不同。例如，在 React 中，可以使用 memo() 和 useMemo() 等内置函数来避免不必要的组件渲染和计算。memo() 可以用来防止组件在父组件渲染时不必要地重新渲染，而 useMemo() 可以用来记忆计算密集型的结果。

# 1. 避免字符串比较

JavaScript 可以轻松隐藏字符串比较的实际成本。如果需要在 C 语言中比较字符串，你会使用 `strcmp(a, b)` 函数。JavaScript 使用 `===` 代替，所以你看不到 `strcmp` 函数。但它确实存在，字符串比较通常（但并不总是）需要将一个字符串中的每个字符与另一个字符串中的字符进行比较；字符串比较的时间复杂度是 O(n)。一种需要避免的常见 JavaScript 模式是将字符串用作枚举。但随着 TypeScript 的出现，这种情况因该很容易避免，因为枚举默认是整数。

```javascript
// 不推荐
enum Position {
  TOP = 'TOP',
  BOTTOM = 'BOTTOM',
}

// 推荐
enum Position {
  TOP, // = 0
  BOTTOM, // = 1
}
```

下面是两者的性能差异：

```javascript
// 1. string 比较
const Position = {
  TOP: "TOP",
  BOTTOM: "BOTTOM",
};

let _ = 0;
for (let i = 0; i < 1000000; i++) {
  let current = i % 2 === 0 ? Position.TOP : Position.BOTTOM;
  if (current === Position.TOP) _ += 1;
}

// 2. int 比较
const Position = {
  TOP: 0,
  BOTTOM: 1,
};

let _ = 0;
for (let i = 0; i < 1000000; i++) {
  let current = i % 2 === 0 ? Position.TOP : Position.BOTTOM;
  if (current === Position.TOP) _ += 1;
}
```

> 译注：博客原文这里可以直接运行测试代码，此处省略。测试结果表明，整数比较比字符串比较快了 1 倍左右。

> 关于基准测试
> 百分比结果表示在 1 秒内完成的操作数量，除以最高得分案例的操作数量。数值越高越好。

如你所见，差异可能非常显著。差异不一定是由 `strcmp` 成本造成的，因为引擎有时可以使用字符串池并通过引用进行比较，但这也是因为在 JS 引擎中整数通常按值传递，而字符串总是作为指针传递，内存访问的成本很高（参见第 5 节）。在大量使用字符串的代码中，这可能会产生巨大的影响。

举一个真实世界的例子，我能够让[这个 JSON5 JavaScript 解析器的速度提高 2 倍](https://github.com/json5/json5/pull/278)，仅仅通过将字符串常量替换为数字。

\*不幸的是它没有被合并，但开源项目就是如此。

# 2.避免不同的形状

JavaScript 引擎试图通过假设对象具有特定的形状(shape)来优化代码，并且假设函数将接收具有相同形状的对象。这允许它们一次存储该形状的所有对象的键，并在单独的平面数组中存储值。在 JavaScript 中表示如下：

```javascript
const objects = [
  {
    name: "Anthony",
    age: 36,
  },
  {
    name: "Eckhart",
    age: 42,
  },
];

// 译注：上面的对象可以表示成下面这种结构，将所有键提取出来，将所有值存储在一个数组中
const shape = [
  { name: "name", type: "string" },
  { name: "age", type: "integer" },
];

const objects = [
  ["Anthony", 36],
  ["Eckhart", 42],
];
```

> 关于术语的解释
> 我使用了 “形状” 这个词来表达这个概念，但请注意，您可能还看到过使用 “隐藏类（hidden class）” 或 “映射（map）” 描述它，具体取决于不同的引擎。

例如，在运行时，如果以下函数接收两个具有形状 `{ x: number, y: number }` 的对象，引擎将推测未来的对象将具有相同的形状，并为该形状生成优化的机器代码。

```javascript
function add(a, b) {
  return {
    x: a.x + b.x,
    y: a.y + b.y,
  };
}
```

如果传递的对象不是形状为 `{ x, y }` 而是形状为 `{ y, x }` 的对象，引擎将需要撤销其推测，而且函数将突然变得相当慢。我不会在这里展开解释，因为你应该阅读 [mraleph 的优秀文章](https://mrale.ph/blog/2015/01/11/whats-up-with-monomorphism.html)以获取更多细节，但我要强调的是，尤其是 V8 有 3 种模式，对于访问分别是：单态（1 个形状）、多态（2-4 个形状）和超多态（5 个以上形状）。你真的应该保持代码的单态性，因为一旦代码变成多态或超多态，性能下降会非常严重：

```javascript
// 初始化
let _ = 0;
// 1. 单态
const o1 = { a: 1, b: _, c: _, d: _, e: _ };
const o2 = { a: 1, b: _, c: _, d: _, e: _ };
const o3 = { a: 1, b: _, c: _, d: _, e: _ };
const o4 = { a: 1, b: _, c: _, d: _, e: _ };
const o5 = { a: 1, b: _, c: _, d: _, e: _ }; // 所有类型都一样
// 2. 多态
const o1 = { a: 1, b: _, c: _, d: _, e: _ };
const o2 = { a: 1, b: _, c: _, d: _, e: _ };
const o3 = { a: 1, b: _, c: _, d: _, e: _ };
const o4 = { a: 1, b: _, c: _, d: _, e: _ };
const o5 = { b: _, a: 1, c: _, d: _, e: _ }; // 这个类型与其他类型不同
// 3. 超多态
const o1 = { a: 1, b: _, c: _, d: _, e: _ };
const o2 = { b: _, a: 1, c: _, d: _, e: _ };
const o3 = { b: _, c: _, a: 1, d: _, e: _ };
const o4 = { b: _, c: _, d: _, a: 1, e: _ };
const o5 = { b: _, c: _, d: _, e: _, a: 1 }; // 所有类型都不一样
// 测试代码
function add(a1, b1) {
  return a1.a + a1.b + a1.c + a1.d + a1.e + b1.a + b1.b + b1.c + b1.d + b1.e;
}

let result = 0;
for (let i = 0; i < 1000000; i++) {
  result += add(o1, o2);
  result += add(o3, o4);
  result += add(o4, o5);
}
```

> 译注：博客原文这里可以直接运行测试代码，此处省略。测试结果表明，一旦代码变得多态或超多态，性能就会急剧下降。

应该如何应对这种情况？

说起来容易做起来难，但是：应该**用完全相同的形状创建所有对象**。即使是像**以不同顺序编写 React 组件属性（prop）这样微不足道的事情也可能触发这个问题**。

例如，以下是我在 React 代码库中发现的[简单案例](https://github.com/facebook/react/pull/28569)，但他们几年前就有一个[影响更大的案例](https://v8.dev/blog/react-cliff)，他们首先用整数初始化了一个对象，后来又存储了一个浮点数。是的，改变类型也会改变形状。尽管整数和浮点数都是数字（`number`）类型，但是它们在底层的表示和处理方式是不同的。开发者需要意识到这个问题，并在编写代码时考虑到这一点。

> 数字表示
> 引擎通常可以将整数编码为值。例如，V8 使用 32 位编码来表示值，整数被编码为紧凑的 [Smi](https://medium.com/fhinkel/v8-internals-how-small-is-a-small-integer-e0badc18b6da) (SMall Integer) 值，但浮点数和大整数则像字符串和对象一样作为指针传递。 JSC 使用 64 位编码、 双标签（[double tagging](https://ktln2.org/2020/08/25/javascriptcore/)）来按值传递所有数字，就像 SpiderMonkey 所做的那样，其余的则作为指针传递。

# 3.避免数组 / 对象方法

我像其他人一样喜欢函数式编程，但除非你使用 Haskell/OCaml/Rust 语言，其函数式代码被编译成高效的机器代码，否则函数式编程总是会比命令式编程慢。

```javascript
const result = [1.5, 3.5, 5.0]
  .map((n) => Math.round(n))
  .filter((n) => n % 2 === 0)
  .reduce((a, n) => a + n, 0);
```

这些方法的问题在于：

1. 它们需要完整复制数组，而这些副本后面需要由垃圾收集器释放。我们将在第 5 节更详细地探讨内存 I/O 问题。
2. 它们为 N 个操作循环 N 次，而 for 循环只需要一次循环。

```javascript
// 初始化:
const numbers = Array.from({ length: 10_000 }).map(() => Math.random());
// 1. 函数式
const result = numbers
  .map((n) => Math.round(n * 10))
  .filter((n) => n % 2 === 0)
  .reduce((a, n) => a + n, 0);
// 2. 命令式
let result = 0;
for (let i = 0; i < numbers.length; i++) {
  let n = Math.round(numbers[i] * 10);
  if (n % 2 !== 0) continue;
  result = result + n;
}
```

> 译注：博客原文这里可以直接运行测试代码，此处省略。测试结果表明，命令式比函数式快了差不多 3 倍。

对象方法如 `Object.values()`、`Object.keys()` 和 `Object.entries()`也有类似的问题，因为它们也分配了更多的数据，而内存访问是所有性能问题的根源。真的，我发誓，我将在第 5 节展示给你看。

# 4.避免间接访问

寻找优化增益的另一个地方是任何间接访问，比如下面 3 个主要来源：

```javascript
const point = { x: 10, y: 20 };

// 1.
// 代理对象很难优化，因为它们的 get/set 函数可能运行自定义逻辑，所以引擎不能做出通常的假设。
const proxy = new Proxy(point, {
  get: (t, k) => {
    return t[k];
  },
});
// 一些引擎可以使代理成本消失，但这些优化的实现是昂贵的，并且可能很脆弱，容易因为 JavaScript 代码的其他变化或引擎的更新而失效。
const x = proxy.x;

// 2.
// 通常被忽视，但通过 `.` 或 `[]` 访问对象也是间接的。在简单的情况下，引擎可以很好地优化掉这个成本：
const x = point.x;
// 但每次额外的访问都会增加成本，并使引擎更难对 `point` 的状态做出假设：
const x = this.state.circle.center.point.x;

// 3.
// 最后，函数调用也有成本。引擎通常擅长通过内联优化[^2]来减少这种成本：
function getX(p) {
  return p.x;
}
const x = getX(p);
// 但并不能保证他们一定可以。特别是如果函数调用不是来自静态函数而是来自参数：
function Component({ point, getX }) {
  return getX(point);
}
```

代理性能基准测试目前对 V8 引擎来说特别具有挑战性。上次我检查的时候，代理对象总是从即时编译器（JIT）[^3]回退到解释器，从那些结果来看，现在可能还是这种情况。

```javascript
// 1. 代理访问
const point = new Proxy({ x: 10, y: 20 }, { get: (t, k) => t[k] });

for (let _ = 0, i = 0; i < 100_000; i++) {
  _ += point.x;
}
javascript;
// 2. 直接访问
const point = { x: 10, y: 20 };
const x = point.x;

for (let _ = 0, i = 0; i < 100_000; i++) {
  _ += x;
}
```

> 译注：博客原文这里可以直接运行测试代码，此处省略。测试结果表明，直接访问比代理访问快了差不多 50 倍。

我还想要展示访问深层嵌套对象与直接访问对象之间的差异，但引擎非常擅长在存在热循环[^4]和常量对象时，通过逃逸分析[^5]来优化对象访问。我加入了一些间接访问来防止这种优化。

```javascript
// 1. 嵌套访问
const a = { state: { center: { point: { x: 10, y: 20 } } } };
const b = { state: { center: { point: { x: 10, y: 20 } } } };
const get = (i) => (i % 2 ? a : b);

let result = 0;
for (let i = 0; i < 100_000; i++) {
  result = result + get(i).state.center.point.x;
}
javascript;
// 2. 直接访问
const a = { x: 10, y: 20 }.x;
const b = { x: 10, y: 20 }.x;
const get = (i) => (i % 2 ? a : b);

let result = 0;
for (let i = 0; i < 100_000; i++) {
  result = result + get(i);
}
```

> 译注：博客原文这里可以直接运行测试代码，此处省略。测试结果表明，直接访问比嵌套访问快了差不多 2 倍。

# 5.避免缓存未命中

这一节需要一些底层知识， JavaScript 是一门高级语言，运行在虚拟机或解释器上，但底层的内存访问模式仍然对性能有影响。从 CPU 的角度来看，从 RAM 中检索内存的速度很慢。为了加快速度，它主要使用两种优化技术。

## 5.1 预取

第一种是预取技术：它会提前获取更多的内存，希望这些是你接下来会感兴趣的内存。它总是猜测，如果你请求了一个内存地址，你可能会对紧随其后的内存区域感兴趣。所以**顺序访问数据**是关键。在下面的示例中，我们可以观察到以随机顺序访问内存的影响。

```javascript
// 初始化:
const K = 1024;
const length = 1 * K * K;

// 这些点是依次创建的，因此它们在内存中是顺序分配的。
const points = new Array(length);
for (let i = 0; i < points.length; i++) {
  points[i] = { x: 42, y: 0 };
}

// 这个数组包含了与上面相同的数据，但是被随机打乱了。
const shuffledPoints = shuffle(points.slice());

// 1. 顺序
let _ = 0;
for (let i = 0; i < points.length; i++) {
  _ += points[i].x;
}
// 2. 随机
let _ = 0;
for (let i = 0; i < shuffledPoints.length; i++) {
  _ += shuffledPoints[i].x;
}
```

> 译注：博客原文这里可以直接运行测试代码，此处省略。测试结果表明，顺序访问比随机访问快了差不多 7 倍。

应该如何应对这种情况？

这个方面可能是最难实践的，因为 JavaScript 没有一种方式来指定对象在内存中的位置。但是，你可以利用这些知识来发挥优势，就像上面的例子一样，例如在重新排序或排序之前对数据进行操作。你不能假设顺序创建的对象在一段时间后还会保持在相同的位置，因为垃圾回收器可能会四处移动它们。有一个例外，那就是数字数组，，最好是 `TypedArray` 实例：

```javascript
// 从这样
const points = [
  { x: 0, y: 5 },
  { x: 0, y: 10 },
];

// 到这样
const points = new Int64Array([0, 5, 0, 10]);
```

更多详细案例，请参考这个[链接](https://mrale.ph/blog/2018/02/03/maybe-you-dont-need-rust-to-speed-up-your-js.html#optimizing-parsing---reducing-gc-pressure)。

\*请注意，虽然它包含一些现已过时的优化，但总体而言仍然准确。

## 5.2 L1/2/3 缓存

CPU 使用的第二种优化是 L1/L2/L3 缓存：这些缓存就像是更快的 RAM，但它们也更昂贵，因此容量要小得多。它们包含了 RAM 中的数据，但像最近最少使用（LRU）缓存一样工作。数据在 “热” 的时候（正在被处理）进入缓存，并在需要空间来装载新的工作数据时被写回主 RAM。所以关键是尽可能少地使用数据，以保持你的工作数据集在快速缓存中。在下面的例子中，我们可以观察到破坏每一个连续缓存的效果。

```javascript
// 初始化:
const KB = 1024
const MB = 1024 \* KB

// 这些是适应这些缓存的大致大小。如果你的机器上没有得到相同的结果，可能是因为你的 L1/L2/L3 大小不同。
const L1  = 256 * KB
const L2  =   5 * MB
const L3  =  18 * MB
const RAM =  32 * MB

// 我们将在所有测试用例中访问同一个缓冲区，但我们只在第一个用例中访问前 0 到 L1 条目，
// 在第二个用例中访问 0 到 L2 条目，依此类推
const buffer = new Int8Array(RAM)
buffer.fill(42)

const random = (max) => Math.floor(Math.random() \* max)
javascript
// 1. L1
let r = 0; for (let i = 0; i < 100000; i++) { r += buffer[random(L1)] }
javascript
// 2. L2
let r = 0; for (let i = 0; i < 100000; i++) { r += buffer[random(L2)] }
javascript
// 3. L3
let r = 0; for (let i = 0; i < 100000; i++) { r += buffer[random(L3)] }
javascript
// 4. RAM
let r = 0; for (let i = 0; i < 100000; i++) { r += buffer[random(RAM)] }
```

> 译注：博客原文这里可以直接运行测试代码，此处省略。测试结果表明，L1 访问速度最快，RAM 访问速度最慢。

应该如何应对这种情况？

**无情地消除每一个可以消除的数据或内存**分配。你的数据集越小，你的程序运行得越快。内存 I/O 是 95% 程序的瓶颈。另一种好的策略是将你的工作分成块，并确保你一次只处理一个小数据集。

有关 CPU 和内存的更多详细信息，请参阅此[链接](https://people.freebsd.org/~lstewart/articles/cpumemory.pdf)。

> 关于不可变数据结构
> 不可变性对于清晰性和正确性来说非常有用，但在性能方面，更新不可变数据结构意味着创建容器的副本，这就需要更多的内存 I/O，可能会清除你的缓存。在可能的情况下，你应该避免使用不可变数据结构。

> 关于展开运算符 { ...spread }
> 它非常方便，但每次使用它时都会在内存中创建一个新对象。更多内存 I/O，更慢的缓存！

# 6.避免大型对象

正如第 2 节所解释的，引擎使用形状来优化对象。然而，当形状变得太大时，引擎别无选择，只能使用常规 hashmap （如 `Map` 对象）。正如我们在第 5 节所看到的，缓存未命中会显著降低性能。Hashmap 由于其数据通常随机且均匀分布在它们所占据的内存区域，因此容易受到这种影响。让我们看看这个按 ID 索引的一些用户的映射如何表现。

```javascript
// 初始化:
const USERS_LENGTH = 1_000;
// 初始化:
const byId = {};
Array.from({ length: USERS_LENGTH }).forEach((_, id) => {
  byId[id] = { id, name: "John" };
});
let _ = 0;
// 1. [] 访问
Object.keys(byId).forEach((id) => {
  _ += byId[id].id;
});
// 2. 直接访问
Object.values(byId).forEach((user) => {
  _ += user.id;
});
```

> 译注：博客原文这里可以直接运行测试代码，此处省略。测试结果表明，直接访问比 [] 访问快了差不多 2 倍。

我们也可以观察到，随着对象大小的增长，性能如何持续下降：

```javascript
// 初始化:
const USERS_LENGTH = 100_000;
```

> 译注：博客原文这里可以直接运行测试代码，此处省略。测试结果表明，当 USERS_LENGTH 的值为 100000 时，直接访问比 [] 访问快了差不多 5 倍。

应该如何应对这种情况？

如上所述，避免频繁索引大型对象。最好事先将对象转换为数组。将 ID 作为模型的一部分来组织数据会很有帮助，因为你可以直接使用 Object.values() 方法，而不需要通过键映射来获取 ID。

# 7.使用 eval

一些 JavaScript 模式对引擎来说很难优化，通过使用 `eval()` 或其衍生产品，你可以使这些模式消失。在这个例子中，我们可以看到使用 `eval()` 避免了创建具有动态对象键的对象的成本：

```javascript
// 初始化:
const key = "requestId";
const values = Array.from({ length: 100_000 }).fill(42);
// 1. 不使用 eval
function createMessages(key, values) {
  const messages = [];
  for (let i = 0; i < values.length; i++) {
    messages.push({ [key]: values[i] });
  }
  return messages;
}

createMessages(key, values);

// 2. 使用 eval
function createMessages(key, values) {
  const messages = [];
  const createMessage = new Function(
    "value",
    `return { ${JSON.stringify(key)}: value }`
  );
  for (let i = 0; i < values.length; i++) {
    messages.push(createMessage(values[i]));
  }
  return messages;
}

createMessages(key, values);
```

> 译注：博客原文这里可以直接运行测试代码，此处省略。测试结果表明，使用 `eval` 比不使用 `eval` 快了差不多 2 倍。

eval 的另一个好的用例是编译一个过滤谓词函数[^6]，你可以丢弃那些你知道永远不会执行的分支。一般来说，任何将在热循环[^7]中运行的函数都适合这种优化。

显然，关于使用 `eval()` 的常见警告同样适用：不要信任用户输入，净化任何传递给 `eval()` 代码中的内容，并且不要创建任何跨站脚本攻击 （XSS）可能性。还要注意，有些环境不允许访问 `eval()`，例如带有内容安全策略(CSP)的浏览器页面。

# 8.谨慎使用字符串

我们已经看到，字符串比它们看起来的成本更高。好吧，我这里有一个好消息和一个坏消息，让我们按逻辑顺序（坏消息优先，好消息其次）讲吧：字符串比它们看起来的更复杂，但如果使用得当，它们也可以非常高效。

由于其使用场景，字符串操作是 JavaScript 的核心部分。为了优化字符串密集型代码，引擎必须具有创造性。我的意思是，它们必须根据不同的用例，在 C++ 中以多种字符串表示形式来表示字符串对象。你应该关注两种通用情况，因为它们对于 V8（迄今为止最常用的引擎）是成立的，通常也适用于其他引擎。

首先，使用 + 连接的字符串不会创建两个输入字符串的副本。该操作创建一个指向每个子字符串的指针。如果是在 TypeScript 中，它可能是这样的：

```javascript
class String {
  abstract value(): char[] {}
}

class BytesString {
  constructor(bytes: char[]) {
    this.bytes = bytes
  }
  value() {
    return this.bytes
  }
}

class ConcatenatedString {
  constructor(left: String, right: String) {
    this.left = left
    this.right = right
  }
  value() {
    return [...this.left.value(), ...this.right.value()]
  }
}

function concat(left, right) {
  return new ConcatenatedString(left, right)
}

const first = new BytesString(['H', 'e', 'l', 'l', 'o', ' '])
const second = new BytesString(['w', 'o', 'r', 'l', 'd'])

// 看这里, 没有数组复制！
const message = concat(first, second)
```

其次，字符串切片也不需要创建副本：它们可以简单地指向另一个字符串中的范围。继续上面的示例：

```javascript
class SlicedString {
  constructor(source: String, start: number, end: number) {
    this.source = source;
    this.start = start;
    this.end = end;
  }
  value() {
    return this.source.value().slice(this.start, this.end);
  }
}

function substring(source, start, end) {
  return new SlicedString(source, start, end);
}

// 结果是 "He"，但它仍然不包含数组副本。
// 它是从 ConcatenatedString 到 SlicedString 的两个 BytesString
const firstTwoLetters = substring(message, 0, 2);
```

但问题是：一旦你需要改变这些字节，那就是你开始支付复制成本的时刻。让我们回到我们的 `String` 类，尝试添加一个`.trimEnd` 方法：

```javascript
class String {
  abstract value(): char[] {}

  trimEnd() {
    // 调用 value() 方法时可能会涉及到一个经过切片（Sliced）、拼接（Concatenated）和两倍字节（2*Bytes）处理的字符串
    const bytes = this.value()

    const result = bytes.slice()
    while (result[result.length - 1] === ' ')
      result.pop()
    return new BytesString(result)
  }
}
```

让我们看这个例子，该例中我们比较使用变异（Mutation）与只使用连接（Concatenation）的操作：

```javascript
// 初始化:
const classNames = ["primary", "selected", "active", "medium"];
// 1. 变异
const result = classNames.map((c) => `button--${c}`).join(" ");
// 2. 连接
const result = classNames
  .map((c) => "button--" + c)
  .reduce((acc, c) => acc + " " + c, "");
```

> 译注：博客原文这里可以直接运行测试代码，此处省略。测试结果表明，使用连接比使用变异快了差不多 3 倍。

应该如何应对这种情况？

总的来说，尽量**尽可能长时间地避免变异操作**。这包括 `.trim()`、`.replace()` 等方法。思考如何可以避免使用这些方法。在一些引擎中，字符串模板也可能比 + 慢。目前在 V8 中是这样，但未来可能不是，所以总是进行基准测试。

关于上面的 `SlicedString`，你应该注意的是，如果一个非常大的字符串中的一个小子字符串在内存中存活，它可能会阻止垃圾回收集器回收大字符串！如果你正在处理大型文本并从中提取小字符串，你可能会泄露大量的内存。

```javascript
const large = Array.from({ length: 10_000 })
  .map(() => "string")
  .join("");
const small = large.slice(0, 50);
// small 将会使 large 保持存活
```

这里的解决方案是使用对我们有利的变异方法。如果我们在 `small` 上使用其中一个变异方法，它将强制创建一个新的副本，并且指向 `large` 的旧指针将丢失：

```javascript
// 替换一个不存在的 token
const small = small.replace("#".repeat(small.length + 1), "");
```

更多详细信息，请参阅 [V8 上的 string.h](https://github.com/v8/v8/blob/main/src/objects/string.h) 或 [JavaScriptCore 上的 JSString.h](https://github.com/WebKit/WebKit/blob/main/Source/JavaScriptCore/runtime/JSString.h)。

> 关于字符串的复杂性
> 我非常快速地浏览了这些内容，但是有很多实现细节增加了字符串的复杂性。每种字符串表示形式通常都有最小长度限制。例如，连接字符串可能不适用于非常小的字符串。或者有时会有一些限制，比如避免指向一个子串的子串。阅读上面链接的 C++ 文件可以很好地了解这些实现细节，哪怕只是读读注释也好。

# 9.使用专门化

性能优化中的一个重要概念是 _专门化_ ：根据特定用例的约束调整你的逻辑。这通常意味着弄清楚哪些条件对你的案例 _可能_ 是真的，并为这些条件编码。

假设我们是一个商户，有时需要给产品列表添加标签。我们从经验中知道，我们的标签通常是空的。了解到这个信息，我们可以为那个案例专门化我们的函数：

```javascript
// 初始化:
const descriptions = ["apples", "oranges", "bananas", "seven"];
const someTags = {
  apples: "::promotion::",
};
const noTags = {};

// 将产品信息转换为字符串，同时包含可用的标签
function productsToString(description, tags) {
  let result = "";
  description.forEach((product) => {
    result += product;
    if (tags[product]) result += tags[product];
    result += ", ";
  });
  return result;
}

// 将上述代码做专门化处理
function productsToStringSpecialized(description, tags) {
  // 我们知道 tags 很可能是空的，所以我们提前检查一次，
  // 然后我们可以在内部循环中移除 if 检查。
  if (isEmpty(tags)) {
    let result = "";
    description.forEach((product) => {
      result += product + ", ";
    });
    return result;
  } else {
    let result = "";
    description.forEach((product) => {
      result += product;
      if (tags[product]) result += tags[product];
      result += ", ";
    });
    return result;
  }
}
function isEmpty(o) {
  for (let _ in o) {
    return false;
  }
  return true;
}

// 1. 未作专门化处理
for (let i = 0; i < 100; i++) {
  productsToString(descriptions, someTags);
  productsToString(descriptions, noTags);
  productsToString(descriptions, noTags);
  productsToString(descriptions, noTags);
  productsToString(descriptions, noTags);
}
// 2. 专门化处理
for (let i = 0; i < 100; i++) {
  productsToStringSpecialized(descriptions, someTags);
  productsToStringSpecialized(descriptions, noTags);
  productsToStringSpecialized(descriptions, noTags);
  productsToStringSpecialized(descriptions, noTags);
  productsToStringSpecialized(descriptions, noTags);
}
```

> 译注：博客原文这里可以直接运行测试代码，此处省略。测试结果表明，专门化处理后比未作专门化处理快了差不多 1.2 倍。

虽然这种类型的优化只能提供适度的改进，但这些改进会累积起来。它们是对更重要的优化（如形状和内存 I/O）的一个很好的补充。但请注意，如果条件发生变化，专门化可能会对你不利，所以应该谨慎使用。

> 分支预测和无分支代码
> 对于性能来说，从你的代码中移除分支可以极大地提高效率。要了解更多关于分支预测器的信息，请阅读 Stack Overflow 上的经典回答 [为什么处理一个已排序的数组会更快](https://stackoverflow.com/questions/11227809/why-is-processing-a-sorted-array-faster-than-processing-an-unsorted-array)。

# 10.数据结构

我不会详细讨论数据结构，因为这部分内容太多需要单独的帖子。但请注意为你的用例使用错误的数据结构可能会产生比**上述任何优化都更大的影响**。我建议你熟悉像 `Map` 和 `Set` 这样的原生数据结构，并且学习链表、优先队列、树（RB 和 B+）并亲自尝试。

举一个简单的例子，让我们比较 `Array.includes` 与 `Set.has` 对于一个小列表的表现：

```javascript
// 初始化:
const userIds = Array.from({ length: 1_000 }).map((_, i) => i);
const adminIdsArray = userIds.slice(0, 10);
const adminIdsSet = new Set(adminIdsArray);
// 1. Array
let _ = 0;
for (let i = 0; i < userIds.length; i++) {
  if (adminIdsArray.includes(userIds[i])) {
    _ += 1;
  }
}
// 2. Set
let _ = 0;
for (let i = 0; i < userIds.length; i++) {
  if (adminIdsSet.has(userIds[i])) {
    _ += 1;
  }
}
```

> 译注：博客原文这里可以直接运行测试代码，此处省略。测试结果表明，Set 比 Array 快了差不多 3 倍。

如你所见，数据结构的选择会产生非常显著的差异。

作为一个真实世界的例子，我们曾经通过将数组换成链表，[将一个函数的运行时间从 5 秒减少到 22 毫秒](https://github.com/mui/mui-x/pull/9200)。

# 11.基准测试

我把这部分留到最后有一个原因：我需要用上面有趣的部分建立信誉。现在我希望已经赢得了你的信任，我要告诉你基准测试是优化中最重要的部分。它不仅是最重要的，而且也是 _困难的_ 。即使拥有 20 年的经验，我有时也会创建有缺陷的基准测试，或者错误地使用分析工具。所以无论你做什么，请把**最大的努力放在正确地使用基准测试上**。

## 11.0 从大块开始

你应该始终优先优化占你程序运行时间最大部分的函数或代码段。如果你花费时间优化其他非关键部分，那么就是在浪费时间。

## 11.1 避免微基准测试

在生产模式下运行你的代码，并基于这些观察进行优化。JavaScript 引擎非常复杂，并且在微基准测试[^8]中的表现通常与在现实场景中不同。例如，看这个微基准测试：

```javascript
const a = { type: "div", count: 5 };
const b = { type: "span", count: 10 };

function typeEquals(a, b) {
  return a.type === b.type;
}

for (let i = 0; i < 100_000; i++) {
  typeEquals(a, b);
}
```

如果你认真看了上面的内容，你会意识到引擎将为形状 `{ type: string, count: number }` 专门化这个函数。但在你的真实世界用例中，这是否成立？`a` 和 `b` 是否总是那种形状，还是会收到任何形状？如果你在生产环境中收到许多形状，这个函数的表现将会不同。

## 11.2 怀疑你结果

如果你刚刚优化完一个函数，它现在运行立马快了 100 倍，请对此表示怀疑。试着反驳你的结果，试着在生产模式下测试它，尝试传递不同的参数。同时，怀疑你的测试工具。仅仅使用开发工具观察基准测试就可以改变其行为。

## 11.3 选择你的目标

不同的引擎会比其他引擎更好或更差地优化某些模式。你应该为你相关的引擎进行基准测试，并确定哪一个更重要。这是 Babel 的[一个真实实例](https://github.com/babel/babel/pull/16357)，其中改进 V8 意味着降低 JSC 的性能。

# 12.分析和工具

关于分析和开发工具的各种评论。

## 12.1 浏览器陷阱

如果你在浏览器中进行分析，请确保使用一个干净且空的浏览器配置文件。我甚至为此使用一个单独的浏览器。如果你在分析时启用了浏览器扩展，它们可能会干扰测量结果。特别是 React DevTools 会显著影响结果，使得代码渲染可能比你用户感知的要慢。

## 12.2 采样与结构化分析

浏览器分析工具是基于采样的分析器，它们定期对堆栈进行采样。这有一个很大缺点：一些非常小但非常频繁的函数可能在这些样本之间被调用，并且可能在你会得到的堆栈图表中被严重低估。可以使用 Firefox 开发工具自定义采样间隔或 Chrome 开发工具的 CPU 节流来缓解这个问题。

## 12.3 行业工具

除了常规的浏览器开发工具，了解以下这些选项可能会有所帮助：

- Chrome devtools 有一些非常有用的实验性标志（Experimental flags），可以帮助你弄清楚为什么事情会变慢。样式失效跟踪器在调试浏览器中的样式 / 布局重新计算时是无价的。
  https://github.com/iamakulov/devtools-perf-features

- deoptexplorer-vscode 扩展允许你加载 V8/chromium 日志文件，以了解你的代码何时触发去优化（deoptimizations），例如当你将不同的形状传递给函数时。你不需要这个扩展来读取日志文件，但使用它会使体验更加愉快。
  https://github.com/microsoft/deoptexplorer-vscode

- 你可以随时编译每个 JS 引擎的调试 shell，以更详细地了解它的工作原理。这允许你运行 `perf` 和其他低级工具，还可以检查每个引擎生成的字节码和机器码。
  [V8 的示例](https://mrale.ph/blog/2018/02/03/maybe-you-dont-need-rust-to-speed-up-your-js.html#getting-the-code) | [JSC 的示例](https://zon8.re/posts/jsc-internals-part1-tracing-js-source-to-bytecode/) | SpiderMonkey 的示例（缺失）

# 后记

希望你们学到了有用的技巧。如果你有任何评论、更正或疑问，请在页脚的电子邮件中告诉我。我总是乐于收到读者的反馈或问题。

如果你已经读到这里，我邀请你参观 [The Castle](https://romgrk.com/castle)。

[^1]: Brave 122 on Arch Linux 指的是在 Arch Linux 操作系统上运行的 Brave 浏览器版本 122。Brave 是一款注重隐私保护的网络浏览器，它通过屏蔽广告和追踪器来提高用户的隐私安全和浏览速度。
[^2]: 内联（Inlining）是一种优化技术，JavaScript 引擎（如 V8、SpiderMonkey、JavaScriptCore 等）使用这种技术来减少函数调用的开销。通过内联，引擎可以将函数的代码直接插入到调用该函数的地方，从而避免了函数调用的额外开销。内联优化通常在以下情况下效果最好：当函数体较小且被频繁调用时，或者函数调用发生在热点代码（即执行非常频繁的代码段）中时。
[^3]: JIT（Just-In-Time）编译器是一种在程序运行时将源代码编译成机器代码的技术，它可以提高程序的执行速度。解释器（interpreter）是一种逐行或逐句执行源代码的程序，通常比 JIT 编译器慢，因为它没有进行优化的编译过程
[^4]: 当存在一个 热循环（即执行非常频繁的循环）和一个 常量对象（即对象在循环过程中没有改变）时，引擎可以通过逃逸分析来优化对象的访问。这种优化可能会减少访问对象属性时的性能开销。
[^5]: 逃逸分析是 JavaScript 引擎用来确定对象是否在当前作用域之外被引用的一种分析技术。如果一个对象在循环中被频繁访问，且对象本身没有被外部引用（即没有逃逸出当前作用域），引擎可能会对这个对象进行优化。
[^6]: 谓词函数是一个返回布尔值的函数，通常用于判断某个条件是否满足。
[^7]: 热循环（hot loop）是指在程序中执行非常频繁的循环。在这种情况下，循环体内的代码性能至关重要。
[^8]: 微基准测试（Microbenchmarking）是一种性能测试方法，它专注于测量代码的非常小片段的执行时间。这种方法通常用于评估和比较特定操作的性能，如函数调用、数据访问、算法实现等。
