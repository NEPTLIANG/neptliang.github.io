---
layout:       post
title:        "设置 border-radius 后边框缺角问题处理"
subtitle:     "设置 border-radius 后边框被裁剪"
date:         2024-02-26 15:42:10
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - CSS
---


# `0x00` 背景

*`/*` 解决办法很简单，网上也有文章讲过，但是问题的描述方式比较多样，刚开始搜“border-radius 缺角”还搜不到，以至于一度放弃，过了会试了下搜“border-radius 被裁剪”才搜到一篇标题是“css 部分border被圆角切掉”的，里面的参考文献链接还不给复制，于是在此总结下 `*/`*


# `0x01` 解决办法

给这个元素设置

```CSS
overflow: auto;
```

就完事了


# `0xff` 参考文献

1. bit_tea@CSDN[《css 部分border被圆角切掉》（cnblogs.com/sanshi/p/9712426.html）](https://blog.csdn.net/weixin_38252066/article/details/105078015)（里面的答案来源链接是下条参考文献）

2. 三生石上@博客园[《有关CSS的overflow和border-radius的那些事，你的圆角被覆盖了吗？》（https://www.cnblogs.com/sanshi/p/9712426.html）](https://www.cnblogs.com/sanshi/p/9712426.html)


---

***`//End of Article`***

---


![公众号二维码](https://neptliang.github.io/img/Article/WeChatBlog.png)