---
title: git 修改已经提交并 push 的 commit 注释
categories:
  - 技术
tags:
  - commit
date: 2020-08-06 10:58:44
---

---
# 修改倒数第 1 次的 commit
```shell script
$ git rebase -i HEAD~1
```
# 修改 pick 为 edit
<!-- more -->
- 按 i 进入编辑模式
- 将要修改的那一条的pick修改成edit
- 按 esc 退出编辑模式，输入 :wq 保存并退出

# 修改commit注释内容
```shell script
$ git commit --amend
```
- 按 i 进入编辑模式
- 将要修改的那一条的pick修改成edit
- 按 esc 退出编辑模式，输入 :wq 保存并退出      

```shell script
$ git rebase --continue
```
# 强制push
```shell script
$ git push --force github
```

** 参考文档 **
[git——修改已经提交并push后的commit注释](https://www.cnblogs.com/superjishere/p/11578130.html)

 