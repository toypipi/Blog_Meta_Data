---
title: JavaScript 树形结构数据平铺展开
categories:
  - 技术
tags:
  - 数据转化
date: 2023-11-02 15:05:18
---

---

# 代码

```js
// 原来的树形结构
const oldTreeData = [
  {
    name: "总部",
    place: "一楼",
    children: [
      {
        name: "财务部",
        place: "二楼",
      },
      {
        name: "生产部",
        place: "三楼",
      },
      {
        name: "开发部",
        place: "三楼",
        children: [
          {
            name: "软件部",
            place: "四楼",
            children: [
              { name: "后端部", place: "五楼" },
              { name: "前端部", place: "七楼" },
              { name: "技术部", place: "六楼" },
            ],
          },
          {
            name: "硬件部",
            place: "四楼",
            children: [
              { name: "DSP部", place: "八楼" },
              { name: "ARM部", place: "二楼" },
              { name: "调试部", place: "三楼" },
            ],
          },
        ],
      },
    ],
  },
];

// 树形结构平铺
function treeDataAdapter(treeData, lastArrayData = []) {
  treeData.forEach((item) => {
    if (item.children) {
      treeDataAdapter(item.children, lastArrayData);
    }
    const { name, place } = item;
    lastArrayData.push({ name, place });
  });
  return lastArrayData;
}
// 返回平铺的组织结构
var data = treeDataAdapter(oldTreeData);
```

<!-- more -->

# Reference

[JavaScript 设计模式学习第十三篇－适配器模式](https://cloud.tencent.com/developer/article/1753396)
