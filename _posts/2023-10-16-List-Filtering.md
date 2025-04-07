---
title: List Filtering
categories:
  - 技术
tags:
  - 每日一练
  - 列表筛选
date: 2023-10-16 10:06:13
---

---

# DESCRIPTION

In this kata you will create a function that takes a list of non-negative integers and strings and returns a new list with the strings filtered out.

Example

```js
filter_list([1, 2, "a", "b"]) == [1, 2];
filter_list([1, "a", "b", 0, 15]) == [1, 0, 15];
filter_list([1, 2, "aasf", "1", "123", 123]) == [1, 2, 123];
```

# SOLUTION

- 数组的 `filter()` 方法

```js
function filter_list(l) {
  // Return a new array with the strings filtered out
  return l.filter((word) => typeof word === "number");
}
```

<!-- more -->

# Reference

[List Filtering](https://www.codewars.com/kata/53dbd5315a3c69eed20002dd/javascript)
