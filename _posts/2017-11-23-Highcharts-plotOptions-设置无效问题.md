---
title: Highcharts plotOptions 设置无效问题
categories:
  - 技术
tags:
  - Highcharts
  - plotOptions
  - 无效
date: 2017-11-23 15:22:56
---

---
今天在柱形图上添加数字标签时，总是不能生效。绘图插件使用的是 Highcharts 。后来突然想到之前引入的是Highmaps js 文件，而不是 Highcharts js 文件。
```js
<script th:src="@{/js/highcharts/highmaps.js}"></script>
```
抱着试试看的心态，我将 Highcharts js 文件引入到 html 页面中，
```js
<script th:src="@{/js/highcharts/highcharts.js}"></script>
<script th:src="@{/js/highcharts/highmaps.js}"></script>
```
刷新页面发现还是不行，然后我又将 Highmaps js 文件屏蔽掉，这下就可以了。难道是两个文件相互干扰了。
<!-- more -->
于是到 Highcharts 官网，找到了这个说明：
{% note danger %}
同 Highcharts 使用方法一样，Highmaps 只需要一个 highmaps.js 文件即可运行。
如果是结合 Highcharts 一起使用，则是引入 map.js 即可。
{% endnote %}

**参考文档：**
[开始使用 Highmaps](https://www.hcharts.cn/docs/highmaps-started)
[ Highcharts + Highstock + Highmaps 混合使用](https://www.hcharts.cn/docs/start-download)
