---
layout:       post
title:        "拼写错误导致的Webpack构建报错Error: Cannot find module '@bable/babel-preset-preset-env'"
subtitle:     "小丑竟是我自己"
date:         2021-08-22 00:15:43
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - FrontEnd
---


# 事故背景

参考文档和教程写Webpack demo的时候，写了好多才开始尝试构建，然后报了一堆错

解决掉好几个之后，有一个报错卡了我好几天：

```
Module build failed (from ./node_modules/babel-loader/lib/index.js): 
Error: Cannot find module '@bable/babel-preset-preset-env'
```

当时写的`babel-loader`配置如下：

```js
{
    test: /\.js$/,
    exclude: /node_modules/, // node_modules目录而非node_module
    include: resolve(__dirname, 'src'), // 对最少数量的必要模块使用 loader，使用 include 字段仅将 loader 应用在实际需要将其转换的模块所处路径
    use: {
        loader: 'babel-loader',
        options: {
            presets: [ // 预设：指示babel做什么样的兼容性处理
                [
                    '@bable/preset-env',    //千万别拼错了（落泪
                    {
                        useBuiltIns: 'usage', // 按需加载
                        corejs: {
                            version: 3, // 指定core-js版本
                        },
                        targets: { // 指定兼容性做到哪个版本浏览器
                            chrome: '60',
                            firefox: '60',
                            ie: '9',
                            safari: '10',
                            edge: '17',
                        },
                    },
                ],
            ],
        },
    },
}
```


# 曲折历程

把`@bable/preset-env`改成`preset-env`就变成：

```
Error: Cannot find module 'babel-preset-preset-env'
```

再改成`@bable/env`就变成：

```
Error: Cannot find module '@bable/babel-preset-env'
```

再改成`env`（好像就是旧版写法）就变成：

```sh
Error: Cannot find module 'babel-preset-env'  	#这好像就是旧版包名
```

看着像是自动在`@babel/`后面拼接了`babel-preset-`

于是bing搜了好久，还跟着报错信息翻了半天`@babel/core`源码（可惜并没有看懂）

还翻了`babel-loader`文档，却发现官方“就是这么写的”

折腾了好几天终于开窍，干脆把自己写的注释掉，并把官方的示例拷进来，然后逐行替换并构建


# 排查结果

最后发现竟是因为**拼写错误**，`use.presets`中把`@babel`写成了`@bable`，Babel找不到，就按旧版本操作拼接了个`babel-preset-`

枯了

---

***`//End of Article`***

---


![公众号二维码](https://neptliang.github.io/img/Article/WeChatBlog.png)