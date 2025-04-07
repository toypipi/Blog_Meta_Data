---
title: Percona XtraBackup 文档翻译
categories:
  - 技术
tags:
  - XtraBackup
  - 翻译
  - 文档
date: 2018-03-22 17:16:29
---

---
{% note danger %}
Percona XtraBackup 文档翻译已经迁移到 [gitbook](https://toypipi.gitbook.io/percona-xtrabackup-2-4-9/)，本博客后续将不再更新~~~
{% endnote %}

翻译 Percona XtraBackup 官方文档，原文[在这](https://www.percona.com/doc/percona-xtrabackup/LATEST/index.html)。以下为翻译内容：

Percona XtraBackup 是一款基于 MySQL 服务器的开源热备份实用程序，在备份过程中不会锁定数据库。

它可以备份 MySQL 5.1（在Percona XtraBackup 2.1 中已经删除了对 InnoDB 5.1 的内置支持），5.5 , 5.6 和 5.7 服务器上的 InnoDB，XtraDB 和 MyISAM 表的数据，以及带有 XtraDB 的 Percona 服务器。有关其许多高级功能（包括功能比较）的高级概述，请参阅 [Percona XtraBackup](https://www.percona.com/doc/percona-xtrabackup/LATEST/intro.html)。

无论是 24x7 高负载的服务器还是低事务量的环境，Percona XtraBackup 旨在使备份成为无缝过程，而不会中断生产环境中服务器的性能。[支持商业合同](https://www.percona.com/services/support/mysql-support)。
<!-- more -->
# 介绍

## [关于 Percona XtraBackup](https://toypipi.github.io/2018/03/23/%E5%85%B3%E4%BA%8E-Percona-XtraBackup/)
## [Percona XtraBackup 如何工作](https://toypipi.github.io/2018/03/23/Percona-XtraBackup-%E5%A6%82%E4%BD%95%E5%B7%A5%E4%BD%9C/)

# 安装 

## [安装Percona XtraBackup 2.4](https://toypipi.github.io/2018/03/24/%E5%AE%89%E8%A3%85Percona-XtraBackup-2-4/)
## 从存储库安装 Percona XtraBackup
## 从源码编译和安装

# 必要条件

## [连接和权限](https://toypipi.github.io/2018/03/27/%E8%BF%9E%E6%8E%A5%E5%92%8C%E6%9D%83%E9%99%90/)
## [配置 xtrabackup](https://toypipi.github.io/2018/03/28/%E9%85%8D%E7%BD%AE-xtrabackup/)

# 备份方案

## [备份周期 - 完整备份](https://toypipi.github.io/2018/03/28/%E5%A4%87%E4%BB%BD%E5%91%A8%E6%9C%9F-%E5%AE%8C%E6%95%B4%E5%A4%87%E4%BB%BD/)
## [增量备份](https://toypipi.github.io/2018/03/30/%E5%A2%9E%E9%87%8F%E5%A4%87%E4%BB%BD/)
## [压缩备份](https://toypipi.github.io/2018/03/31/%E5%8E%8B%E7%BC%A9%E5%A4%87%E4%BB%BD/)
## [加密备份](https://toypipi.github.io/2018/04/02/%E5%8A%A0%E5%AF%86%E5%A4%87%E4%BB%BD/)

# 用户手册

## [Percona XtraBackup 用户手册](https://toypipi.github.io/2018/04/03/Percona-XtraBackup-%E7%94%A8%E6%88%B7%E6%89%8B%E5%86%8C/)

# 高级功能

## 节流备份
## 无锁二进制日志信息
## 加密的 InnoDB 表空间备份

# 教程，方法，入门指南

## xtrabackup 的用法
## innobackupex 的用法
## 入门指南
## 辅助指南

# 参考

## xtrabackup 选项参考
## innobackupex 选项参考
## xbcloud 二进制文件
## xbcrypt 二进制文件
## xbstream 二进制文件
## 已知的问题和限制
## 常问问题
## Percona XtraBackup 发行说明
## 词汇表
## 由 Percona XtraBackup 创建的文件索引
## 商标政策

# 索引和附录

## 索引
## 搜索页面