---
title: 解决 hexo next 主题不显示 hypercomments 评论
categories:
  - 技术
tags:
  - hypercommetns
  - hexo
date: 2018-03-27 16:10:56
---

---

hexo next 使用 hypercomments 很简单，只需要在主题配置文件中的 `hypercomments_id: ` 填上自己在 hypercomments 注册站点后生成的 ID 号即可。我要说的是我按照这个方法填写以后却总是没有显示评论窗口，重新安装主题以后还是不行。
后来登录 hypercomments 官网，在设置 -> General -> Website name 栏中，发现我的站点名多了一个 "/" ，删掉它就可以正常显示了。

<img src="https://raw.githubusercontent.com/toypipi/graph_bed/master/image/20180327/hypercomments.png"/>
注意：
{%note info %}
在更新 hexo 主题时一定要先备份原来的主题，不然辛辛苦苦配置的各种效果一不小心就全没了。。。
备份以后如果发现新主题不满意，还可以马上切换回原来的主题。
{% endnote %}
