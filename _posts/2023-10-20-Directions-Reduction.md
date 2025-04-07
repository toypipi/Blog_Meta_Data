---
title: Directions Reduction
categories:
  - 技术
tags:
  - 每日一练
date: 2023-10-20 16:09:13
---

---

# DESCRIPTION

Write a function dirReduc which will take an array of strings and returns an array of strings with the needless directions removed (W<->E or S<->N side by side).

The Haskell version takes a list of directions with data Direction = North | East | West | South.
The Clojure version returns nil when the path is reduced to nothing.
The Rust version takes a slice of enum Direction {North, East, West, South}.

详细描述可以查看文档底部的引用链接。

# SOLUTION

- 方法一：模拟栈的方式。

```js
function dirReduc(arr) {
  const opposites = {
    NORTH: "SOUTH",
    SOUTH: "NORTH",
    EAST: "WEST",
    WEST: "EAST",
  };
  // 创建一个数组用来模拟一个栈，用于跟踪方向
  const stack = [];
  // 遍历输入的方向数组
  for (const direction of arr) {
    // 获取当前方向的反方向
    const opposite = opposites[direction];
    // 如果栈顶方向等于当前方向的反方向，则弹出栈顶方向
    if (stack[stack.length - 1] === opposite) {
      stack.pop();
    } else {
      // 否则，将当前方向压入栈顶
      stack.push(direction);
    }
  }
  // 最终，栈中剩下的方向就是去掉不必要的相反方向后的结果
  return stack;
}
```

<!-- more -->

- 方法二：正则表达式 + `while` 循环。

```js
function dirReduc(arr) {
  var str = arr.join(""),
    pattern = /NORTHSOUTH|EASTWEST|SOUTHNORTH|WESTEAST/;
  while (pattern.test(str)) str = str.replace(pattern, "");
  return str.match(/(NORTH|SOUTH|EAST|WEST)/g) || [];
}
```

# Reference

[Directions Reduction](https://www.codewars.com/kata/550f22f4d758534c1100025a/javascript)
