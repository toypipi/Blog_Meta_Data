---
title: Git remote repository
categories:
  - 技术
tags:
  - Git
  - 远程仓库
date: 2017-09-01 10:47:19
---

---
A remote repository is a repository that's just like the one you're using but it's just stored at a different location. To manage a remote repository, use the git remote command:
远程仓库与你使用的本地仓库差不多，只不过远程仓库存储在另一个地方，如 [GitHub](https://github.com/)，[码云](http://git.oschina.net/) 等。可以使用以下命令管理管理远程仓库。
{% blockquote %}
$ git remote
{% endblockquote %}

- It's possible to have links to multiple different remote repositories. 
  可以添加多个不同的远程仓库。
- A shortname is the name that's used to refer to a remote repository's location. Typically the location is a URL, but it could be a file path on the same computer.
  短命名用于指向远程仓库的地址。该地址通常为远程仓库的URL，或者是你电脑上的文件路径。
- git remote add is used to add a connection to a new remote repository.
  git remote add 命令用于添加一个新的远程仓库的连接。
- git remote -v is used to see the details about a connection to a remote.
  git remote -v 命令用于查看远程仓库的详细信息。


<!-- more -->
**参考文档：**
[GitHub & Collaboration](https://classroom.udacity.com/courses/ud456/lessons/edde9226-f71c-4a81-a858-4c83e4c7b664/concepts/27f11e0c-50d8-47d5-bf18-ac2eed717386)
[the git remote command](https://git-scm.com/docs/git-remote)