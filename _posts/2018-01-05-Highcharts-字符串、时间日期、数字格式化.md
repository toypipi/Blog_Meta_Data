---
title: Highcharts 字符串、时间日期、数字格式化
categories:
  - 技术
tags:
  - Highcharts
  - 格式化
date: 2018-01-05 09:27:36
---

---

格式化字符串是包含了变量的标签模板。具体的使用实例是在 xAxis.labels.format、tooltip.pointFormat 及 legend.labelFormat.
变量是格式化中用大括号包括的字符，例如 "The point value at {point.x} is {point.y}".

<!-- more -->

## 字符串格式化

```js
$(function () {
  $("#container").highcharts({
    chart: {
      type: "column",
    },
    title: {
      text: "月平均降雨量",
    },
    subtitle: {
      text: "数据来源: WorldClimate.com",
    },
    xAxis: {
      categories: [
        "一月",
        "二月",
        "三月",
        "四月",
        "五月",
        "六月",
        "七月",
        "八月",
        "九月",
        "十月",
        "十一月",
        "十二月",
      ],
      crosshair: true,
    },
    yAxis: {
      min: 0,
      title: {
        text: "降雨量 (mm)",
      },
    },
    tooltip: {
      headerFormat: '<span style="font-size:10px">{point.key}</span><table>',
      pointFormat:
        '<tr><td style="color:{series.color};padding:0">{series.name}: </td>' +
        '<td style="padding:0"><b>{point.y:.1f} mm</b></td></tr>',
      footerFormat: "</table>",
      shared: true,
      useHTML: true,
    },
    plotOptions: {
      column: {
        borderWidth: 0,
      },
    },
    series: [
      {
        name: "东京",
        data: [
          49.9, 71.5, 106.4, 129.2, 144.0, 176.0, 135.6, 148.5, 216.4, 194.1,
          95.6, 54.4,
        ],
      },
      {
        name: "纽约",
        data: [
          83.6, 78.8, 98.5, 93.4, 106.0, 84.5, 105.0, 104.3, 91.2, 83.5, 106.6,
          92.3,
        ],
      },
      {
        name: "伦敦",
        data: [
          48.9, 38.8, 39.3, 41.4, 47.0, 48.3, 59.0, 59.6, 52.4, 65.2, 59.3, 51.2,
        ],
      },
      {
        name: "柏林",
        data: [
          42.4, 33.2, 34.5, 39.7, 52.6, 75.5, 57.4, 60.4, 47.6, 39.1, 46.8, 51.1,
        ],
      },
    ],
  });
});
```

## 时间日期格式化

### 构造函数：

```
Highcharts.dateFormat(String format, [Number time], [Boolean capitalize])
```

参数列表：
{% note default %}
String format 格式化字符串，下面详细说明
Number time 需要格式化的时间戳，可选参数，留空则取当前时间
Boolean capitalize 返回结果是否大写（例如英文时间表示中的 Sunday, January 等），可选参数
{% endnote %}

返回值类型：String

### format 格式化字符说明

{% asset_img 1.jpg %}

### 举个栗子

```js
$("#container").highcharts({
  tooltip: {
    pointFormat: "Value: {point.y:,.1f} mm",
  },
  xAxis: {
    type: "datetime",
    labels: {
      format: "{value:%Y-%m-%d}",
      rotation: 45,
      align: "left",
    },
  },
  series: [
    {
      data: [
        1029.9, 1071.5, 1106.4, 1129.2, 1144.0, 1176.0, 1135.6, 1148.5, 1216.4,
        1194.1, 1095.6, 1054.4,
      ],
      pointStart: Date.UTC(2013, 0, 1),
      pointInterval: 24 * 36e5,
    },
  ],
});
```

## 数字格式化

### 构造函数

```
Highcharts.numberFormat (Number number, [Number decimals], [String decimalPoint], [String thousandsSep])
```

参数列表:
{% note default %}
number 需要格式化的数字
decimals 小数保留位数，最后一位是四舍五入，默认为 0（可选参数）
decimalPoint 小数点符，默认是“.”（可选参数）
thousandsSep 千位符，默认是“,” （可选参数）
{% endnote %}

返回值类型：String

### 举个栗子

对于数字 12223.8723
{% note info %}
Highcharts.numberFormat(12223.87) = 12,224 （默认精度是 0）
Highcharts.numberFormat(12223.87, 2) = 12223.87 （保留两位小数）
Highcharts.numberFormat(12223.87, 2, ",", " ") = 12 223,87 （小数点用“,”，千分符用“ ”）
Highcharts.numberFormat(12223.87, 2, ".", "") = 12223.87 （不显示千分符）
{% endnote %}

```js
Highcharts.setOptions({
  lang: {
    decimalPoint: ",",
    thousandsSep: " ",
  },
});
var chart = new Highcharts.Chart({
  chart: {
    renderTo: "container",
  },
  xAxis: {
    categories: [
      "Jan",
      "Feb",
      "Mar",
      "Apr",
      "May",
      "Jun",
      "Jul",
      "Aug",
      "Sep",
      "Oct",
      "Nov",
      "Dec",
    ],
  },
  tooltip: {
    pointFormat: "Value: {point.y:,.1f} mm",
  },
  series: [
    {
      data: [
        1029.9, 1071.5, 1106.4, 1129.2, 1144.0, 1176.0, 1135.6, 1148.5, 1216.4,
        1194.1, 1095.6, 1054.4,
      ],
    },
  ],
});
```

**参考文档：**
[标签及字符串格式化 | Highcharts 使用教程](https://www.hcharts.cn/docs/basic-labels-string-formatting)
[Highcharts 时间格式化函数 ](https://bbs.hcharts.cn/article-124-1.html)
[数字格式化函数：Highcharts.numberFormat() ](https://bbs.hcharts.cn/article-54-1.html)
