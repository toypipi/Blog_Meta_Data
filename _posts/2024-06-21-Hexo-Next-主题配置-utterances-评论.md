---
title: Hexo NexT 主题配置 utterances 评论
categories:
  - 技术
tags:
  - Hexo
  - Next
  - utterances
  - 评论
date: 2024-06-21 17:59:24
---

---

最近将操作系统从 win7 升级到 win10 了，然后将 Node 等软件全部升级成最新版，顺便把博客版本也更新一下。在查看 Hexo 升级的相关文档时，碰巧看到了 [utterances](https://utteranc.es/) ，无跟踪、无广告，永远免费，所有数据直接存储在自己的 github 仓库，深得我爱。立马开始部署！

搜索、阅读了大量博客，如 [Hexo 博客添加评论功能](https://pengtech.net/hexo/hexo_blog_comments.html)，都说直接在主题的配置文件下，新增如下配置即可：

<!-- more -->

```yml
# Utterances
# For more information: https://utteranc.es
utterances:
  enable: true
  repo: yoour_github_id/the_repo_name_to_store_comments # Github repository name
  # Available values: pathname | url | title | og:title
  issue_term: pathname
  # Available values: github-light | github-dark | preferred-color-scheme | github-dark-orange | icy-dark | dark-blue | photon-dark | boxy-light
  theme: github-light
```

[NexT](https://github.com/theme-next/hexo-theme-next/pull/764) 官方仓库也说直接这样配置就行。

我配置了但是没没有生效，搜索了好久终于按照这篇 [Hexo Next 主题添加 Utterances 评论系统](https://nekodaemon.com/2020/05/20/Hexo-Next%E4%B8%BB%E9%A2%98%E6%B7%BB%E5%8A%A0Utterances%E8%AF%84%E8%AE%BA%E7%B3%BB%E7%BB%9F/) 搞定了。

究其原因就是我现在用的 NexT 版本：7.8.0 没有集成 utterances 插件（在 VSCode 里面全文搜索 utterance 也搜不到任何内容），所以尽管添加了配置但还是无法显示评论框。

NexT GitHub 仓库的[官方文档](https://github.com/theme-next/hexo-theme-next/issues) 不全，大部分配置可以参照老版本，其他功能只能靠网页搜索。

# 配置过程

## 创建 utterances.swig

在 layout/\_third-party/comments 里创建 utterances.swig，内容如下：

```
{%- if page.comments %}
<script>
NexT.utils.loadComments(document.querySelector('#utterances-container'), () => {
    // if (typeof parcelRequire === 'function') { return; }
    var js = document.createElement('script');
    js.type = 'text/javascript';
    js.src = 'https://utteranc.es/client.js';
    js.async = true;
    js.crossorigin = 'anonymous';
    js.setAttribute('repo', '{{ theme.utterances.repo }}');
    js.setAttribute('issue-term', '{{ theme.utterances.issue_term }}');
    js.setAttribute('theme', '{{ theme.utterances.theme }}');
    document.getElementById('utterances-container').appendChild(js);
});
</script>
{%- endif %}

```

## 创建 utterances.js

在 scripts/filters/comment 下创建 utterances.js，内容如下：

```
/* global hexo */

'use strict';

const path = require('path');

// Add comment
hexo.extend.filter.register('theme_inject', injects => {
  let theme = hexo.theme.config;
  if (!theme.utterances.enable) return;

  injects.comment.raw('utterances', '<div class="comments" id="utterances-container"></div>', {}, {cache: true});

  injects.bodyEnd.file('utterances', path.join(hexo.theme_dir, 'layout/_third-party/comments/utterances.swig'));

});


```

## 修改主题配置文件

在主题配置文件中添加：

```
utterances:
  enable: true
  repo: owner/repo # 这里需要修改为 你的 GitHub 用户名/刚刚创建的，用户保存博客评论的 GitHub 仓库名
  issue_term: pathname
  theme: github-light
```

## 配置 utterances 插件

在 GitHub 中创建一个公开仓库用于保存博客的评论，点击 [utterances app](https://github.com/apps/utterances) 安装 utterances 。

# 后记

在写作这篇博客的时候又检索到了 NexT 主题已经升级到了 8.20.0 ，是在另外一个仓库，这个仓库的文档比较全。接下来再研究下把博客升级到这个版本吧……

# 参考文档

- [NexT](https://github.com/theme-next)
- [Documentation](https://theme-next.org/docs.html)
- [utterances](https://utteranc.es/)
