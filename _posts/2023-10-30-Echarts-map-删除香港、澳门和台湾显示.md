---
title: Echarts map 删除香港、澳门和台湾显示
categories:
  - 技术
tags:
  - Echarts
  - map
date: 2023-10-30 10:45:40
---

---

修改 map option，将香港、澳门和台湾的透明度设置为 0，同时不显示标签名：

```js
series: [
  {
    name: "",
    type: "map",
    map: "china",
    roam: true,
    itemStyle: {
      label: { show: true, areaColor: "#b4e8fe" },
    },
    emphasis: { label: { show: true } },
    data: [
      { name: "北京", value: 0, id: 110000 },
      { name: "天津", value: 0, id: 120000 },
      { name: "上海", value: 0, id: 310000 },
      { name: "重庆", value: 0, id: 500000 },
      { name: "河北", value: 0, id: 130000 },
      { name: "河南", value: 0, id: 410000 },
      { name: "云南", value: 0, id: 530000 },
      { name: "辽宁", value: 0, id: 210000 },
      { name: "黑龙江", value: 0, id: 230000 },
      { name: "湖南", value: 0, id: 430000 },
      { name: "安徽", value: 0, id: 340000 },
      { name: "山东", value: 0, id: 370000 },
      { name: "新疆", value: 0, id: 650000 },
      { name: "江苏", value: 0, id: 320000 },
      { name: "浙江", value: 0, id: 330000 },
      { name: "江西", value: 0, id: 360000 },
      { name: "湖北", value: 0, id: 420000 },
      { name: "广西", value: 0, id: 450000 },
      { name: "甘肃", value: 0, id: 620000 },
      { name: "山西", value: 0, id: 140000 },
      { name: "内蒙古", value: 0, id: 150000 },
      { name: "陕西", value: 0, id: 610000 },
      { name: "吉林", value: 0, id: 220000 },
      { name: "福建", value: 0, id: 350000 },
      { name: "贵州", value: 0, id: 520000 },
      { name: "广东", value: 0, id: 440000 },
      { name: "青海", value: 0, id: 630000 },
      { name: "西藏", value: 0, id: 540000 },
      { name: "四川", value: 0, id: 510000 },
      { name: "宁夏", value: 0, id: 640000 },
      { name: "海南", value: 0, id: 460000 },
      {
        name: "香港",
        itemStyle: {
          normal: { opacity: 0 },
        },
        label: { show: false },
      },
      {
        name: "澳门",
        itemStyle: {
          normal: { opacity: 0 },
        },
        label: { show: false },
      },
      {
        name: "台湾",
        itemStyle: {
          normal: { opacity: 0 },
        },
        label: { show: false },
      },
      { name: "南海诸岛", value: 0, id: 460324 },
    ],
    label: {
      show: true,
      fontSize: 12,
      color: "#dcdcdc",
    },
    zoom: 1.25,
  },
];
```

<!-- more -->

# Reference

[echart 怎么让南海诸岛不显示或隐藏部分省市名称](https://blog.csdn.net/xieamy/article/details/70256542)
