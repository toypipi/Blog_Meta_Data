---
title: Create Phone Number
categories:
  - 技术
tags:
  - 每日一练
date: 2023-10-19 10:04:23
---

---

# DESCRIPTION

Write a function that accepts an array of 10 integers (between 0 and 9), that returns a string of those numbers in the form of a phone number.

Example

`createPhoneNumber([1, 2, 3, 4, 5, 6, 7, 8, 9, 0]) // => returns "(123) 456-7890"`

The returned format must be correct in order to complete this challenge.

Don't forget the space after the closing parentheses!

# SOLUTION

- 方法一：数组的 `slice()` 方法

```js
function createPhoneNumber(numbers) {
  // Create an array of the different parts of the phone number
  const areaCode = `(${numbers.slice(0, 3).join("")})`;
  const prefix = numbers.slice(3, 6).join("");
  const suffix = numbers.slice(6, 10).join("");

  // Combine the parts into the final phone number string
  const phoneNumber = `${areaCode} ${prefix}-${suffix}`;

  return phoneNumber;
}
```

<!-- more -->

- 方法二：字符串的 `replace()` 方法

```js
function createPhoneNumber(numbers) {
  var format = "(xxx) xxx-xxxx";

  for (var i = 0; i < numbers.length; i++) {
    format = format.replace("x", numbers[i]);
  }

  return format;
}
```

- 方法三：正则表达式

```js
function createPhoneNumber(numbers) {
  return numbers.join("").replace(/(\d{3})(\d{3})(\d{4})/, "($1) $2-$3");
}
```

# Reference

[Create Phone Number](https://www.codewars.com/kata/525f50e3b73515a6db000b83/solutions/javascript?filter=all&sort=best_practice&invalids=false)
