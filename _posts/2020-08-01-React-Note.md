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

```js
ReactDOM.render(vDom, document.getElementById('test'))
```

其中的```ReactDOM```来自**react-dom.development.js**

_//可使用Chrome的React开发者工具插件（React Developer Tools）调试_

---


# 0x02 JSX理解与基本使用

## 创建虚拟DOM元素

1. React提供了一些API来创建一种特别的JS对象
    
    * 纯JS（一般不用）
        ```js
        React.createElement('h1', {id: 'myTitle'}, title)
        ```
        上面创建的就是一个简单的虚拟DOM对象
    * JSX:
        ```jsx
        <h1 id='myTitle'>{title}</h1>
        ```
2. 虚拟DOM对象最终都会被React**转换为真实的DOM**
3. 开发时只需要操作React的虚拟DOM相关数据，React会转换为真实DOM变化而更新界面

## 渲染虚拟DOM（元素）

1. 语法：
    ```js
    ReactDOM.render(virtualDOM, containerDOM)
    ```
2. 作用：将虚拟DOM元素渲染到页面中的真实容器DOM中显示
3. 参数说明
    * 参数1：纯JS或JSX创建的**虚拟DOM对象**
    * 参数2：用来包含虚拟DOM元素的**真实DOM元素对象**（一般是一个div）

## 虚拟DOM优势

1. 比真实的DOM“轻”
2. 更新不重绘页面，渲染时才重绘

## JSX简介

1. 全称：**JavaScript XML**
2. React定义的一种类似于XML的JS拓展语法：XML+JS
3. 作用：用来创建React虚拟DOM（元素）对象
    ```jsx
    var ele = <h1>Hello JSX!</h1>
    ```
    它不是字符串，也不是HTML/XML标签；  
    最终产生一个**JS对象**
4. 标签名任意：HTML标签或其他标签
5. 标签属性任意：HTML标签属性或其他
6. 基本语法规则
    * 遇到```<```开头的代码，以**标签语法**解析：  
        HTML同名标签转换为HTML同名元素，  
        其他标签需要特别解析
    * 遇到以```{```开头的代码，以**JS语法**解析：  
        标签中的JS代码必须用{}包含
7. bable.js的作用
    * 浏览器不能直接解析JSX代码，需要babel转译为纯JS才能运行
    * 只要用了JSX，都要加上```type="text/babel"```，声明需要babel来处理

## JSX基本使用

1. 创建虚拟DOM

    **JS语法**（本质）：
    ```js
    const msg = 'I Like You!'
    const myId = 'Atguigu'

    const vDom1 = React.createElement('h2', {id: myId.toLowerCase()}, msg.toUpperCase())  //参数：标签类型，标签属性：id=myId的小写， 标签内容
    ```
    _//这里是JS语法而非babel语法，故script标签的type为js_

    **babel语法**（babel会转换成JS写法）：
    ```jsx
    const vDom2 = <h3 id={myId.toUpperCase()}>{msg.toLowerCase()}</h3>
    ```
    *//script标签的type为babel*

2. 渲染虚拟DOM
    ```js
    ReactDom.render(vDom1, document.getElementById('test1'))  //参数：要渲染的虚拟DOM对象，要渲染到的标签
    ```

*//在js中写```debugger```相当于在Chrome中加断点*

---


# 0x03 后记

发现用Markdown写这个笔记太费时间了，还是改用有道云笔记吧


_//End of Article_