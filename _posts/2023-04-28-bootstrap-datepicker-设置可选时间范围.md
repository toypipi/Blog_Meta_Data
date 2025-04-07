---
title: bootstrap-datepicker 设置可选时间范围
categories:
  - 技术
tags:
  - datepicker
date: 2023-04-28 11:44:25
---

---
datepicker 设置未来日期不可选，只能选择一年内日期。一开始通过 endDate 属性设置未来日期不可选，出现了一个奇怪的 bug，然后用如下方法实现该功能。

```javaScript
//初始化
var ops = {
    multidate: true,//同时可以选中多个日期
    todayHighlight: true, //设置当天日期高亮
    language: 'zh-CN', //语言
    autoclose: true, //选择后自动关闭
    clearBtn: true,//清除按钮
    format: "yyyy-mm-dd",//日期格式
};
$("#itxst").datepicker(ops);
//设置只能选择一年内日期
function setStartDate() {
    var dt = new Date(new Date().setDate(new Date().getDate() - 365));
    $('#itxst').datepicker('setStartDate', dt);
}
//设置不能选择当天日期起之后的日期
function setEndDate() {
    var dt = new Date(new Date().setDate(new Date().getDate()));
    $('#itxst').datepicker('setEndDate', dt);
}

setStartDate();
setEndDate();
```

[参考文档](https://www.itxst.com/bootstrap-datepicker/2uqarzaq.html)

