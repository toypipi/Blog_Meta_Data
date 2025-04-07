---
title: 解决 vue 使用 background-image 无法加载图片的问题
categories:
  - 技术
tags:
  - vue
  - background image
  - url
date: 2023-09-07 18:09:39
---

---

下面这样写无法正确加载背景图片：

```javascript
<template>
  <div class="container"></div>
</template>

<script>
export default {
  name: 'component'
}
</script>

<style scoped lang="scss">
.container {
  height: 40px;
  width: 40px;
  background-image: url('@/assets/image.svg');
}
</style>
```

<!-- more -->

正确的做法 url 应该这样写：`url('~@/assets/image.svg')`

References  
[vue cli 3 – use background image in style tag](https://stackoverflow.com/questions/51812496/vue-cli-3-use-background-image-in-style-tag/54030632#54030632)
