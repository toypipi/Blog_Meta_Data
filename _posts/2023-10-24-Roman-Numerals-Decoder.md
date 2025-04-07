---
title: Roman Numerals Decoder
categories:
  - 技术
tags:
  - 每日一练
date: 2023-10-24 13:47:28
---

---

# DESCRIPTION

Create a function that takes a Roman numeral as its argument and returns its value as a numeric decimal integer. You don't need to validate the form of the Roman numeral.

Modern Roman numerals are written by expressing each decimal digit of the number to be encoded separately, starting with the leftmost digit and skipping any 0s. So 1990 is rendered "MCMXC" (1000 = M, 900 = CM, 90 = XC) and 2008 is rendered "MMVIII" (2000 = MM, 8 = VIII). The Roman numeral for 1666, "MDCLXVI", uses each letter in descending order.

注意一点：题目没有描述，当较小的值出现在较大的值之前时，将从较大的值中减去该值。可参考文末的引用链接。

# SOLUTION

```js
function solution(roman) {
  // 定义一个罗马数字到整数的映射
  const romanMap = {
    I: 1,
    V: 5,
    X: 10,
    L: 50,
    C: 100,
    D: 500,
    M: 1000,
  };
  // 初始化结果变量，以存储罗马数字的最终值
  let result = 0;
  // 遍历罗马数字
  for (let i = 0; i < roman.length; i++) {
    // 获取当前字母和下一个字母
    let letter = roman[i];
    let letterAfter = roman[i + 1];
    // 如果当前字母小于后一个字母，则从结果中减去当前字母的值
    if (romanMap[letter] < romanMap[letterAfter]) {
      result -= romanMap[letter];
    } else {
      // 如果当前字母大于后一个字母，则从结果中加上当前字母的值
      result += romanMap[letter];
    }
  }
  // 返回罗马数字的最终值
  return result;
}
```

<!-- more -->

# Reference

[Roman Numerals Decoder](https://www.codewars.com/kata/51b6249c4612257ac0000005/javascript)
[Converting Decimal Numbers to Roman Numerals with JavaScript: A step-by-step guide](https://jagathishsaravanan.medium.com/converting-decimal-numbers-to-roman-numerals-with-javascript-a-step-by-step-guide-4a5197d51c2)
