---
title: "OBS Failed to open NVENC Codec: Function not implemented"
categories:
  - 技术
tags:
  - OBS
  - NVENC Codec
date: 2024-05-16 18:19:11
---

---

今天打算开始我的视频剪辑之旅，首先需要下载一个 win7 支持的 OBS 版本，别问为什么是 win7，问就是公司电脑……申请新电脑及装系统贼麻烦 o(╥﹏╥)o  
我下载的是`OBS-Studio-27.2.4.exe` 版本，如果找不到可以留言获取哦~

安装完成后打开软件开始录制，发现软件报错：

{% asset_img 1.png %}

<!-- more -->

google 一下你就知道 😁 第一条就是解决办法：

```
1. Open OBS
2. Click Settings in the lower right.
3. Click Output, then look for the "Recording" box.
4. Click "Encoder" and switch it to Software instead of NVENC, then apply.

```

大功告成 🎉

# 参考文档

[OBS Failed to open NVENC Codec: Function not implemented](https://obsproject.com/forum/threads/obs-failed-to-open-nvenc-codec-function-not-implemented.124430/)
