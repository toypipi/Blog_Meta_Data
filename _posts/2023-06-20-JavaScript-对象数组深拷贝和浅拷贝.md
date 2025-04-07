---
title: JavaScript 对象数组深拷贝和浅拷贝
categories:
  - 技术
tags:
  - 对象数组
  - 深拷贝
  - 浅拷贝
date: 2023-06-20 09:13:07
---

---

遇到的问题：VUE 项目里把 data 里定义的对象数组赋值给新的变量，新的变量对数组对象做了一些操作，但却把 data 里面原本的对象数组也修改了。

下面举一个简单例子：

```JavaScript
 window.onload = function () {
      const b = [
        { name: "John", age: 25 },
        { name: "Jane", age: 30 },
      ];
      var a = b

      // 修改变量 a 的值
      a[0].age = 99;

      console.log(a); // 输出[{name: 'John', age: 99}, {name: 'Jane', age: 25}]
      console.log(b); // 输出[{name: 'John', age: 99}, {name: 'Jane', age: 25}]
    };
```

<!-- more -->

要将 JavaScript 对象数组 b 的值赋给变量 a，同时确保更改 a 的值不会影响 b 的值，可以使用对象的深拷贝方法。这将创建一个新的数组，并复制原始数组中的所有元素。

在 JavaScript 中，可以使用多种方法执行深拷贝，其中一种常见的方法是使用 JSON.stringify 和 JSON.parse 组合来实现。下面是一个示例代码：

```JavaScript
var b = [{name: 'John', age: 30}, {name: 'Jane', age: 25}];

// 使用 JSON.stringify 和 JSON.parse 执行深拷贝
var a = JSON.parse(JSON.stringify(b));

// 修改变量a的值
a[0].name = 'Updated John';

console.log(a); // 输出: [{name: 'Updated John', age: 30}, {name: 'Jane', age: 25}]
console.log(b); // 输出: [{name: 'John', age: 30}, {name: 'Jane', age: 25}]
```

在上述代码中，我们使用 JSON.stringify 将数组 b 转换为 JSON 字符串，然后使用 JSON.parse 将该 JSON 字符串解析为新的数组 a。这样做会创建一个新的数组 a，其中包含与数组 b 相同的对象，但是它们在内存中的位置是不同的。因此，对变量 a 进行的更改不会影响变量 b。

请注意，这种方法只适用于包含原始数据类型和普通对象的数组。如果数组中包含复杂对象（例如函数、正则表达式、Map、Set 等），则需要使用其他深拷贝方法，如递归复制或第三方库（例如 lodash 的 cloneDeep 函数）来确保完整的深拷贝。

最好还要注意一点:

1.slice 函数，newArr = arr.slice(0)

2.concat 函数，newArr = [].concat(arr,arr2,...)

3.assign 函数（对象），newObj = object.assign({},obj)

但以上三种方式都是对对象第一层的深拷贝，第二层之后还是浅拷贝，要实现多维数组的深拷贝，还是需要上面的方法。
