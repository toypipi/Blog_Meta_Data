---
title: Highcharts 在新的图表里显示选中的曲线范围
categories:
  - 技术
tags:
  - Highcharts
  - 曲线范围
date: 2018-01-03 09:41:24
---

---
总结一下实现方式，两张图表共用一个 chart 图表配置，在图表1中根据选中区域设置 X 轴最大、最小值，图表2同步更新。
<!-- more -->

```javascript
$(function () {
    var chart = null;
    // create the chart
    $('#container').highcharts({
        chart: {
            events: {
                selection: function (event) {
                    var min = event.xAxis[0].min;
                    var max = event.xAxis[0].max;
                    chart.xAxis[0].setExtremes(Math.ceil(min), Math.ceil(max));
                    return false;
                }
            },
            zoomType: 'x'
        },
        title: {
            text: 'Chart selection demo'
        },
        subtitle: {
            text: 'Click and drag the plot area to draw a selection'
        },
        series: [{
            type: 'column',
            data: [29.9, 71.5, 106.4, 129.2, 144.0, 176.0, 135.6, 148.5, 216.4, 194.1, 95.6, 54.4]
        }, {
            data: [29.9, 71.5, 106.4, 129.2, 144.0, 176.0, 135.6, 148.5, 216.4, 194.1, 95.6, 54.4].reverse()
        }]
    });
    $('#min-chart').highcharts({
        title: {
            text: ''
        },
        series: [{
            type: 'column',
            data: [29.9, 71.5, 106.4, 129.2, 144.0, 176.0, 135.6, 148.5, 216.4, 194.1, 95.6, 54.4]
        }, {
            data: [29.9, 71.5, 106.4, 129.2, 144.0, 176.0, 135.6, 148.5, 216.4, 194.1, 95.6, 54.4].reverse()
        }]
    }, function(c) {
        chart = c;
    });
});

```
**参考文档：**
[图表缩放](https://code.hcharts.cn/hcharts.cn/hhhG8o)