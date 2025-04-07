---
title: MySQL TIMESTAMP 的坑
categories:
  - 技术
tags:
  - TIMESTAMP
date: 2018-01-16 16:57:43
---

---

{% asset_img 1.png %}
上图为 SQLYog 点击改变表后的视图，其中 update_time 的类型为 timestamp ，默认值为 CURRENT_TIMESTAMP, CURRENT_TIMESTAMP 想必大家都知道，如果在插入数据时没有给 update_time 赋值，会默认使用系统当前时间。

<!-- more -->

{% asset_img 2.png %}
上图是用 MySQL 执行 `show create table weather` 后的结果。我们发现 update_time 除了有默认值 CURRENT_TIMESTAMP ，还有一个 ON UPDATE CURRENT_TIMESTAMP 字段，这表示当更新表中数据时，如果没有给 update_time 赋值，会默认使用系统当前时间更新该字段的值。

今天在更新一张天气表时，没有注意到 update_time 还有 ON UPDATE CURRENT_TIMESTAMP 默认值，导致表中数据 update_time 误更新，原来保存的数据全都废了。

经验教训：
{% note danger %}
更新数据时要先备份，以防万一；
仔细查看表结构，同时多注意 MySQL 日期时间类型。
{% endnote%}
