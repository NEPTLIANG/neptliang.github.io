---
layout:       post
title:        "React笔记"
subtitle:     "null"
date:         2020-08-01 23:24:56
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: true
tags:
    - FrontEnd
---


# 0x00 引入文件

* babel.min.js  _//解析JSX语法成纯JS语法的库（原作用：ES6转ES5)_
* react.development.js  _//React核心库_
* react-dom.development.js  _//操作DOM的React拓展库_

---


# 0x01 起步

## 1. 插入```script```标签

```type```为```text/babel```

```html
<script type="text/babel"></script>
```

## 2. 创建虚拟DOM元素对象

```jsx
var vDom = <h1>Hello React!</h1>
```

其中的h1没有双引号，**不是字符串**，是JSX的语法

## 3. 将虚拟DOM渲染到页面真实DOM容器中

```jsx
ReactDOM.render(vDom, document.getElementById('test'))
```

其中的```ReactDOM```来自**react-dom.development.js**

_//可使用Chrome的React开发者工具插件（React Developer Tools）调试_

_//未完待xu_