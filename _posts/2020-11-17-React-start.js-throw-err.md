---
layout:       post
title:        "React项目npm start报错node_modules/react-scripts/scripts/start.js:19 throw err"
subtitle:     "null"
date:         2020-11-17 02:35:43
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: true
tags:
    - FrontEnd
---

今天写React的时候，写着写着突然`npm start`报错退出，报错信息大概如下（没有保留完整）：

```
node_modules/react-scripts/scripts/start.js:19 throw err; ^ [Error: ENOENT: no...
```

```
npm ERR! code ELIFECYCLE
npm ERR! errno 1
npm ERR! client@0.1.0 start: `react-scripts start`
npm ERR! Exit status 1
npm ERR! 
npm ERR! Failed at the client@0.1.0 start script.
npm ERR! This is probably not a problem with npm. There is likely additional logging output above.
 
npm ERR! A complete log of this run can be found in:
npm ERR!     /Users/liuzhao/.npm/_logs/2019-07-25T04_02_03_249Z-debug.log
```

必应了好久，只找到个
> `npm install -g npm@latest` to update npm because it is sometimes buggy.  
> `rm -rf node_modules` to remove the existing modules.  
> `npm install` to re-install the project dependencies.

试了没用

于是只好看代码有没有问题，看着看着发现有个jsx中的import自定义组件语句中的路径错了
```jsx
import Verify from 'verify/verify'
```

路径开头漏了`../`，加上后npm start就没事了


*//End of Article*

![公众号二维码](https://neptliang.github.io/img/Article/WeChatBlog.png)