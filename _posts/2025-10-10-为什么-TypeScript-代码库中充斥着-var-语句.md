---
title: 为什么 TypeScript 代码库中充斥着 var 语句
categories:
  - 翻译
tags:
  - Var
  - TDZ
  - Temporal Dead Zone
date: 2025-10-10 10:14:40
---

---

> 原文：[The Temporal Dead Zone, or why the TypeScript codebase is littered with var statements](https://vincentrolfs.dev/blog/ts-var)

如果您使用 JavaScript 已经有一段时间，您可能知道有几种不同的方式来初始化变量。现在，我们通常使用：

```javascript
const password = "hunter2";
```

并且偶尔的，当状态可变时我们使用：

```javascript
let password = "hunter2";
```

这些声明方式已经存在一段时间了，它们拥有合理的块级作用域规则：

```javascript
function example(measurement) {
  console.log(calculation); // ReferenceError（引用错误）
  console.log(anotherCalc); // ReferenceError（引用错误）

  if (measurement > 1) {
    const calculation = measurement + 1;
    let anotherCalc = measurement * 2;
    // ...
  } else {
    // ...
  }

  console.log(calculation); // ReferenceError（引用错误）
  console.log(anotherCalc); // ReferenceError（引用错误）
}
```

<!-- more -->

但是，如果您使用 JS 的时间**真的**很长，您可能还记得那时这些声明方式都不存在。我们所拥有的只有 `var`。而 `var` 非常糟糕。不仅每个变量都是可变的，无法强制实现不变性，更糟的是，`var` 会泄漏到块级作用域之外：

```javascript
function example(measurement) {
  console.log(calculation); // undefined - 可访问！calculation 泄漏了
  console.log(i); // undefined - 可访问！i 泄漏了

  if (measurement > 1) {
    var calculation = measurement + 1;
    // ...
  } else {
    // ...
  }
  console.log(calculation); // 1 - 可访问！calculation 泄漏了

  for (var i = 0; i < 3; i++) {
    // ...
  }

  console.log(i); // 3 - 可访问！i 泄漏了
}
```

太糟糕了！

因此，当我发现 **TypeScript 的代码库**（它本身是用 TypeScript 编写的——至少[目前](https://devblogs.microsoft.com/typescript/typescript-native-port/)是）[中充斥着像 2003 年那样的大量 var 语句](https://github.com/microsoft/TypeScript/blob/968d5deb8b5fd4af3ce85433872bdefc1eb17f10/src/compiler/scanner.ts#L1033)时，我感到非常惊讶：

```typescript
/** @internal */
export function createSourceMapGenerator(
  host: EmitHost,
  file: string,
  sourceRoot: string,
  sourcesDirectoryPath: string,
  generatorOptions: SourceMapGeneratorOptions
): SourceMapGenerator {
  /* eslint-disable no-var */
  var { enter, exit } = generatorOptions.extendedDiagnostics
    ? performance.createTimer("Source Map", "beforeSourcemap", "afterSourcemap")
    : performance.nullTimer;

  // Current source map file and its index in the sources list
  var rawSources: string[] = [];
  var sources: string[] = [];
  var sourceToSourceIndexMap = new Map<string, number>();
  var sourcesContent: (string | null)[] | undefined; // eslint-disable-line no-restricted-syntax
  var names: string[] = [];
  var nameToNameIndexMap: Map<string, number> | undefined;

  var mappingCharCodes: number[] = [];
  var mappings = "";

  // Last recorded and encoded mappings
  var lastGeneratedLine = 0;
  var lastGeneratedCharacter = 0;
  var lastSourceIndex = 0;
  var lastSourceLine = 0;
  var lastSourceCharacter = 0;
  var lastNameIndex = 0;
  var hasLast = false;
  // ... 等等
}
```

原因与所谓的[暂时性死区（Temporal Dead Zone，简称 TDZ）](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let#temporal_dead_zone_tdz)有关。

对于代码中的每个变量，都存在一个区域，在这个区域内变量已被声明，但**尚未初始化**。下面的例子可以清楚地说明这一点：

```javascript
function example() {
  const result = Math.random() < 0.5 ? useX() : 1; // 50% 的概率出现 ReferenceError

  const x = 10;

  return result;

  function useX() {
    return x;
  }
}
```

在上面的例子中，将 `useX` 声明在函数的底部是完全有效的。问题出在当你在 `x` 尚未初始化之前就调用它时——换句话说，当解释器仍处于 `x` 的 **TDZ** 中时。解释器拒绝让您访问 `x` 并抛出一个错误。

这一点非常好！因为如果您在这个例子中使用 `var` 而不是 `const`，就不会报错，函数只会简单地返回 `undefined`！

```javascript
function example() {
  var result = useX(); // undefined

  var x = 10;

  return result; // undefined

  function useX() {
    return x;
  }
}
console.log(example()); // undefined
```

太糟糕了！

所以 TDZ 实际上是 `const` 和 `let` 带来的一个**超有用的特性**。那么为什么 TypeScript 不愿意使用它呢？

**答案是性能（Performance）。**

判断是否处于变量的 TDZ 中，对解释器来说是一项繁重的工作。正如您在上面看到的，它不能静态完成，而是取决于**非确定性的运行时行为**。这会带来显著的性能开销，这对 TypeScript 代码库来说是至关重要的。

在将大量声明迁移回 `var` 之后，他们在某些基准测试中看到了[8% 的性能提升](https://github.com/microsoft/TypeScript/issues/52924)。

对我个人而言，我很高兴不必再使用 `var` 了。而对于 TypeScript，我确信这只是他们[将代码库迁移到 Go 语言](https://devblogs.microsoft.com/typescript/typescript-native-port/)的又一个理由。

# 译注

文章中提到的“暂时性死区”（Temporal Dead Zone，简称 TDZ）是理解 JavaScript 中 `let` 和 `const` 行为的关键概念。

简单来说，TDZ 是指**变量已经存在于作用域中，但你不能访问它的时间段。**

---

## 暂时性死区的核心理解

要理解 TDZ，我们需要先了解 `var`、`let` 和 `const` 在作用域内的声明过程。

### 1\. `var` 的行为：变量提升 (Hoisting)

使用 `var` 声明的变量，在代码执行前，会被“提升”到其所在作用域（函数作用域）的顶部。在实际执行代码时，它虽然被提升了，但**会默认被初始化为 `undefined`**。

- **代码：**
  ```javascript
  console.log(myVar); // 输出：undefined (没有报错)
  var myVar = 10;
  ```
- **TDZ 视角：** `var` 没有 TDZ。它被提升并初始化为 `undefined`，所以你可以在它声明之前访问它，虽然得到的是一个无效值。

### 2\. `let` 和 `const` 的行为：TDZ 保护

`let` 和 `const` 声明的变量也会被提升到其块级作用域的顶部，但与 `var` 不同的是，**它们不会被默认初始化**。它们的状态会经历以下三个阶段：

1.  **被创建 (Created)：** 变量进入作用域，但未初始化。
2.  **暂时性死区 (TDZ)：** 从变量被创建，到代码执行到它的声明语句为止的这段时间。在这段时间内访问变量，会立即抛出 `ReferenceError`。
3.  **被初始化 (Initialized)：** 代码执行到 `let` 或 `const` 的声明行，变量被赋予一个值（或等待赋值）。

---

## 示例与 TDZ 的作用

让我们通过文章中的例子，来明确 TDZ 如何“保护”你的代码：

### 带有 TDZ 的 `const`（安全）

```javascript
function example() {
  // ---- x 的 TDZ 开始 ----

  // 试图在 TDZ 内访问 x
  const result = Math.random() < 0.5 ? useX() : 1; // 约 50% 概率调用 useX()

  // 假设 useX() 尝试访问 x
  function useX() {
    return x; // <-- 如果在这里访问，就会抛出 ReferenceError！
  }

  // ---- 代码执行到这里，x 离开 TDZ，并被初始化 ----
  const x = 10;

  return result;
}
```

**TDZ 的好处：** 它强制你在变量被赋予有效值（即初始化）之后才能使用它，避免了使用**未定义状态**的变量，从而帮助你捕获潜在的运行时错误，让代码更加可靠。

### 没有 TDZ 的 `var`（危险）

```javascript
function example() {
  var result = useX(); // useX() 访问 x，但 x 此时是 undefined

  function useX() {
    return x; // 访问的是 undefined
  }

  var x = 10; // 即使在这里赋值，也于事无补

  return result; // 返回 undefined，但程序没有报错
}
```

**`var` 的问题：** 由于 `var` 在提升时自动初始化为 `undefined`，程序虽然逻辑错误，但没有抛出错误，而是返回了 `undefined`，这会使错误更难被发现和调试。

### 总结

TDZ 不是一个物理空间，而是一个**时间概念**。它是 JavaScript 引擎设计用来确保程序在变量被正确初始化之前不会意外使用它的安全机制。

至于文章中提到的 TypeScript **为了性能而避免 TDZ**，是因为在复杂的代码库中，解释器在运行时跟踪变量是否在 TDZ 中会带来额外的性能负担。这是大型项目在**代码执行速度**和**代码安全性/现代性**之间做出的一个权衡。
