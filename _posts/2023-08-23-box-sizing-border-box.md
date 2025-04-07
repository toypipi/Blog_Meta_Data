---
title: "box-sizing: border-box"
categories:
  - 技术
tags:
  - CSS
date: 2023-08-23 15:20:36
---

---

`box-sizing: border-box;` 是一个 CSS 属性，用于指定元素的盒模型。它影响了元素的尺寸计算方式。

默认情况下，元素的盒模型是 "content-box"，这意味着元素的宽度和高度仅包括内容的宽度和高度，不包括边框（border）和内边距（padding）。这导致了在使用固定宽度和内边距时，元素的实际宽度会超出所设置的宽度。

`box-sizing: border-box;` 的作用是改变盒模型为 "border-box"，这意味着元素的宽度和高度包括内容的宽度、内边距和边框。这使得在设置元素的宽度时，你可以确保元素的最终宽度不会超出你所指定的宽度。

<!-- more -->

例如：

```css
/* 默认盒模型，宽度不包括内边距和边框 */
.element {
  width: 200px;
  padding: 20px;
  border: 5px solid #000;
  /* 实际宽度 = 内容宽度 + 内边距 + 边框 = 200px + 20px + 10px = 230px */
}

/* 使用 border-box 盒模型，宽度包括内边距和边框 */
.element {
  width: 200px;
  padding: 20px;
  border: 5px solid #000;
  box-sizing: border-box;
  /* 实际宽度 = 200px */
}
```

在实际开发中，通常会将 `box-sizing: border-box;` 应用到所有元素，以确保更一致和可控的盒模型行为。这对于创建响应式设计和布局非常有用，因为你可以更准确地控制元素的尺寸。
