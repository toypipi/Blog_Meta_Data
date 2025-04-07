---
title: Javascript for...in
categories:
  - 技术
tags:
  - 遍历对象属性
  - for...in
date: 2023-10-13 11:28:09
---

---

for...in 语句以任意顺序迭代一个对象的除 Symbol 以外的可枚举属性，包括继承的可枚举属性。

# 语法

```javascript
for (variable in object) {
  if(object.hasOwnProperty(variable) {
    ...
  }
}
```

`variable`
在每次迭代时，variable 会被赋值为不同的属性名。

`object`
非 Symbol 类型的可枚举属性被迭代的对象。

<!-- more -->

# 仅迭代自身的属性

如果你只要考虑对象本身的属性，而不是它的原型，那么使用 getOwnPropertyNames() 或执行 hasOwnProperty() 来确定某属性是否是对象本身的属性（也能使用 propertyIsEnumerable）。或者，如果你知道不会有任何外部代码干扰，你可以使用检查方法扩展内置原型。

下面的函数说明了 hasOwnProperty()的用法：继承的属性不显示。

```javascript
var triangle = { a: 1, b: 2, c: 3 };

function ColoredTriangle() {
  this.color = "red";
}

ColoredTriangle.prototype = triangle;

var obj = new ColoredTriangle();

for (var prop in obj) {
  if (obj.hasOwnProperty(prop)) {
    console.log(`obj.${prop} = ${obj[prop]}`);
  }
}

// Output:
// "obj.color = red"
```

# 为什么用 for ... in?

for ... in 是为遍历对象属性而构建的，不建议与数组一起使用，数组可以用 Array.prototype.forEach()和 for ... of，那么 for ... in 的到底有什么用呢？

它最常用的地方应该是用于调试，可以更方便的去检查对象属性（通过输出到控制台或其他方式）。尽管对于处理存储数据，数组更实用些，但是你在处理有 key-value 数据（比如属性用作“键”），需要检查其中的任何键是否为某值的情况时，还是推荐用 for ... in

# Reference

[for...in](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/for...in)
