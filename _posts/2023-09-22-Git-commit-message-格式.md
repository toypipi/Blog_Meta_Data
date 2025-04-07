---
title: Git commit message 格式
categories:
  - 技术
tags:
  - git
  - commit
  - 格式
date: 2023-09-22 13:31:27
---

---

# 提交规范

格式: `type (scope): subject`

<!-- more -->

```js
- type
  - 用于说明 `commit` 的类别，只允许使用下面10个标识。
    - feat：新功能（feature）
    - fix：修补bug
    - docs：文档（documentation）
    - style： 格式（不影响代码运行的变动）
    - refactor：重构（即不是新增功能，也不是修改bug的代码变动）
    - perf：性能优化
    - test：增加测试
    - chore：构建过程或辅助工具的变动
    - revert：回退
    - build：打包
- scope(可选)
  - 用于说明 `commit` 影响的范围，比如Button组件、store、首页、路由等等，视项目不同而不同。
- subject(可选))
  - 是 `commit` 目的的简短描述，不超过50个字符。
    - 以动词开头，使用第一人称现在时，比如change，而不是changed或changes
    - 第一个字母小写
    - 结尾不加句号（.）
```

# Reference

[JDC 前端代码规范](https://jdf2e.github.io/jdc_fe_guide/docs/git/commit)
[约定式提交](https://www.conventionalcommits.org/zh-hans/v1.0.0/#%e7%ba%a6%e5%ae%9a%e5%bc%8f%e6%8f%90%e4%ba%a4%e8%a7%84%e8%8c%83)
[自动生成变更日志及规范 git 提交记录(release-it+husky+ commitizen+ commitlint)](https://juejin.cn/post/7114673069293043719)
[语义化版本 2.0.0](https://semver.org/lang/zh-CN/)
1697080977000
