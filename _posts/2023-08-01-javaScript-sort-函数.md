---
title: javaScript sort 函数
categories:
  - 技术
tags:
  - sort
date: 2023-08-01 13:55:25
---

---

在 JavaScript 中，Array.prototype.sort() 是一个内置的数组排序方法，用于对数组元素进行排序。它可以接受一个可选的比较函数作为参数，用于定义排序的顺序。

语法：

```javascript
array.sort([compareFunction]);
```

参数：

compareFunction：一个可选的函数，用于定义排序顺序。如果省略此参数，数组元素将按照默认的字符串排序顺序进行排序。
返回值：
排序后的数组。请注意，sort() 方法会直接修改原始数组，并且不会创建新的副本。

比较函数的形式：

```javascript
function compareFunction(a, b) {
  // 返回一个负数表示 a 应该在 b 之前
  // 返回 0 表示 a 和 b 相等的排序顺序
  // 返回一个正数表示 a 应该在 b 之后
}
```

<!-- more -->

- 如果 compareFunction(a, b) 返回负数，表示 a 小于 b，a 会排在 b 前面。
- 如果 compareFunction(a, b) 返回 0，表示 a 和 b 相等，排序顺序不变。
- 如果 compareFunction(a, b) 返回正数，表示 a 大于 b，a 会排在 b 后面。

如果不提供 compareFunction 参数，sort() 方法将按照默认的字符串排序顺序进行排序，例如：

```javascript
const fruits = ["banana", "orange", "apple", "lemon"];
fruits.sort();
console.log(fruits); // 输出 ['apple', 'banana', 'lemon', 'orange']
```

如果提供了 compareFunction 参数，排序顺序将根据该函数的逻辑来确定。例如，对于数字数组：

```javascript
const numbers = [10, 5, 8, 2, 1];
numbers.sort((a, b) => a - b); // 升序排序
console.log(numbers); // 输出 [1, 2, 5, 8, 10]
// 或者
numbers.sort((a, b) => b - a); // 降序排序
console.log(numbers); // 输出 [10, 8, 5, 2, 1]
```

需要注意的是，sort() 方法会直接修改原始数组，因此请谨慎使用以免造成不必要的副作用。如果需要保留原数组，可以在调用 sort() 前先复制一份原数组。
