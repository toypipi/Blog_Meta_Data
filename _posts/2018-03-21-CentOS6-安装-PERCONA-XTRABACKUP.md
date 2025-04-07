---
title: CentOS 6 安装 PERCONA XTRABACKUP
categories:
  - 技术
tags:
  - XTRABACKUP
  - 备份
date: 2018-03-21 11:19:03
---

---
CentOS 6 安装 PERCONA XTRABACKUP MySQL 备份工具。
1. 首先去 [PERCONA](https://www.percona.com/downloads/XtraBackup/LATEST/) 下载并解压安装包。
```
tar -xvf Percona-XtraBackup-2.4.9-ra467167cdd4-el7-x86_64-bundle.tar
```

2. 下载并安装 `libev.so.4`
```
wget ftp://rpmfind.net/linux/atrpms/el6-x86_64/atrpms/stable/libev-4.04-2.el6.x86_64.rpm
rpm -ivh libev-4.04-2.el6.x86_64.rpm
```

3. 下载并安装 `perl-DBD-MySQL`
```
yum install  perl-DBD-MySQL
```

4. 安装 XTRABACKUP
```
rpm -ivh percona-xtrabackup-24-2.4.9-1.el7.x86_64.rpm
```

5. Done!
<!-- more -->

**NOTICE**
CentOS 7 安装 XTRABACKUP 还需要安装 perl 相关依赖包。
```
yum install -y perl-Digest-MD5
```

**参考文档**
[解决安装XtraBackup出现"libev.so.4()(64bit) is needed"问题](http://www.laozuo.org/11514.html)
[解决 libev.so.4()(64bit) is needed by percona-xtrabackup-2.3.4-1.el6.x86_64案例 ](http://blog.csdn.net/evils798/article/details/51433073)