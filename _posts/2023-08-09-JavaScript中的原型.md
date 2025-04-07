---
title: JavaScript中的原型
categories:
  - 技术
tags:
  - 原型
date: 2023-08-09 15:35:46
---

---

在 JavaScript 中，每个对象都有一个与之关联的原型（prototype）。原型是一个对象，它包含了共享的属性和方法，可以被其他对象继承。通过原型链，JavaScript 对象可以访问到其原型对象上的属性和方法。

当你创建一个对象时，JavaScript 会自动为这个对象关联一个原型。你可以将原型看作是一个对象的"父对象"，它定义了该对象共享的属性和方法。通过原型链，对象可以访问其原型对象的属性和方法。

每个 JavaScript 对象都有一个隐藏的属性 `__proto__`，它指向了该对象的原型。在访问一个对象的属性或方法时，如果对象本身没有找到，JavaScript 引擎会沿着原型链去查找对象的原型，然后再去原型的原型，依此类推，直到找到所需的属性或方法或者到达原型链的末端。

例如：

<!-- more -->

```javascript
// 定义一个原型对象
const animalPrototype = {
  sound: "Animal makes a sound",
  makeSound: function () {
    console.log(this.sound);
  },
};

// 创建一个对象并关联原型
const dog = Object.create(animalPrototype);
dog.sound = "Woof!";

// 调用对象自身的属性和原型链上的方法
dog.makeSound(); // 输出 "Woof!"
```

在上面的例子中，dog 对象通过 Object.create(animalPrototype) 创建，并且关联了原型对象 animalPrototype。虽然 dog 自身没有定义 makeSound 方法，但它可以通过原型链访问到 animalPrototype 上的 makeSound 方法。

总之，JavaScript 中的原型是一种机制，它允许对象通过原型链继承共享的属性和方法。这也是 JavaScript 中实现继承的一种方式。
