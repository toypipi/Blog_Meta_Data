---
title: hexo 部署博客 github 报错 Connection time out 的解决办法
categories:
  - 技术
tags:
  - hexo 部署
  - github 连接超时
date: 2024-01-24 16:59:53
---

---

今天在部署 hexo 博客时，发现 github 报错 Connection time out，查了下资料，解决办法如下：

1. 在 Git Bash 中输入：

```
ssh -T -p 443 git@ssh.github.com
```

如果看到以下提示信息 ① 表示可用：

> Hi toypipi! You've successfully authenticated, but GitHub does not provide shell access.

2. 在 Git Bash 中输入 `vim ~/.ssh/config`, 并添加以下内容：

```
Host github.com
Hostname ssh.github.com
Port 443
```

<!-- more -->

3. 再次测试 `ssh -T git@github.com`，如果看到上述提示信息 ①，表明配置成功。

4. 最后部署博客，成功！

# Reference

[解决 git-ssh: connect to host github.com port 22: Connection timed out](https://www.jianshu.com/p/c3aac5024877)
[连接 github 报错“ssh: connect to host github.com port 22: Connection timed out”](https://blog.csdn.net/YR_112233/article/details/122436258)
