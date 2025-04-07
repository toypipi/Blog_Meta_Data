---
title: Element tabs 切换组件钩子函数未调用
categories:
  - 技术
tags:
  - Element
  - tabs
  - 组件
  - 钩子函数
date: 2023-10-09 16:02:48
---

---

分析原因应该是点击切换标签页时，组件没有销毁。
解决办法：给组件添加 `v-if` 判断，使组件真正的创建和销毁。然后就在组件中定义的 `mounted` 和 `destoryed` 钩子函数就可以被正常调用了。

```js
<template>
  <div class="app-container">
    <el-tabs v-model="activeName" @tab-click="handleClick" type="border-card">
      <!--实时状态-->
      <el-tab-pane name="realTimeState">
        <span slot="label"> <i class="el-icon-time"></i> 实时状态</span>
        <realTimeState v-if="activeName === 'realTimeState'" />
      </el-tab-pane>
      <!--历史状态-->
      <el-tab-pane name="historyData">
        <span slot="label"> <i class="el-icon-document"></i> 历史状态</span>
        <historyData v-if="activeName === 'historyData'" />
      </el-tab-pane>
      <!--基本信息-->
      <el-tab-pane name="basicInfo">
        <span slot="label"> <i class="el-icon-info"></i> 基本信息</span>
        <basicInfo v-if="activeName === 'basicInfo'" />
      </el-tab-pane>
      <!--状态导出-->
      <el-tab-pane name="exportData">
        <span slot="label"> <i class="el-icon-download"></i> 状态导出</span>
        <exportData v-if="activeName === 'exportData'" />
      </el-tab-pane>
      <!--故障查询-->
      <el-tab-pane name="error">
        <span slot="label"> <i class="el-icon-error"></i> 故障查询</span>
        <error v-if="activeName === 'error'" />
      </el-tab-pane>
      <!--预警监测-->
      <el-tab-pane name="statusPicture">
        <span slot="label"> <i class="el-icon-picture"></i> 预警监测</span>
        <statusPicture v-if="activeName === 'statusPicture'" />
      </el-tab-pane>
    </el-tabs>
  </div>
</template>

```

<!-- more -->

# Reference

[tabs + 组件使用会导致什么呢？](https://juejin.cn/post/7035158064177086494)
