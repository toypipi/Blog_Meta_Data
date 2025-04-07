---
title: MySQL server PID file could not be found!
categories:
  - 技术
tags:
  - PID
date: 2018-07-20 15:22:17
---

---

今天使用测试数据库时发现突然无法连接了，然后登录到服务器上查看 MySQL 进程还在，使用命令 `service restart mysql` 重启 MySQL 报错:

```
MySQL server PID file could not be found!     [FAILED]
Starting MySQL...The server quit without updating PID file (/usr/local/mysql/data/mysql.pid).
```

经过一番网上查询，有说磁盘满会导致此问题，马上检查了一下，果然 `/` 目录已经使用了 98% ，删除一些不再使用的程序包后。重启 MySQL 还是同样的问题。

<!-- more -->

网上的其他解决方案试了也不行。后来我想要不使用 MySQL 安全模式启动试试看。执行命令 `./mysqld_safe` 后，检查 MySQL 日志文件 `tail -100  /var/log/mysqld.log` ，终于发现了问题所在：

{% asset_img 1.png %}

原来是 `my.cnf` 中有一个参数不支持，注释掉该参数以后，数据库可以正常启动。

问题解决，耶 ヾ(￣ ▽ ￣)Bye~
