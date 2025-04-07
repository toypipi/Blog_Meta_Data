---
title: git stage 和 git stash
categories:
  - 技术
tags:
  - git
  - stage
  - stash
date: 2023-08-28 09:25:13
---

---

`git stage` 和 `git stash` 是 Git 中用于处理工作目录中的更改的两个不同的机制，它们有各自的用途和区别。

**`git stage`（也称为 `git add`）**:

- **用途**：`git stage` 或 `git add` 用于将工作目录中的更改添加到暂存区（Staging Area）中，以便之后可以提交到版本控制系统。
- **操作**：你可以使用 `git add` 命令来选择性地添加文件或更改到暂存区。例如，`git add file1.txt` 将添加名为 `file1.txt` 的文件的更改到暂存区。
- **持久性**：暂存区的内容是持久的，它们会一直保留，直到你执行 `git commit` 命令将它们提交到版本控制仓库。
- **用例**：`git stage` 用于将更改准备好以便提交到版本控制系统。通常在你认为一组更改完成并准备好提交时使用。
- <!-- more -->

  **`git stash`**:

- **用途**：`git stash` 用于将工作目录中的未提交更改（包括已经 `git add` 过的更改）保存到一个临时的存储区，以便你可以在工作目录变得干净后进行其他操作，然后再回到之前保存的更改。
- **操作**：`git stash` 命令用于保存当前的工作目录状态，包括已修改但未提交的文件，然后将工作目录重置为上一个提交的状态。你可以使用 `git stash apply` 或 `git stash pop` 来重新应用之前保存的更改到工作目录。
- **持久性**：`git stash` 是临时的，保存的更改可以随时重新应用或丢弃。一般不会一直保留，除非你明确决定保留它。
- **用例**：`git stash` 用于临时保存当前的工作进度，以便你可以在分支切换、bug 修复或其他任务之间切换而不必提交未完成的更改。这是一种在工作目录变得干净时保存进度的方法。

**区别**:

1. **目的**:

   - `git stage` 的主要目的是将更改准备好提交到版本控制系统。
   - `git stash` 的主要目的是保存未提交的更改，以便稍后重新应用。

2. **处理方式**:

   - `git stage` 将更改添加到暂存区，以备提交。
   - `git stash` 将未提交的更改保存到一个临时的存储区，然后重置工作目录。

3. **持久性**:
   - 暂存区的内容是持久的，它们会一直保留，直到你执行 `git commit` 命令将它们提交到版本控制仓库。
   - `git stash` 是临时的，保存的更改可以随时重新应用或丢弃。

选择使用哪个命令取决于你的需求。如果你要提交更改到版本控制系统，使用 `git stage`。如果你需要保存未提交的更改以进行其他任务，使用 `git stash`。通常，这两个命令可以在不同情况下一起使用以帮助你有效地管理工作目录中的更改。

`git stage` 和 `git stash` 的常用命令：

**`git stage` (或 `git add`) 常用命令**:

1. **`git add <file>`**：将指定文件的更改添加到暂存区。例如，`git add myfile.txt` 将 `myfile.txt` 中的更改添加到暂存区。

2. **`git add .`**：将当前目录下所有文件的更改添加到暂存区。

3. **`git add -A`** 或 **`git add --all`**：将所有未跟踪的文件和已跟踪的更改都添加到暂存区。

4. **`git reset <file>`**：从暂存区中移除指定文件的更改，但保留在工作目录中。这个命令通常用于取消暂存的更改。

**`git stash` 常用命令**:

1. **`git stash`**：将工作目录中的未提交更改保存到一个新的 stash 中，然后重置工作目录为上一个提交的状态。

2. **`git stash save "stash_message"`**：除了保存 stash，还可以提供一个描述性的消息来标识 stash 的用途。例如，`git stash save "Work in progress on feature X"`。

3. **`git stash list`**：列出所有保存的 stash，显示 stash 标识符和消息。

4. **`git stash apply`**：将最新的 stash 应用到工作目录，但不会从 stash 列表中删除它。你也可以指定一个特定的 stash，例如 `git stash apply stash@{2}`。

5. **`git stash pop`**：将最新的 stash 应用到工作目录，并从 stash 列表中删除它。

6. **`git stash drop`**：删除指定的 stash。你需要指定 stash 标识符，例如 `git stash drop stash@{1}`。

7. **`git stash clear`**：删除所有保存的 stash。

8. **`git stash branch <new_branch_name>`**：创建一个新分支并将最新的 stash 应用到这个新分支上。这在你需要在不同分支上继续工作时很有用。
