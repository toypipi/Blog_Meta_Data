---
title: JavaScript 匿名函数的直接调用
categories:
  - 技术
tags:
  - JavaScript
  - 匿名函数
date: 2023-06-29 10:17:20
---

---

在 JavaScript 中，可以使用匿名函数的直接调用（Immediately Invoked Function Expression，IIFE）来立即执行函数。

下面是一个使用匿名函数直接调用的示例：

```javaScript
(function() {
  // 在这里编写需要执行的代码
  console.log("这是一个匿名函数的直接调用示例。");
})();
```

在上面的示例中，我们使用 (function() { ... })() 的语法来创建一个匿名函数，并立即调用它。函数体内的代码会立即执行。

<!-- more -->

匿名函数的直接调用的主要目的是创建一个作用域边界，避免函数内部的变量和函数污染全局作用域。通过立即调用函数，函数内部的变量和函数都会在执行完后销毁，不会对全局作用域造成影响。

此外，如果需要将参数传递给匿名函数，可以在调用时传入参数：

```javaScript
(function(name) {
  console.log("Hello, " + name + "!");
})("John");
```

在上面的示例中，我们传递了一个名为 "John" 的参数给匿名函数，并在函数内部打印了 "Hello, John!"。

通过使用匿名函数的直接调用，可以在需要时立即执行一段代码，同时有效地控制变量作用域。

需要注意的是，由于匿名函数是没有名称的，因此我们无法在外部代码中引用它。如果需要多次调用该函数，可以将它赋值给一个变量，例如：

```javaScript
var myFunction = function(){
  // 匿名函数的代码
}

myFunction(); // 调用匿名函数
myFunction(); // 再次调用匿名函数
```
