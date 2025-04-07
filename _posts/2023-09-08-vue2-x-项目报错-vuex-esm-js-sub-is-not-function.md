---
title: vue2.x 项目报错 vuex.esm.js sub is not function
categories:
  - 技术
tags:
  - Vuex
  - devtools
date: 2023-09-08 11:25:13
---

---

此问题是 chrome vue.js devtools 插件引起的。
我的插件版本号是： Vue.js devtools 6.5.0
操作系统：win7
解决办法：
F12 打开调试控制台 -> 点击 Vue -> 点击右上角竖排三个点 -> 选择 Devtools plugins -> 点击控制台中间的 Vue2 -> 在最右侧 Plugin settings 开启 Legacy Actions 开关

<!-- more -->

References  
[vue2.x 项目报错 vuex.esm.js sub is not function](https://juejin.cn/post/7097157803478876168)
