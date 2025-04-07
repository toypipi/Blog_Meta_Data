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
可以使用 *myisampack* 对 MyISAM 表进行压缩。压缩表是不能进行修改的（除非先将压缩表解除压缩，修改数据，然后再次压缩）。压缩表可以极大地减少磁盘空间占用，因此也可以减少磁盘 I/O ，从而提升查询性能。压缩表也支持索引，但索引也是只读的。

## 压缩表
```
myisampack /home/mysql/data/test2/slave_status_20171108.MYI
```
<!-- more -->
- 压缩前
![](https://raw.githubusercontent.com/toypipi/graph_bed/master/image/20180110/%E5%8E%8B%E7%BC%A9%E5%89%8D.png)
- 压缩后
![](https://raw.githubusercontent.com/toypipi/graph_bed/master/image/20180110/压缩后.png)
- 压缩率
![](https://raw.githubusercontent.com/toypipi/graph_bed/master/image/20180110/%E5%8E%8B%E7%BC%A9%E7%8E%87.png)

## 检查索引
```
myisamchk -rq /home/mysql/data/test2/slave_status_20171108.MYI
```

## 解压缩
```
myisamchk  /home/mysql/data/test2/slave_status_20171108.MYI
```

**参考文档**
[高性能MySQL](https://book.douban.com/subject/23008813/)
[mysql myisam引擎压缩表](https://www.phpsong.com/1051.html)

