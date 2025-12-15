---
title: new URL() 的问题，以及 URL.parse() 如何解决它
categories:
  - 翻译
tags:
  - URL()
  - URL.parse()
date: 2025-12-15 11:43:39
---

---

> 原文：[The problem with new URL(), and how URL.parse() fixes that](https://kilianvalkhof.com/2024/javascript/the-problem-with-new-url-and-how-url-parse-fixes-that/)

## `new URL()` 的问题，以及 `URL.parse()` 如何解决它

作为一个构建[浏览器](https://polypane.app/)的人(译注：本文作者 Kilian Valkhof 也是 Polypane 浏览器的开发者)，我需要解析大量的 URL。其中一部分是为了验证它们，但也是为了规范化它们或并从中提取特定部分。浏览器中的 `URL` API 可以让你做到这一点，但其人体工程学设计并不理想。

<!-- more -->

## `new URL()` 的问题

`new URL()` 前面的 “new” 表明它被用作构造函数：调用它会为你创建一个新的 `URL` 实例。然而，当你给它一个格式错误的、无法解析的 URL 时，它会抛出一个错误（throw an error）。因为它会抛出错误，所以你需要编写代码来处理该错误。

如果你不这样做，抛出的错误将得不到处理，你的 JS 将停止执行。下面的代码看起来很棒，但如果 `urlstring` 格式错误，它将停止执行：

```javascript
const urlstring = "this is not a URL";
const not_a_url = new URL(urlstring);
// Uncaught TypeError: URL constructor: "this is not a URL" is not a valid URL.
```

因此，你会希望将其包装在一个 `try...catch` 块中，以便捕获该错误。

```javascript
const urlstring = "this is not a URL";
let not_a_url;
try {
  not_a_url = new URL(urlstring);
} catch {
  // 我们捕获并忽略该错误
  // not_a_url 已经是 undefined，所以实际上不需要做任何事情。
}
```

这要多出很多行代码，有更多的视觉干扰，而且这意味着你必须将 `not_a_url` 从 `const` 更改为 `let` 才能对其进行覆盖。应用程序的控制流程最终变得更加复杂。

## 略微改进

`URL` API 最近的一个补充是 `URL.canParse()`，如果 URL 是一个可解析的 URL,这个函数会返回 `true`。

它直到 2023 年 12 月才实现跨浏览器可用，因此现在用于一般用途可能还为时过早，但它确实让代码更具可读性。

我们不需要再捕获错误，而是可以在解析之前先检查 URL 是否可解析，并且我们可以内联地做到这一点：

```javascript
const urlstring = "this is not a URL";
const not_a_url = URL.canParse(urlstring) && new URL(urlstring);
// not_a_url = false
```

这使得 `not_a_url` 再次成为 `const`，并且绝对更容易理解。

## 抱怨

我并没有封装一个小函数来将 `try...catch` 或 `canParse` 从我的常规代码库中抽象出来，而是决定做正确的事情并在 Twitter 上抱怨：

> 当你提供无效 URL 时让 `new URL()` 抛出错误是一个糟糕的 API 选择。

不久之后，Anne van Kesteren 回复了一个 GitHub 问题的链接，该问题讨论了向 `URL` 中添加一个不会抛出错误的 "parse" 函数。

Anne 在 2018 年就提出了这个问题，但我的推文重新引起了兴趣。不久之后，Anne 将 `URL.parse()` 添加到了规范中，并为所有浏览器引擎提交了实现 任务。

Anne 本人已在 WebKit 中实现了它，它也将在 **Chromium 126** 和 **Firefox 126** 中发布。

## 使用 `URL.parse()`

有了 `URL.parse()`，我们就可以回到最上面的那个原始示例，并保持我们的控制流程尽可能简单：

```javascript
const urlstring = "this is not a URL";
const not_a_url = URL.parse(urlstring);
// not_a_url = null
```

带有此功能的浏览器将在接下来的几个月内发布（Firefox 在 5 月，Chrome 在 6 月，我还没能确定 Safari 何时发布），所以你必须稍等片刻才能使用它，但我迫不及待地想要摆脱所有 `try..catch` 调用！
