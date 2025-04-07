---
title: package.json dependencies 指定项目依赖
categories:
  - 技术
tags:
  - package.json
  - dependencies
  - 项目依赖
date: 2022-03-04 14:22:01
---

---

## dependencies

指定项目依赖，值为包名加上版本号。

版本号可以指定一个范围，有以下语法。

- `version`：精确的版本，例如 `1.0.0`
- `>version`：大于 `version`
- `>=version`：大于等于
- `<version`: 小于
- `<=version`: 小于等于
- `~version`: 允许补丁级别的变化，例如 `~1.2.3` 表示 `>=1.2.3 <1.3.0`
- `^version`：允许不更改最左侧非0版本号数字的变化，例如 `^1.2.3` 表示 `>=1.2.3 <2.0.0`
- `1.2.x`
- `http://...`
- `*`
- `version1 - version2` 等同于 `>=version1 <=version2`

<!-- more -->
[参考文档](https://docs.npmjs.com/cli/v6/configuring-npm/package-json)
