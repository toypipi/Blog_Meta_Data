---
title: Highcharts 曲线自定义最大值、最小值标签
categories:
  - 技术
tags:
  - Highcharts
  - 最大值
  - 最小值
  - 标签
date: 2017-12-28 14:04:23
---

---
主要通过 Renderer 添加文字标签。
<!-- more -->

```javascript
$(function () {
    $('#container').highcharts({
        title: {
            text: 'Highcharts custom label'
        },
        xAxis: {
            categories: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec']
        },
        series: [{
            data: [29.9, 71.5, 106.4, 129.2, 144.0, 176.0, 135.6, 148.5, 216.4, 194.1, 95.6, 54.4]
        }]
    }, function (chart) { // on complete
        var min = 1000,
            max = 0,
            pointsToShow = [0, 0],
            points = chart.series[0].points;
        Highcharts.each(points, function(p) {
            if(p.y>max) {
                pointsToShow[0] = p.index;
                max = p.y;
            }
            if(p.y<min) {
                pointsToShow[1] = p.index;
                min = p.y;
            }
        });
        render(chart, points[pointsToShow[0]], 'Max');
        render(chart, points[pointsToShow[1]], 'Min');
        //var point = chart.series[0].points[8];
    });
    function render(chart, point, text) {
        chart.renderer.label(text + ': ' + point.y,  point.plotX + chart.plotLeft -20 , point.plotY + chart.plotTop - 45, 'callout', point.plotX + chart.plotLeft, point.plotY + chart.plotTop)
            .css({
            color: '#FFFFFF',
            align: 'center',
        })
            .attr({
            fill: 'rgba(0, 0, 0, 0.75)',
            padding: 8,
            r: 5,
            zIndex: 6
        })
            .add();
    }
});

```
**参考文档：**
[绘制自定义标签](https://code.hcharts.cn/hcharts.cn/hhhhov)