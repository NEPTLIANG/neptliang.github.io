---
layout:       post
title:        "复制了 MR 源分支上 commit 的哈希导致 cherry-pick 报 bad object"
subtitle:     "undefined"
date:         2023-05-31 11:24:56
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - FrontEnd
---


尝试通过 `git cherry-pick` 来 pick master 上的 commit 到 patch 分支的时候没有意识到 MR 源分支上的 commit hash 和目标分支的是不一样的，直接**复制了开发分支合并到 master 的 MR 里 commit 的哈希**，结果报 `bad object`。

网上搜了好几篇文章都说是因为没有把对应的 commit 拉到本地，**但是实际上我已经拉取了**。

于是摸索了半天，最后因为对 git 工作原理为数不多的一点点了解去翻了下 master 上对应的 commit 时候，终于发现了 commit 哈希的区别。

**改成 master 上的 commit 哈希**之后终于 pick 成功了，在此记录下。


---

***`//End of Article`***

---


![公众号二维码](https://neptliang.github.io/img/Article/WeChatBlog.png)