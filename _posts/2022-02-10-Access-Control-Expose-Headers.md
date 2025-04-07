---
title: Access-Control-Expose-Headers
categories:
  - 技术
tags:
  - 前端
  - Access-Control-Expose-Headers
  - 文件下载
date: 2022-02-10 13:57:31
---

---

# Access-Control-Expose-Headers

响应首部 **`Access-Control-Expose-Headers`** 列出了哪些首部可以作为响应的一部分暴露给外部。

默认情况下，只有七种 [simple response headers](https://developer.mozilla.org/zh-CN/docs/Glossary/Simple_response_header) （简单响应首部）可以暴露给外部：

- [`Cache-Control`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Cache-Control)
- [`Content-Language`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Language)
- [`Content-Length`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Length)
- [`Content-Type`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Type)
- [`Expires`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Expires)
- [`Last-Modified`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Last-Modified)
- [`Pragma`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Pragma)

如果想要让客户端可以访问到其他的首部信息，可以将它们在 `Access-Control-Expose-Headers` 里面列出来。

<!-- more -->

## [语法](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Access-Control-Expose-Headers#语法)

```
Access-Control-Expose-Headers: <header-name>, <header-name>, ...
```

## [指令](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Access-Control-Expose-Headers#指令)

- <header-name>

  包含0个或多个除 [simple response headers](https://developer.mozilla.org/zh-CN/docs/Glossary/Simple_response_header) （简单响应首部）之外的[首部名称](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers)列表，可以暴露给外部，供页面资源使用。

## [示例](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Access-Control-Expose-Headers#示例)

想要暴露一个非简单响应首部，可以这样指定：

```
Access-Control-Expose-Headers: Content-Length
```

想要额外暴露自定义的首部，例如 `X-Kuma-Revision`，可以指定多个，用逗号隔开：

```
Access-Control-Expose-Headers: Content-Length, X-Kuma-Revision
```

## 注意
当前端实现文件下载功能时，通过 fetch 函数无法获取请求头中的 `Content-Disposition` 参数时，需要看下后端是否通过 `Access-Control-Expose-Headers` 暴露了这个参数。

[Access-Control-Expose-Headers](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Access-Control-Expose-Headers)
[js无法获取响应header的Content-Disposition字段（2020）](https://blog.csdn.net/PGguoqi/article/details/106824957)
