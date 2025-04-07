---
title: VSCode git 提交记住用户名和密码
categories:
  - 技术
tags:
  - VSCode
  - git
  - 用户名
  - 密码
date: 2023-08-25 15:59:35
---

---

1.`ctrl + shift + p` 打开命令面板，输入 `Preferences:Open User Settings(JSON)`。
{% asset_img 1.png %}

<!-- more -->

2.修改 `"git.path":"输入你自己电脑安装的 git 地址"`，如果没有则自行添加。
{% asset_img 2.png %}

3.git bush 中设置全局变量

```
git config --global user.name "用户名"
git config --global user.email "用户邮箱"
```

4.设置记住用户名和密码

```
git config --global credential.helper store
```

参考文档：
[VSCode 设置 git 账号和密码](https://blog.csdn.net/Zheng_xinle/article/details/113524134)
