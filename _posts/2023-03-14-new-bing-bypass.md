---
layout:       post
title:        "New Bing 总是跳转到国内特供版临时解决办法：删除 _EDGE_S Cookie 键值对"
subtitle:     "undefined"
date:         2023-03-14 14:14:56
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - Tools
---


# `0x00` 背景

New Bing 看起来应该是个挺 NB 的生产力工具，老夫在群友的分享下进行了申请并获得了通过。（感谢联想 S899t 交流群的大佬们）

但是很坑的一个问题是必应总是会跳转回国内特供版，估计是因为某种不可抗力，但是运用了某种上网技巧之后却仍然经常会这样。

网上的建议是清除 Cookie，但是每次清除完都要重新登录，实在是烦不胜烦。

于是经过多次尝试，发现只需**删除 `_EDGE_S` Cookie 键值对**即可在运用上网技巧之时避免跳转回国内特供版，故在此记录下​


# `0x01` 操作过程

1. Edge 打开 [edge://settings/cookies/detail?site=bing.com](edge://settings/cookies/detail?site=bing.com) 或手动打开设置-Cookie 和网站权限-管理和删除 cookie 和站点数据-管理和删除 cookie 和站点数据-bing.com；

2. 点击 `_EDGE_S` 右方的删除即可


# `0x02` 存在的问题

1. 仍然需要上网技巧，一旦哪天没开就进了必应，该 cookie 就又会被标记，直到再次删除该键值对；

2. 由于该 cookie 被设置了 `secure: true`，故删除操作不好自动化。

    在必应页面控制台执行

    ```JS
    [...await cookieStore.getAll()].find(c => c.name = '_EDGE_S')
    ```

    可以看出，这个 cookie 被设置了 `secure: true`，尝试使用

    ```JS
    await cookieStore.delete('_EDGE_S')
    ```

    删除虽然没报错，但是再次查看会发现这个 cookie 仍然存在，页面也仍然会跳转回国内特供版，所以似乎不好通过脚本自动删除该 cookie。


# `0x03` 后话

虽然也还是有点麻烦，不过我的主力浏览器是 Chrome，所以 Edge 常年开着这个设置页面，时不时切过去点一下用着也还行。

还有个想说的就是申请 New Bing 的时候不知道为什么登录了工作邮箱（也是 Outlook），很轻松就申请上并在两三天后通过了，后来发现了这个问题想改回主用邮箱申请，却怎么也申请不上，点申请始终提示“出错了。请重试。”，退出 Rewards 之类的操作尝试过也没有效果。。。


---

***`//End of Article`***

---


![公众号二维码](https://neptliang.github.io/img/Article/WeChatBlog.png)