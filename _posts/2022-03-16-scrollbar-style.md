---
layout:       post
title:        "Chrome ::-webkit-scrollbar相关样式及不占用布局内部空间的配置"
subtitle:     "单纯设置::-webkit-scrollbar-thumb样式无效处理"
date:         2022-03-16 13:56:54
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - FrontEnd
---


# `0x00` 事故背景

遇到个修改滚动滑块和滚动条样式，且要求滚动条不占用布局内部空间（即布局内容要铺到滚动条轨道底下，也就是类似于滚动条轨道透明的样式）的需求，先给滚动滑块调了样式，但没有效果，谷歌了半天也没有找到原因，最后在[《scrollbar \| CSS-Tricks》（https://css-tricks.com/almanac/properties/s/scrollbar/）](https://css-tricks.com/almanac/properties/s/scrollbar/)找到解释，遂记录下


# `0x01` 单纯设置`::-webkit-scrollbar-thumb`样式无效的处理

刚开始只给`::-webkit-scrollbar-thumb`设置了`background: #00df7f`，没有效果

直到看到上述文章才明白，得**给滚动条设置个宽度或者高度等样式，滚动滑块样式才生效**

```css
.ele::-webkit-scrollbar {
    width: 4px;     /* 给滚动条设置个宽度 */
}
.ele::-webkit-scrollbar-thumb {
    background: #00df7f;
}
```

然后就有效果了


# `0x02` 实现滚动条不占用布局内部空间

需求是要求滚动条轨道透明，且布局内容铺到滚动条轨道底下，但是给`::-webkit-scrollbar-track`设置`background: transparent`也没有效果。其实滚动条设置了样式之后，轨道和滑块默认就都是透明的，但是布局的内容和滚动条轨道始终泾渭分明

原来正确操作应该是**给布局设置`overflow: overlay`**

```css
.ele {
    overflow: overlay;
}
```

就完事了

![设置 overflow: overlay 前的\<html/\>（还设置了 background: #777）](https://neptliang.github.io/img/Article/ScrollbarStyle/before.png)

![设置 overflow: overlay 后的\<html/\>](https://neptliang.github.io/img/Article/ScrollbarStyle/after.png)


# `0xff` 总结

```css
.ele {
    overflow: overlay;      /* 让滚动条不占用布局内部空间 */
}
.ele::-webkit-scrollbar {
    width: 4px;     /* 给滚动条设置了样式，滚动滑块样式才生效 */
}
.ele::-webkit-scrollbar-thumb {
    background: #00df7f;
}
.ele::-webkit-scrollbar-track {
    margin: 8px;    /* 滚动条轨道的margin和padding只对水平于滚动条方向上的内外边距有效 */
}
```


---

***`//End of Article`***

---


![公众号二维码](https://neptliang.github.io/img/Article/WeChatBlog.png)