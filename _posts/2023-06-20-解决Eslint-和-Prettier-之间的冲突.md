---
title: 解决 Eslint 和 Prettier 之间的冲突
categories:
  - 技术
tags:
  - Eslint
  - Prettier
date: 2023-06-20 09:26:23
---

---

解决办法，在 .eslintrc.js 文件中修改：

```JavaScript
//修改前
extends: ['plugin:vue/recommended', 'eslint:recommended'],
//修改后
 extends: ['plugin:vue/recommended', 'eslint:recommended', "plugin:prettier/recommended"],
```

[参考文档](https://juejin.cn/post/7012160233061482532)
