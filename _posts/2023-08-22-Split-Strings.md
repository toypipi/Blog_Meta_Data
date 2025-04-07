---
title: Split Strings
categories:
  - 技术
tags:
  - 算法
date: 2023-08-22 14:52:57
---

---

1.[题目描述](https://www.codewars.com/kata/515de9ae9dcfc28eb6000001/solutions/javascript)
Complete the solution so that it splits the string into pairs of two characters. If the string contains an odd number of characters then it should replace the missing second character of the final pair with an underscore ('\_').

Examples:

```
* 'abc' =>  ['ab', 'c_']
* 'abcdef' => ['ab', 'cd', 'ef']

```

2.我的解法：

```javascript
function solution(str) {
  const pair = [];
  // 1.将字符串两两分割存入数组
  // Array.prototype.slice(start,end)
  // 如果 end >= array.length 或者省略了 end，则使用 array.length，提取所有元素直到末尾
  for (let i = 0; i < str.length; i += 2) {
    pair.push(str.slice(i, i + 2));
  }
  // 2.如果字符串长度为奇数，则为 pair 最后一个元素添加下划线
  if (str.length % 2 === 1) {
    pair[pair.length - 1] += "_";
  }
  return pair;
}
```

<!-- more -->

3.学习到的解法：

```javascript
function solution(s) {
  return (s + "_").match(/.{2}/g) || [];
}
```
