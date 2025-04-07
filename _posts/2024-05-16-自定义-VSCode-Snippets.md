---
title: 自定义 VSCode Snippets
categories:
  - 技术
tags:
  - VSCode Snippets
  - markdown
date: 2024-05-16 14:37:46
---

---

我的博客使用 Hexo 生成，博文里面的图片地址是相对地址本地引用，使用标签插件的语法引入，如下：

```
# 本地图片资源，限制图片尺寸
{% asset_img image.jpg 200 400 %}
```

每次输入有点麻烦，可以借助 VSCode Snippets 实现更加快捷的输入。

具体使用方法如下：

1. 打开 VSCode，点击`File`菜单，选择`Preferences`，然后点击`Configure User Snippets`。
2. 输入`Markdown`，点击回车。
3. 在弹出的窗口中，添加一个新的 snippets，如下：
<!-- more -->

````json
  "img": {
    "prefix": "img",
    "body": ["{% asset_img ${0} %}"],
    "description": "img"
  },
  "code": {
	  "prefix": "code",  // 触发词
	  "body": [  // 补全内容
	    "```js",
	    "$1",  // 光标停留位置
	     "```"
	  ],
	  "description": "Add js code block"  // 注释
  }
````

4. 打开 VSCode，点击 `File` 菜单，选择 `Preferences`，然后点击 `Settings`，点击右上角分页左边的按钮 `Open Settings(JSON)`，输入如下文字：

```json
  "[markdown]": {
    "editor.quickSuggestions": true
  }
```

5. 保存，然后在博文里面输入`img`，即可自动补全。

# 参考文档

[Snippets in Visual Studio Code](https://code.visualstudio.com/docs/editor/userdefinedsnippets#_create-your-own-snippets)
[snippet-generator](https://snippet-generator.app/?description=&tabtrigger=&snippet=&mode=vscode)
[VSCode 设置 Markdown 自定义补全](https://juejin.cn/post/6844904089680085006)
