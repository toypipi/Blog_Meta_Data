---
title: MySQL Workbench生成ER图
date: 2016-12-30 11:27:36
categories:
  - 技术
tags:
  - MySQL Workbench
  - ER图
---

---

在 MySQL 数据库中创建表以后，可以直接通过 MySQL Workbench 生成 ER 图。

**版本**：MySQL Workbench Version 6.3.6 (64bits)

1.点击 Workbench 菜单栏的*Database*，选中下拉框中的*Reverse Engineer*，如下图所示：

{% asset_img 1.png %}

2.根据向导提示完成配置，如下图所示：

<!-- more -->

{% asset_img 2.jpg %}

3.生成 ER 图，如下图所示：

{% asset_img 3.jpg %}

4.点击菜单栏的*File*，选中下拉框中的*Export*，在弹出框中选中*Export as PNG*，如下图所示:

{% asset_img 4.png %}

5.保存为 PNG 格式的 ER 图，如下图所示:

{% asset_img 5.png %}

6.完成！
