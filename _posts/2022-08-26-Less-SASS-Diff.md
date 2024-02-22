---
layout:       post
title:        "Less 与 SASS 较为重要的区别整理"
subtitle:     "undefined"
date:         2022-08-26 19:05:43
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - FrontEnd
---


整理自 Less 文档的 Overview 部分与 SASS 文档的 Learn Sass 部分

Less特性 | Less | Sass | Sass特性
--------|------|-------|-----------
变量 | `@变量名` | 使用`$`符号来使某些东西成为变量 | 变量
Mixins | `.className()`（请注意，您也可以使用 `#ids` 作为混合。） | Mixins: `@mixin` + `@include`；<br/> 占位符类：`%类名` + `@extend %类名` | Mixins、扩展/继承
嵌套 | Less 使您能够使用嵌套来代替cascading或与cascading结合使用。您还可以使用此方法将伪选择器与您的 mixin 捆绑在一起。 | Sass可以让你嵌套CSS选择器 | 嵌套
运算 | 算术运算`+`、`-`、`*`、`/`可以对任何数字、颜色或变量进行运算 | 在 CSS 中进行数学运算非常有帮助 | 运算符
转义 | `~"anything"` 或 `~'anything'` 中的任何内容都按原样使用 | |
函数 | Less 提供了多种转换颜色、操作字符串和进行数学运算的函数。 | |
命名空间和访问器 | 有时，出于组织目的或只是为了提供一些封装，您可能希望对 mixin 进行分组 | |
映射 | 从 Less 3.5 开始，您还可以使用 mixins 和规则集作为值的映射 | |
作用域 | 变量和 mixin 首先局部地被查找，如果找不到，则从“父”作用域继承 | |
注释 | 块状注释和内联注释都可以使用 | |
导入 | `@import "path/to/file" //file.less` | 您可以使用 `@use` 规则随意拆分它（目前只有 Dart Sass 支持`@use`。其他实现的用户必须改用`@import`规则）。使用文件时，不需要包含文件扩展名 | 模块
 | | | 您可以创建 partial Sass 文件，其中含有可以包含在其他 Sass 文件中的CSS小片段 | Partials


---

***`//End of Article`***

---


![公众号二维码](https://neptliang.github.io/img/Article/WeChatBlog.png)