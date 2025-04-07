---
title: 解决Bootstrap导航切换DataTables表头不对齐
categories:
  - 技术
tags:
  - Bootstrap
  - tab
  - DataTables
  - 表头
date: 2017-11-22 14:42:52
---

---
使用Ajax从后台获取数据后，分别将最大、最小和平均值显示在三个tab中。表格插件使用的是 DataTables。初始化表格以后，只有当前激活的表格可以正常显示，点击tab切换页面中的表格表头与内容不对齐。

![当前激活的tab表格显示正常](https://raw.githubusercontent.com/toypipi/graph_bed/master/image/20171122/normal.png)
图1.当前激活的tab表格显示正常
<!-- more -->
![点击激活的tab表格显示异常](https://raw.githubusercontent.com/toypipi/graph_bed/master/image/20171122/abnormal.png)
图2.点击激活的tab表格显示异常

经过检索后找到了解决办法。如果是使用 Bootstrap 或者 zui 插件(zui 需要引入Bootstrap js 文件)，可以在控制表格的 js 文件中添加如下代码：
```js
    $('a[data-toggle="tab"]').on('shown.bs.tab',function (e) {
        $.fn.dataTable.tables({visible:true,api:true}).columns.adjust();
    })
```

![点击激活的tab表格也可以正常显示了](https://raw.githubusercontent.com/toypipi/graph_bed/master/image/20171122/normalmin.png)
图3.点击激活的tab表格也可以正常显示了

**参考文档：**
[Scrolling and Bootstrap tabs](https://datatables.net/examples/api/tabs_and_scrolling.html)
[datatables 采用bootstrap的tab方式，表头会缩在一起，为什么？](https://github.com/ssy341/datatables-cn/issues/9)
[Bootstrap 标签页（Tab）插件](http://www.runoob.com/bootstrap/bootstrap-tab-plugin.html)
