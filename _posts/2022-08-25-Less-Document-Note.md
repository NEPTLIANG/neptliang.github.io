---
layout:       post
title:        "Less 文档 Overview 部分机翻整理"
subtitle:     "undefined"
date:         2022-08-25 15:34:56
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - FrontEnd
---


# 概述

## Less（代表 Leaner Style Sheets）是一个向后兼容的 CSS 语言扩展。这是 Less 语言和 Less.js 的官方文档，Less.js 是一种将您的 Less 样式转换为 CSS 样式的 JavaScript 工具。

因为 Less 看起来就像 CSS，所以学习它是轻而易举的事。Less 只是对 CSS 语言进行了一些方便的添加，这也是它可以如此快速地学习的原因之一。

* *有关 Less 语言功能的详细文档，请参阅[功能](https://lesscss.org/features/)*
* *有关 Less 内置函数的列表，请参阅[函数](https://lesscss.org/functions/)*
* *有关详细使用说明，请参阅[使用 Less.js](https://lesscss.org/usage/)*
* *对于 Less 的第三方工具，请参阅[工具](https://lesscss.org/tools/)*

Less 为 CSS 添加了什么？这是功能的快速概述。

# 变量

这些是不言自明的：

```Less
@width: 10px;
@height: @width + 10px;

#header {
  width: @width;
  height: @height;
}
```

输出：

```CSS
#header {
  width: 10px;
  height: 20px;
}
```

[了解有关变量的更多信息](https://lesscss.org/features/#variables-feature)

# Mixins

Mixins 是一种将一组属性从一个规则集中包含（“mixing in”）到另一个规则集中的方法。假设我们有以下类：

```Less
.bordered {
  border-top: dotted 1px black;
  border-bottom: solid 2px black;
}
```

我们想在其他规则集中使用这些属性。好吧，我们只需要输入我们想要属性的类名，如下所示：

```Less
#menu a {
  color: #111;
  .bordered();
}

.post a {
  color: red;
  .bordered();
}
```

`.bordered`类的属性现在将同时出现在`#menu a`和`.post a`中。（请注意，您也可以使用 `#ids` 作为混合。）

[了解有关 Mixin 的更多信息](https://lesscss.org/features/#mixins-feature)

# 嵌套

Less 使您能够使用嵌套来代替cascading或与cascading结合使用。假设我们有以下 CSS：

```CSS
#header {
  color: black;
}
#header .navigation {
  font-size: 12px;
}
#header .logo {
  width: 300px;
}
```

在 Less 中，我们也可以这样写：

```Less
#header {
  color: black;
  .navigation {
    font-size: 12px;
  }
  .logo {
    width: 300px;
  }
}
```

产生的代码更加简洁，并且模仿了你的 HTML 的结构。

您还可以使用此方法将伪选择器与您的 mixin 捆绑在一起。这是经典的 clearfix hack，重写为 mixin（`&`代表当前选择器父级）：

```Less
.clearfix {
  display: block;
  zoom: 1;

  &:after {
    content: " ";
    display: block;
    font-size: 0;
    height: 0;
    clear: both;
    visibility: hidden;
  }
}
```

[了解有关父选择器的更多信息](https://lesscss.org/features/#parent-selectors-feature)


# 嵌套规则和冒泡

`@media` 或 `@supports` 等规则可以像选择器一样嵌套。规则位于顶部，并且与同一规则集中的其他元素的相对顺序保持不变。这称为冒泡。

```Less
.component {
  width: 300px;
  @media (min-width: 768px) {
    width: 600px;
    @media  (min-resolution: 192dpi) {
      background-image: url(/img/retina2x.png);
    }
  }
  @media (min-width: 1280px) {
    width: 800px;
  }
}
```

输出：

```CSS
.component {
  width: 300px;
}
@media (min-width: 768px) {
  .component {
    width: 600px;
  }
}
@media (min-width: 768px) and (min-resolution: 192dpi) {
  .component {
    background-image: url(/img/retina2x.png);
  }
}
@media (min-width: 1280px) {
  .component {
    width: 800px;
  }
}
```


# 运算

算术运算`+`、`-`、`*`、`/`可以对任何数字、颜色或变量进行运算。如果可能，数学运算会考虑单位并在加、减或比较之前转换数字。结果具有最左侧明确说明的单位类型。如果转换不可能或没有意义，则忽略单位。不可能转换的示例：px 到 cm 或 rad 到 %。

```Less
// numbers are converted into the same units
@conversion-1: 5cm + 10mm; // result is 6cm
@conversion-2: 2 - 3cm - 5mm; // result is -1.5cm

// conversion is impossible
@incompatible-units: 2 + 5px - 3cm; // result is 4px

// example with variables
@base: 5%;
@filler: @base * 2; // result is 10%
@other: @base + @filler; // result is 15%
```

乘法和除法不转换数字。在大多数情况下它没有意义 - 长度乘以长度得到面积，而 css 不支持指定面积。Less 将按原样对数字进行运算，并将明确声明的单位类型分配给结果。

```Less
@base: 2cm * 3mm; // result is 6cm
```

您还可以对颜色进行算术运算：

```Less
@color: (#224488 / 2); // result is #112244
background-color: #112244 + #111; // result is #223355
```

但是，您可能会发现 Less 的[颜色函数](https://lesscss.org/functions/#color-operations)更有用。

从 4.0 开始，在括号之外使用`/`运算符不会执行除法。

```Less
@color: #222 / 2; // results in `#222 / 2`, not #111
background-color: (#FFFFFF / 16); //results is #101010
```

如果您想让它始终有效，您可以更改[数学](https://lesscss.org/usage/#less-options-math)设置，但不推荐这么做。

## calc() 异常

*[v3.0.0](https://github.com/less/less.js/blob/master/CHANGELOG.md)发布*

为了 CSS 兼容性，`calc()`不计算数学表达式，但将计算嵌套函数中的变量和数学。

```Less
@var: 50vh/2;
width: calc(50% + (@var - 20px));  // result is calc(50% + (25vh - 20px))
```


# 转义

转义允许您使用任意字符串作为属性或变量值。`~"anything"` 或 `~'anything'` 中的任何内容都按原样使用，除了[插值](https://lesscss.org/features/#variables-feature-variable-interpolation)外没有任何变化。

```Less
@min768: ~"(min-width: 768px)";
.element {
  @media @min768 {
    font-size: 1.2rem;
  }
}
```

结果是：

```CSS
@media (min-width: 768px) {
  .element {
    font-size: 1.2rem;
  }
}
```

请注意，从 Less 3.5 开始，您可以简单地编写：

```Less
@min768: (min-width: 768px);
.element {
  @media @min768 {
    font-size: 1.2rem;
  }
}
```

在 3.5 之后，许多以前需要“引号转义”的情况都不需要了。


# 函数

Less 提供了多种转换颜色、操作字符串和进行数学运算的函数。它们在[函数参考](https://lesscss.org/functions/)中有完整的记录。

使用它们非常简单。以下示例使用percentage将 0.5 转换为 50%，将base颜色的饱和度增加 5%，然后将背景颜色设置为淡化 25% 并旋转 8 度的颜色：

```Less
@base: #f04615;
@width: 0.5;

.class {
  width: percentage(@width); // returns `50%`
  color: saturate(@base, 5%);
  background-color: spin(lighten(@base, 25%), 8);
}
```

[请参阅：函数参考](https://lesscss.org/functions/)


# 命名空间和访问器

（不要与[CSS `@namespace`](http://www.w3.org/TR/css3-namespace/)或[namespace选择器](http://www.w3.org/TR/css3-selectors/#typenmsp)混淆）。

有时，出于组织目的或只是为了提供一些封装，您可能希望对 mixin 进行分组。你可以在 Less 中非常直观地做到这一点。假设您想在 `#bundle` 下捆绑一些 mixins 和变量，以供以后重用或分发：

```Less
#bundle() {
  .button {
    display: block;
    border: 1px solid black;
    background-color: grey;
    &:hover {
      background-color: white;
    }
  }
  .tab { ... }
  .citation { ... }
}
```

现在，如果我们想在我们的 `#header a` 中混合 `.button` 类，我们可以这样做：

```Less
#header a {
  color: orange;
  #bundle.button();  // can also be written as #bundle > .button
}
```

注意：如果您不希望它出现在您的 CSS 输出（即 `#bundle .tab`）中，请将 `()` 附加到您的namespace（例如 `#bundle()`）。


# 映射

从 Less 3.5 开始，您还可以使用 mixins 和规则集作为值的映射。

```Less
#colors() {
  primary: blue;
  secondary: green;
}

.button {
  color: #colors[primary];
  border: 1px solid #colors[secondary];
}
```

正如预期的那样输出：

```CSS
.button {
  color: blue;
  border: 1px solid green;
}
```

[另请参阅：映射](https://lesscss.org/features/#maps-feature)


# 作用域

Less 中的作用域与 CSS 中的作用域非常相似。变量和 mixin 首先局部地被查找，如果找不到，则从“父”作用域继承。

```Less
@var: red;

#page {
  @var: white;
  #header {
    color: @var; // white
  }
}
```

与 CSS 自定义属性一样，mixin 和变量定义不必放在引用它们的行之前。所以下面的 Less 代码和前面的例子是一样的：

```Less
@var: red;

#page {
  #header {
    color: @var; // white
  }
  @var: white;
}
```

[另请参阅：延迟加载](https://lesscss.org/features/#variables-feature-lazy-loading)


# 注释

块状注释和内联注释都可以使用：

```Less
/* One heck of a block
 * style comment! */
/* 一个块
 * 状注释！ */
@var: red;

// Get in line!
// 得到行内注释！
@var: white;
```


# 导入

导入几乎可以按预期工作。您可以导入一个`.less`文件，其中的所有变量都将可用。为`.less`文件指定扩展名是可选的。

```Less
@import "library"; // library.less
@import "typo.css";
```

[了解有关导入的更多信息](https://lesscss.org/features/#imports-feature)


---

***`//End of Article`***

---


![公众号二维码](https://neptliang.github.io/img/Article/WeChatBlog.png)