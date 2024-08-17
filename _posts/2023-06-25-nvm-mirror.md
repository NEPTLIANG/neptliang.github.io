---
layout:       post
title:        "nvm Version '...' not found 处理"
subtitle:     "即 nvm 镜像修改"
date:         2023-06-25 15:36:54
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - FrontEnd
---


# 背景

今天在力扣做题时候本地（node v20.0.0）跑没问题但是提交报 `TypeError`：

```
TypeError: sorted.findLastIndex is not a function
```

十有八九是因为力扣 node 版本不支持 `findLastIndex`，根据力扣中文站首页-右下角问题反馈-左侧技术支援-技术问题中（藏得真深啊）更新于2021年09月03日的[《各语言对应版本和环境》（https://support.leetcode.cn/hc/kb/article/1194343/）](https://support.leetcode.cn/hc/kb/article/1194343/)可知，力扣目前用的估计是 14.8.0	，那自然高低得在本地整一个 `nvm alias`，

但是跑 `nvm install` 的时候报：

```
Version '14.8.0' not found - try `nvm ls-remote` to browse available versions.
```

然后 `nvm ls-remote` 时候返回的**只有 iojs 的版本**：

```sh
$ nvm ls-remote
    iojs-v1.0.0
    iojs-v1.0.1
    iojs-v1.0.2
    iojs-v1.0.3
    iojs-v1.0.4
    ...
```


# 处理过程

据大圣代@CSDN[《Mac & Linux下 nvm ls-remote只显示iojs》（https://blog.csdn.net/qq_23191031/article/details/59510801）](https://blog.csdn.net/qq_23191031/article/details/59510801)与一个人_a6ed@简书[《nvm 只有iojs列表解决办法》（https://www.jianshu.com/p/878edbeb5d87）](https://www.jianshu.com/p/878edbeb5d87)所述，

把 shell 全局变量 **`NVM_NODEJS_ORG_MIRROR` 设为镜像站**可解，

因为我这是 zsh 所以参考 ineo6@掘金[《NVM 快速安装教程》的配置-2. 设置node镜像（https://juejin.cn/post/7127249381455200287#heading-6）](https://juejin.cn/post/7127249381455200287#heading-6)与 USTC Mirror Help[《Node 源使用帮助》（https://mirrors.ustc.edu.cn/help/node.html#nvm-node-js）](https://mirrors.ustc.edu.cn/help/node.html#nvm-node-js)，

把

```sh
export NVM_NODEJS_ORG_MIRROR=https://mirrors.ustc.edu.cn/node/
```

加到 ~/.zshrc 就好了


# 参考文献

1. 力扣中文站[《各语言对应版本和环境》（https://support.leetcode.cn/hc/kb/article/1194343/）](https://support.leetcode.cn/hc/kb/article/1194343/)
2. 大圣代@CSDN[《Mac & Linux下 nvm ls-remote只显示iojs》（https://blog.csdn.net/qq_23191031/article/details/59510801）](https://blog.csdn.net/qq_23191031/article/details/59510801)
3. 一个人_a6ed@简书[《nvm 只有iojs列表解决办法》（https://www.jianshu.com/p/878edbeb5d87）](https://www.jianshu.com/p/878edbeb5d87)
4. ineo6@掘金[《NVM 快速安装教程》（https://juejin.cn/post/7127249381455200287#heading-6）](https://juejin.cn/post/7127249381455200287#heading-6)
5. USTC Mirror Help[《Node 源使用帮助》（https://mirrors.ustc.edu.cn/help/node.html#nvm-node-js）](https://mirrors.ustc.edu.cn/help/node.html#nvm-node-js)


---

***`//End of Article`***

---


![公众号二维码](https://neptliang.github.io/img/Article/WeChatBlog.png)