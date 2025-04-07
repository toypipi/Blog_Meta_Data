---
title: Element DateTimePicker 报错 dateStr.match is not a function
categories:
  - 技术
tags:
  - Elemenmt
  - DateTimePicker 
date: 2021-01-06 17:01:23
---

---
如题，百度检索了一圈没发现正确答案，用 google 搜了一下，找到一篇报同样错误的，戳 [这里](https://blog.csdn.net/weixin_44640323/article/details/108670941)，但是也不能解决我的问题。
后来仔细分析了一下故障，在点击选择日期的时候报错，点击选择时间是正常的。
回看代码，发现这个地方是这么写的(注意代码第三行的 :default-time)：
```javascript
    <!--开始时间日期选择器-->
    <el-date-picker
      v-model="startTimeDate"
      :default-time="['00:00:00']"
      type="datetime"
      class="filter-item"
      :placeholder="$t('table.startTime')"
      value-format="yyyy-MM-dd HH:mm:ss">
    </el-date-picker>
```
我想可能是由于 :default-time 只初始化了时间，而日期没有初始化，所以导致了此问题，删除 :default-time 这行配置，果然不再报错了。
我可真是一个小机灵鬼儿  (๑╹◡╹)ﾉ"""
