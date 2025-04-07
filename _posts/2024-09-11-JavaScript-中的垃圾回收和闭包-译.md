---
title: JavaScript 中的垃圾回收和闭包[译]
categories:
  - 翻译
tags:
  - JavaScript
  - 垃圾回收
  - 闭包
date: 2024-09-11 16:19:10
---

---

在 JavaScript 开发中，垃圾回收和闭包是两个常见但容易被忽视的技术细节。表面上，它们的工作机制看似简单，但在特定情况下，它们可能会导致内存泄漏，甚至影响应用的性能。

作者 Jake Archibald 在 [《Garbage collection and closures》](https://jakearchibald.com/2024/garbage-collection-and-closures/#an-iife-is-enough-to-trigger-the-leak)一文中通过真实的代码示例，深入剖析垃圾回收与闭包之间的复杂关系，帮助你了解如何避免内存泄漏，写出更高效、更可靠的代码。

以下是文章的全文翻译：

我和 [Surma](https://twitter.com/DasSurma)、[Jason](https://twitter.com/_developit) 一起开发时，发现函数内的垃圾回收并不像我们预期的那样工作。

```javascript
function demo() {
  const bigArrayBuffer = new ArrayBuffer(100_000_000);
  const id = setTimeout(() => {
    console.log(bigArrayBuffer.byteLength);
  }, 1000);

  return () => clearTimeout(id);
}

globalThis.cancelDemo = demo();
```

<!-- more -->

在上面的代码中，`bigArrayBuffer` 会被永远泄漏。我没想到会这样，因为：

- 一秒钟后，引用 `bigArrayBuffer` 的函数将不能调用。
- 返回的取消定时函数没有引用 `bigArrayBuffer`。

但这并不重要。原因如下：

> **JavaScript 引擎相当智能**

下面这个例子不会发生内存泄漏：

```javascript
function demo() {
  const bigArrayBuffer = new ArrayBuffer(100_000_000);
  console.log(bigArrayBuffer.byteLength);
}

demo();
```

因为当函数执行后，`bigArrayBuffer` 就不再需要了，所以它被垃圾回收了。

下面这个例子也不会发生内存泄漏：

```javascript
function demo() {
  const bigArrayBuffer = new ArrayBuffer(100_000_000);

  setTimeout(() => {
    console.log(bigArrayBuffer.byteLength);
  }, 1000);
}

demo();
```

在这个例子中：

1. 浏览器引擎看到 `bigArrayBuffer` 被内部函数引用，所以它被保留下来。它与调用 `demo()` 时创建的作用域关联。
2. 一秒钟后，引用 `bigArrayBuffer` 的函数不可再次被调用。
3. 由于作用域内没有任何可以调用的函数，`bigArrayBuffer`可以和作用域一起被回收。

下面这个例子也不会有泄漏：

```javascript
function demo() {
  const bigArrayBuffer = new ArrayBuffer(100_000_000);

  const id = setTimeout(() => {
    console.log("hello");
  }, 1000);

  return () => clearTimeout(id);
}

globalThis.cancelDemo = demo();
```

在这个例子中，引擎知道它不需要保留 `bigArrayBuffer`，因为它只是定义了，但是并没有被访问。

# 问题案例

下面这个案例就比较复杂了：

```javascript
function demo() {
  const bigArrayBuffer = new ArrayBuffer(100_000_000);

  const id = setTimeout(() => {
    console.log(bigArrayBuffer.byteLength);
  }, 1000);

  return () => clearTimeout(id);
}

globalThis.cancelDemo = demo();
```

它会泄漏，因为：

1. 浏览器引擎看到 `bigArrayBuffer` 被内部函数引用，所以它被保留下来。它与调用 `demo()` 时创建的作用域关联。
2. 一秒钟后，引用 `bigArrayBuffer` 的函数不能再被调用。
3. 但是，作用域仍然存在，因为 'cancel' 函数仍然可调用。
4. `bigArrayBuffer` 与作用域关联，所以它仍然保留在内存中。

我以为引擎会更智能，即使 `bigArrayBuffer` 不再可引用，也会进行垃圾回收，但事实并非如此。

```javascript
globalThis.cancelDemo = null;
```

现在 `bigArrayBuffer` 可以被垃圾回收了，因为作用域内没有任何可调用的函数。

这个问题并不局限于定时器，可能在其他场景中也会发生。例如：

```javascript
function demo() {
  const bigArrayBuffer = new ArrayBuffer(100_000_000);

  globalThis.innerFunc1 = () => {
    console.log(bigArrayBuffer.byteLength);
  };

  globalThis.innerFunc2 = () => {
    console.log("hello");
  };
}

demo();
// bigArrayBuffer 被保留，正如预期。

globalThis.innerFunc1 = undefined;
// bigArrayBuffer 仍然被保留，这是出乎意料的。

globalThis.innerFunc2 = undefined;
// bigArrayBuffer 现在可以被回收了。
```

我今天才知道！

# 更新

> 一个立即执行的函数表达式（IIFE）足以触发泄漏

我最初认为只有当子函数的生命周期长于其父函数的执行时，才会发生值的捕获，但事实并非如此：

```javascript
function demo() {
  const bigArrayBuffer = new ArrayBuffer(100_000_000);
  // 译者注：IIFE 通过闭包捕获了 bigArrayBuffer，即使 demo 函数执行完毕，IIFE 仍然存在（因为它被立即执行），这导致 bigArrayBuffer 仍然被引用，因此不会被垃圾回收。
  // 这意味着，只要闭包中的函数在父函数执行完毕后仍然存在，它们就会捕获并保留父函数中的变量，即使这些变量在父函数中已经不再需要。
  (() => {
    console.log(bigArrayBuffer.byteLength);
  })();

  globalThis.innerFunc = () => {
    console.log("hello");
  };
}

demo();

// bigArrayBuffer 被保留，这是出乎意料的。
```

在这里，内部的立即执行函数表达式足以触发泄漏。

> 这是一个跨浏览器的问题

这个问题在所有浏览器中都存在，并且由于性能问题不太可能被修复。

- [Chromium 相关问题](https://issues.chromium.org/issues/41070945)
- [Firefox 相关问题](https://bugzilla.mozilla.org/show_bug.cgi?id=894971)
- [WebKit 相关问题](https://bugs.webkit.org/show_bug.cgi?id=224077)

> 我不是第一个写这个问题的人

Slava Egorov 在 2012 年就这个问题[进行了深入的探讨](https://mrale.ph/blog/2012/09/23/grokking-v8-closures-for-fun.html)。
David Glasser 在 2013 年[发现了这个问题](https://point.davidglasser.net/2013/06/27/surprising-javascript-memory-leak.html)，他是 Meteor 的工程师。
Kevin Schiener 在 2024 年 5 月对它[进行了 React 中的研究](https://point.davidglasser.net/2013/06/27/surprising-javascript-memory-leak.html)。

> 不，这不是因为 `eval()`

[Hacker News](https://news.ycombinator.com/item?id=41111062) 和 Twitter 上的人很快指出，这都是因为 `eval()`，但事实并非如此。

eval 是棘手的，因为它意味着代码可以存在于无法静态分析的作用域中：

```javascript
function demo() {
  const bigArrayBuffer1 = new ArrayBuffer(100_000_000);
  const bigArrayBuffer2 = new ArrayBuffer(100_000_000);

  globalThis.innerFunc = () => {
    eval(whatever);
  };
}

demo();
```

是否在 `innerFunc` 内访问了 `bigArrayBuffer1` 或者 `bigArrayBuffer2`？没有办法知道。但浏览器可以静态确定 `eval` 存在。这会导致去优化(deopt)，然后其父作用域中的所有内容都将被保留。

> 译者注：
> 由于 eval 的存在，浏览器无法确定 `innerFunc` 内部是否会访问 `bigArrayBuffer1` 或 `bigArrayBuffer2`。因此，浏览器必须假设 `innerFunc` 可能会访问这些变量。
>
> 这种不确定性导致浏览器在优化代码时采取保守策略，即不优化可能涉及 `eval` 的代码。这被称为 “去优化”（deopt），意味着浏览器不会对这部分代码进行某些优化，以确保运行时的正确性。
>
> 由于去优化，`bigArrayBuffer1` 和 `bigArrayBuffer2` 即使在 `demo` 函数执行完毕后，也可能不会被垃圾回收，因为浏览器必须保留它们以确保 `innerFunc` 可以正常访问它们。

但是下面这个案例中，浏览器可以静态分析，因为 `eval` 的表现有点像关键字：

```javascript
const customEval = eval;

function demo() {
  const bigArrayBuffer1 = new ArrayBuffer(100_000_000);
  const bigArrayBuffer2 = new ArrayBuffer(100_000_000);

  globalThis.innerFunc = () => {
    customEval(whatever);
  };
}

demo();
```

…这个例子中去优化不会发生。因为 `eval` 关键字没有直接使用，`whatever` 将在全局作用域中执行，而不是在 `innerFunc` 作用域内。这被称为“间接 eval”，MDN 上有更多[关于这个话题](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/eval#direct_and_indirect_eval)的信息。

这种行为的存在是为了让浏览器能够将这种去优化限制在可以静态分析的情况下。

[在 GitHub 上查看此页面](https://github.com/jakearchibald/jakearchibald.com/blob/main/static-build/posts/2024/07/garbage-collection-and-closures/index.md)

# 译者注

## IIFE(Immediately Invoked Function Expression)

IIFE ，中文可以翻译为 “立即调用的函数表达式”。这是一种在定义后立即执行的 JavaScript 函数，它是一种自执行的匿名函数。

IIFE 通常用于创建一个封闭的作用域，这样可以避免变量冲突或污染全局命名空间。它的基本语法如下：

```javascript
(function () {
  // 函数体内的代码
})();
```

这个函数没有名字，并且立即执行括号中的代码。由于它立即执行，因此被称为 “立即调用”。

IIFE 还可以接受参数，并且返回值，就像普通的函数一样：

```javascript
(function (a, b) {
  return a + b;
})(1, 2); // 返回 3
```

在 ES6 引入了箭头函数之后，IIFE 的写法可以更简洁：

```javascript
(() => {
  // 函数体内的代码
})();
```

IIFE 是 JavaScript 编程中常用的一种模式，特别是在模块化开发和库的设计中，用于封装代码和创建私有变量。
