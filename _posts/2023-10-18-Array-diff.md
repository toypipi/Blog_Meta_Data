---
title: Array.diff
categories:
  - 技术
tags:
  - 每日一练
  -
date: 2023-10-18 16:45:08
---

---

# DESCRIPTION

Your goal in this kata is to implement a difference function, which subtracts one list from another and returns the result.

It should remove all values from list a, which are present in list b keeping their order.

`arrayDiff([1,2],[1]) == [2]`

If a value is present in b, all of its occurrences must be removed from the other:

`arrayDiff([1,2,2,2,3],[2]) == [1,3]`

# SOLUTION

- 方法一：

```js
function arrDiff(a, b) {
  return a.filter((item) => !b.includes(item));
}
```

<!-- more -->

# Reference

[Array.diff](https://www.codewars.com/kata/523f5d21c841566fde000009/javascript)
