---
title: MySQL MyISAM 引擎压缩表
categories:
  - 技术
tags:
  - MyISAM
  - 压缩表
date: 2018-01-10 10:44:01
---

---

可以使用 _myisampack_ 对 MyISAM 表进行压缩。压缩表是不能进行修改的（除非先将压缩表解除压缩，修改数据，然后再次压缩）。压缩表可以极大地减少磁盘空间占用，因此也可以减少磁盘 I/O ，从而提升查询性能。压缩表也支持索引，但索引也是只读的。

## 压缩表

```
myisampack /home/mysql/data/test2/slave_status_20171108.MYI
```

<!-- more -->

- 压缩前
  {% asset_img 1.png %}
- 压缩后
  {% asset_img 2.png %}
- 压缩率
  {% asset_img 3.png %}

## 检查索引

```
myisamchk -rq /home/mysql/data/test2/slave_status_20171108.MYI
```

## 解压缩

```
myisamchk  /home/mysql/data/test2/slave_status_20171108.MYI
```

**参考文档**
[高性能 MySQL](https://book.douban.com/subject/23008813/)
[mysql myisam 引擎压缩表](https://www.phpsong.com/1051.html)
