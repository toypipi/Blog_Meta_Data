---
title: git 初始化已存在项目并上传 gitlab
categories:
  - 技术
tags:
  - git
  - 初始化
  - gitlab
date: 2023-04-06 15:20:07
---

---
要在已存在的目录下新建 Git 仓库，可以按照以下步骤进行操作：

1. 打开命令行终端并进入要作为 Git 仓库的目录。
2. 运行 `git init` 命令初始化该目录为 Git 仓库。
3. 将需要进行版本控制的文件添加到 Git 仓库中。可以使用 `git add <file>` 命令将文件添加到暂存区。
4. 运行 `git commit -m "<commit message>"` 命令提交更改到 Git 仓库中，并添加一条提交说明。

完成以上步骤后，已经成功在该目录下创建了一个 Git 仓库，并将文件添加到了版本控制中。

其中 3、4 步骤可以在 sourceTree 中执行。 
<!-- more -->
在 gitlab 中新建一个项目，不要勾选生成 README.md 文件，因为我们项目里已经有这个文件了。

参考以下命令就能实现把本地项目推送到 gitlab 上了！

{% asset_img git推送已存在项目到远程.png %}
