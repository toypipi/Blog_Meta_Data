---
title: webpack-dev-server 常用配置参数
categories:
  - 技术
tags:
  - webpack 
  - 参数
date: 2023-04-06 14:51:35
---

---

Webpack Dev Server的一些常用配置参数：

host：指定服务器的主机名，默认为 localhost。

port：指定服务器运行的端口号，默认为 8080。

contentBase/publicPath：指定静态文件的路径。contentBase参数用于指定提供文件的根目录，而publicPath则用于指定浏览器请求资源的基础路径。默认情况下，两个参数都指向项目根目录。

hot：启用热模块替换（Hot Module Replacement），允许在不刷新整个页面的情况下更新模块。值为true或false。

historyApiFallback：启用 HTML5 的历史记录 API ，解决使用单页应用时路由跳转404问题。值为true或false。
<!-- more -->
proxy：设置代理服务器。可以将客户端请求代理到后端API服务器以避免跨域问题。

compress：启用gzip压缩来减小文件传输大小。值为true或false。

watchOptions：配置监视文件变化的选项，例如轮询间隔、忽略某些文件等。

clientLogLevel：设置日志级别。可选的值有none/error/warning/info。

open：启动dev server时是否自动打开浏览器。值为true或false。

可以在webpack配置文件中通过devServer对象进行设置。