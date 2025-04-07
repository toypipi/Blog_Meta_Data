---
title: MariaDB Keepalived 双主热备之踩坑记
categories:
  - 技术
tags:
  - MySQL
  - MariaDB
  - Keepalived
  - 双主热备
date: 2018-09-12 14:16:55
---

---
MariaDB 和 Keepalived 使用 yum 安装，在验证 Keepalived 时，关闭一台机器上的 MariaDB ， 总是无法杀死 Keepalived 进程，导致虚拟 ip 无法迁移到另外一台服务器上。单独执行 track_script 里配置的脚本，却可以杀死 Keepalived 进程，怀疑是 track_script 里配置的脚本没有执行，所以一开始我以为是 Keepalived 的配置文件写错了，查阅了多篇博客，反复修改还是不行。后来在 Keepalived 日志文件里发现一些提示 `Unknown keyword 'track_script{` 。经过检索，发现是配置文件和大括号之间少了一个空格。加上空格后就可以正常运行了。

总结一下：
1. 日志很重要，有些问题从日志里就可以找到答案。其实一开始就发现日志里有这个问题了，但是没有进一步深究，于是浪费了很多时间在其他方面。
2. 当没有解决思路时，可以放一放，不要一味纠结其中。配置文件我也检查了好几遍，发现了其他没有加空格的地方，唯独单单没发现这点，可能当时自己一心急于解决问题，头脑已经混乱了。

<!-- more -->

另附 Keepalived 配置单独日志的方法：
Keepalived 日志默认存放在 `/var/log/messages` 中，想要将其单独存放，需在 `/etc/sysconfig/keepalived` 文件中修改 `KEEPALIVED_OPTIONS="-D -d -S 0`。并在 `/etc/rsyslog.conf` 末尾添加 `local0.*     /var/log/keepalived.log`。

**参考文档：**
[指定 Keepalived 输出日志文件](https://blog.csdn.net/weiyuefei/article/details/78131509)
[Keepalived+Nginx 架构详解](http://blog.51cto.com/dianel/1965803)

