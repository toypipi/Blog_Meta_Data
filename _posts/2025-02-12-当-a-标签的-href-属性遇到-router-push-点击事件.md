---
title: 当 <a> 标签的 href 属性遇到 router.push 点击事件
categories:
  - 技术
tags:
  - Vue Routerye
  - href
date: 2025-02-12 16:09:00
---

---

今天在新增用户注册功能时，点击去注册按钮，发现页面刷新后，马上又回到了登录页面，并没有跳转到注册页面。反复排查了几个小时，最后发现问题在于 `<a>` 标签的 `href` 属性和 `router.push` 点击事件同时生效，导致页面刷新，没有正常跳转。

## 问题原因

具体代码详见 [GitHub](https://github.com/toypipi/imooc-front/blob/master/src/views/login-register/login/index.vue)，出问题的在源文件的第 50 行：

```vue
<!-- 跳转按钮 -->
<div class="pt-1 pb-3 leading-[0px] text-right"
  <a
    class="inline-block pb-1 text-zinc-400 text-right dark:text-zinc-600 hover:text-zinc-600 dark:hover:text-zinc-400 text-sm duration-300 cursor-pointer"
    href=""
    @click="onToReg"
    >去注册</a>
</div>
```

<!-- more -->

当默认行为未被阻止时：

- 浏览器会尝试加载 `href=""`，空的 href 属性值通常被视为当前页面的 URL。因此，点击这样的 `<a>` 标签时，浏览器会尝试重新加载当前页面。
- 同时，`router.push('/register')` 会被执行，但不会生效，因为页面已经刷新。
  所以会一直卡在登录页面，无法跳转到注册页面。

## 解决办法：

1. 删除 `<a>` 标签的 `href` 属性 或将 `href` 属性的值设置为 `javascript:void(0)` 或 `#`（推荐方案一）。
2. 阻止 `<a>` 标签的默认事件，在 `onToReg` 函数中添加：

```javascript
const onToReg = async () => {
  event.preventDefault(); // 阻止默认行为
  router.push("/register");
};
```
