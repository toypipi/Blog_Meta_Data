---
title: Element Card 组件添加移入移出事件
categories:
  - 技术
tags:
  - Element
  - 移入 Card
  - 移出 Card
date: 2023-06-21 11:39:22
---

---

```JavaScript
 <el-card class="box-card" @mouseente native="enter" @mouseleave.native='leave'>
   <div slot="header">
    <span>卡片名称</span>
    <el-button style="float: right; padding: 3px 0" type="text">操作按钮</el-button>
   </div>
  <div v-for="o in 4" :key="o" class="text item">
    {{'列表内容 ' + o }}
  </div>
 </el-card>

 methods: {
  enter(){
    console.log('enter')
  },
  leave(){
    console.log('leave')
  }
 },
```
<!-- more -->
