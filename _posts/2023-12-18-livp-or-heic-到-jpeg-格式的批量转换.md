---
title: livp or heic 到 jpeg 格式的批量转换
categories:
  - 技术
tags:
  - livp
  - heic
  - jpeg
  - 批量转换
date: 2023-12-18 16:28:16
---

---

1. 首先将 `.livp` 格式的文件修改后缀名为 `.zip`。可以使用一个批处理文件（`.bat` 文件）来完成这个操作。在任意一个文本编辑器中新建一个文件，输入以下命令：

```bat
ren *.livp *.zip
```

然后将这个文件保存为 `.bat` 格式，例如 `rename_to_zip.bat`。将这个文件放到需要修改后缀名的文件夹中，双击运行即可。

2. 使用 WinRAR 软件批量解压缩 `.zip` 格式的文件。下载并安装 WinRAR 软件，然后选中需要解压缩的文件夹，右键点击选择“解压到指定文件夹”，选择目标文件夹，点击“确定”即可完成批量解压缩。

3. 使用 HEIC Utility 软件批量将 `.heic` 格式的文件转换为 `.jpeg` 格式。下载并安装 HEIC Utility 软件，打开软件，在菜单栏选择 更多 -> 批量转换，点击右上角的 `+` 号，选择上一步解压的文件夹里的所有 `.heic` 格式的文件，在左下角设置输出目录和输出质量，最后点击“开始转换”按钮即可完成批量转换。

<!-- more -->

# Reference

[livp\heic 到 jpeg 格式的批量转换](https://blog.csdn.net/goodgoodstudy___/article/details/125294762)
