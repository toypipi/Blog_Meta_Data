---
title: The Hashtag Generator
categories:
  - 技术
tags:
  - 每日一练
date: 2023-10-26 14:39:09
---

---

# The Hashtag Generator

The marketing team is spending way too much time typing in hashtags.
Let's help them with our own Hashtag Generator!

Here's the deal:

It must start with a hashtag (#).
All words must have their first letter capitalized.
If the final result is longer than 140 chars it must return false.
If the input or the result is an empty string it must return false.

# SOLUTION

- 方法一：普通循环

```js
function generateHashtag(str) {
  // 判断是否是空字符串，或只包含多个空格的字符串
  if (str.length === 0 || str.replace(/\s+/g, "").length === 0) {
    return false;
  }
  // 将每个单词的首字母大写
  let words = str.split(" ");
  for (let i = 0; i < words.length; i++) {
    words[i] = words[i].charAt(0).toUpperCase() + words[i].slice(1);
  }
  // 拼接 #
  let result = "#" + words.join("");
  return result.length > 140 ? false : result;
}
```

<!-- more -->

- 方法二：`reduce()` 方法

```js
function generateHashtag(str) {
  var hashtag = str.split(" ").reduce(function (tag, word) {
    return tag + word.charAt(0).toUpperCase() + word.substring(1);
  }, "#");

  return hashtag.length == 1 || hashtag.length > 140 ? false : hashtag;
}
```

- 方法三：

```js
function generateHashtag(str) {
  if (!str.trim()) return false;

  const result =
    "#" +
    str
      .split(" ")
      .map((l) => l.charAt(0).toUpperCase() + l.slice(1))
      .join("");

  return result.length > 140 ? false : result;
}
```

# 判断回文数

```js
var isPalindrome = function (x) {
  // 小于零肯定不是回文数
  if (x < 0) {
    return false;
  }
  // 反转数字
  const revertedX = x.toString().split("").reverse().join("");
  // 比较反转后的数字是否相等
  return x === parseInt(revertedX, 10);
};
```

# Reference

[The Hashtag Generator](https://www.codewars.com/kata/52449b062fb80683ec000024/solutions/javascript?filter=me&sort=best_practice&invalids=false)
[9. 回文数](https://leetcode.cn/problems/palindrome-number/)
