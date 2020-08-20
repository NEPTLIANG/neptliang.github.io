---
layout:       post
title:        "CSS+JS实现一个轮盘时钟"
subtitle:     "null"
date:         2020-08-09 02:36:54
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: true
tags:
    - FrontEnd
---


# 0x00 前言

/* 之前学校计算机社团招新前叫我收集理事们的技术作品来展示，结果只有一位理事交了份作品，我又刚好在网上看到一个安卓的什么“抖X网红时钟动态壁纸”，就试着用CSS和JS整了一个类似的交了上去。所幸最后另一位理事交了第二份作品，用不上我这个 

![截图](https://neptliang.github.io/img/Article/RoteteClock/RoteteClock.png)

该玩意纯为本菜鸡练习之作，请各路巨佬勿嘲 

完整源码：[github.com/NEPTLIANG/Web/tree/master/RotateClock/RotateClock\_v1.0](https://github.com/NEPTLIANG/Web/tree/master/RotateClock/RotateClock_v1.0)（v2.0~v4.0都是待修改的半成品） */

---


# 0x01 HTML搭个框

**先大致整几个```div```**，文字之类的一条条写的话比较繁琐，故用JS生成：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <link href="RotateClock.css" rel="stylesheet"/>  <!--引入样式-->
</head>
<body>
<div class="father">  <!--父布局，用来调整整体位置-->
    <div id="time" class="time"></div>  <!--中间的时间-->
    <div id="date" class="date"></div>  <!--中间的日期-->
    <div id="wheelOfSeconds" class="wheel"></div>  <!--显示秒的轮-->
    <div id="wheelOfMinutes" class="wheel"></div>  <!--显示分钟的轮-->
    <div id="wheelOfHours" class="wheel"></div>  <!--显示小时的轮-->
</div>
<script src="RotateClock.js"></script>  <!--引入JS-->
</body>
</html>
```

![框](https://neptliang.github.io/img/Article/RoteteClock/html.png)

---


# 0x02 样式整上

设置一下**背景字体**什么的：

```css
body {
    background: #000;
    color: #00ffff;
    text-shadow: rgba(0, 255, 255, 50) 0 0 1em;
    font-size: 32px;
}
```

![背景字体](https://neptliang.github.io/img/Article/RoteteClock/css.png)

给父div整个**绝对定位**：

```css
.father {
    position: absolute;
}
```

因为显示秒的轮的旋转基点（圆心）设置在x轴的-1024px，所以**旋转半径**为1024px，把轮盘**右移**1024px，以便让定位锚点从右边第一行字的左上角移到圆心，设置每60秒**旋转**一周：

```css
#wheelOfSeconds {
    left: 1024px;  /*右移*/
    position: absolute;
    display: inline;
    transform-origin: -1024px;  /*设置圆心*/
    animation: rotate 60s infinite linear;  /*应用后面设置的旋转关键帧rotate*/
}
```

显示分钟的轮的旋转半径为768px，把轮盘左移1024px，每3600秒旋转一周：

```css
#wheelOfMinutes {
    left: 768px;  /*右移*/
    position: absolute;
    display: inline;
    transform-origin: -768px;  /*设置圆心*/
    animation: rotate 3600s infinite linear;  /*旋转*/
}
```

显示小时的轮的旋转半径为512px，每24小时旋转一周：

```css
#wheelOfHours {
    left: 512px;  /*右移*/
    position: absolute;
    display: inline;
    transform-origin: -512px;  /*设置圆心*/
    animation: rotate 86400s infinite linear;  /*旋转*/
}
```

设置轮盘中间的时间和日期样式：

```css
#time {
    font-size: 128px;
    position: relative;
    left: -50%;
    top: 128px;
}

#date {
    font-size: 48px;
    position: relative;
    left: -50%;
    top: 128px;
    text-align: center;
}
```

设置轮盘上文字的样式：

```css
.seconds {  /*显示秒的轮盘上的文字*/
    transform-origin: -1024px;  /*旋转半径1024px*/
    position: fixed;
    width: 256px;
}

.minutes {  /*显示分钟的轮盘上的文字*/
    transform-origin: -768px;  /*旋转半径768px*/
    position: fixed;
    width: 256px;
}

.hours {  /*显示小时的轮盘上的文字*/
    transform-origin: -512px;
    position: fixed;
    width: 256px;
}
```

设置旋转**关键帧**rotate为逆时针旋转360°：

```css
@keyframes rotate {
    100% {
        transform: rotate(-360deg);
    }
}
```

---


# 0x03 完成JS

先是两个后面用到的参数，由于调整起来效果不好，就不详述了，保持默认即可，或者可以自己尝试调整一下：

```js
var rotate = 0;  //设置轮盘3D旋转角度
var left = -50 + "%";  //设置中间日期位置偏移，默认-50%
```

实现父div**居中**：

```js
var height = window.innerHeight;
var father = document.getElementsByClassName("father");
father[0].style.left = "50%";
father[0].style.top = (height / 2 - 204).toString() + "px";
```

![居中](https://neptliang.github.io/img/Article/RoteteClock/center.png)

**生成轮盘**，轮盘上的文字一条条写太麻烦了，所以用JS生成一下（虽然也方便不了多少，但好歹不用一条条写）。先做一些准备工作：

```js
var num = ["零", "一", "二", "三", "四", "五", "六", "七", "八", "九", "十", "", "", "", "", "", "", ""];
var num2 = ["十", "十一", "十二", "十三", "十四", "十五", "十六", "十七", "十八", "十九", "", "", "",];
var wheelOfSeconds = document.getElementById("wheelOfSeconds");
var wheelOfMinutes = document.getElementById("wheelOfMinutes");
var wheelOfHours = document.getElementById("wheelOfHours");
var hours = [], minutes = [], seconds = [];
var i;
var initTime = new Date();  //获取当前日期时间，作为轮盘最右边的初始时间
var initSecond = initTime.getSeconds();
var initMinute = initTime.getMinutes() + initSecond / 60;
var initHour = initTime.getHours() + initMinute / 60;
```

然后**拼接秒和分钟文字**并插到轮盘div里

```js
for (i = 0; i < 60; i++) {
    seconds.push(document.createElement("div"));
    minutes.push(document.createElement("div"));
    if (i === 0) {
        seconds[i].innerHTML = "整";
        minutes[i].innerHTML = "零分";
    }
    if (i > 0 && i <= 10) {
        seconds[i].innerHTML = "零" + num[i] + "秒";
        minutes[i].innerHTML = "零" + num[i] + "分";
    }
    if (i >= 10 && i < 20) {
        seconds[i].innerHTML = num2[i % 10] + "秒";
        minutes[i].innerHTML = num2[i % 10] + "分";
    }
    if (i >= 20 && i < 60) {
        seconds[i].innerHTML = num[Math.floor(i / 10)] + num2[i % 10] + "秒";
        minutes[i].innerHTML = num[Math.floor(i / 10)] + num2[i % 10] + "分";
    }
    seconds[i].className = "seconds";  //设置秒的文字样式
    minutes[i].className = "minutes";  //设置分钟文字样式
    seconds[i].style = "transform: rotate(" + 6 * (i - initSecond) + "deg);";  //设置秒的文字旋转角度，组成轮盘
    minutes[i].style = "transform: rotate(" + 6 * (i - initMinute) + "deg);";  //设置分钟文字旋转角度，组成轮盘
    wheelOfSeconds.appendChild(seconds[i]);
    wheelOfMinutes.appendChild(minutes[i]);
}
```

![分钟和秒](https://neptliang.github.io/img/Article/RoteteClock/minAndSec.png)

再拼接小时文字并插到轮盘div里

```js
for (i = 0; i < 24; i++) {
    hours.push(document.createElement("div"));
    if (i === 0) {
        hours[i].innerHTML = "零时";
    }
    if (i > 0 && i <= 10) {
        hours[i].innerHTML = num[i] + "时";
    }
    if (i >= 10 && i < 20) {
        hours[i].innerHTML = num2[i % 10] + "时";
    }
    if (i >= 20 && i < 24) {
        hours[i].innerHTML = num[Math.floor(i / 10)] + num2[i % 10] + "时";
    }
    hours[i].className = "hours";  //设置小时文字样式
    hours[i].style = "transform: rotate(" + 15 * (i - initHour) + "deg);";  //设置小时文字旋转角度，组成轮盘
    wheelOfHours.appendChild(hours[i]);
}
```

![小时](https://neptliang.github.io/img/Article/RoteteClock/hour.png)

整一个函数来**更新时间文字样式**：

```js
var date, year, month, day, hour, minute, second;
var time = document.getElementById("time");
var dateDiv = document.getElementById("date");
function changeStyle() {
    date = new Date();  //获取当前时间
    year = date.getFullYear();
    month = date.getMonth() + 1;
    day = date.getDate();
    hour = date.getHours();
    minute = date.getMinutes();
    second = date.getSeconds();
    time.innerHTML = hour.toString() + ":" + minute.toString();
    dateDiv.innerHTML = year + "." + month.toString() + "." + day.toString();  //设置轮盘中间的日期
    hours[hour].style.color = "#fff";  //设置轮盘上当前小时的文字样式
    hours[hour].style.textShadow = "rgba(255, 255, 255, 50) 0 0 1em";
    hours[(hour + 24 - 1) % 24].style.color = "#0ff";  //恢复上一小时的文字样式
    hours[(hour + 24 - 1) % 24].style.color = "rgba(0, 255, 255, 50) 0 0 1em";
    minutes[minute].style.color = "#fff";  //设置轮盘上当前分钟的文字样式
    minutes[minute].style.textShadow = "rgba(255, 255, 255, 100) 0 0 1em";
    minutes[(minute + 60 - 1) % 60].style.color = "#0ff";  //恢复上一分钟的文字样式
    minutes[(minute + 60 - 1) % 60].style.color = "rgba(0, 255, 255, 50) 0 0 1em";
    seconds[second].style.color = "#fff";  //设置轮盘上当前秒的文字样式
    seconds[second].style.textShadow = "rgba(255, 255, 255, 50) 0 0 1em";
    seconds[(second + 60 - 1) % 60].style.color = "#0ff";  //恢复上一秒的文字样式
    seconds[(second + 60 - 1) % 60].style.color = "rgba(0, 255, 255, 50) 0 0 1em";
}
```

设置轮盘3D旋转，这一小段可有可无。就是这里用到了JS部分开头那两个参数，由于实现起来效果不好，所以旋转角度默认设成0：

```js
father[0].style.transform = `rotate3d(-1, 1, 0, ${rotate}deg)`;
time.style.transform = `rotate3d(1, 1, 0, ${rotate}deg)`;
dateDiv.style.transform = `rotate3d(1, 1, 0, ${rotate}deg)`;
dateDiv.style.left = left;
```

最后设置每0.1秒**更新**一次轮盘上的文字样式：

```js
setInterval(changeStyle, 100);
```

![更新](https://neptliang.github.io/img/Article/RoteteClock/update.png)

就完事了

*//End of article*

**参考文献**：

* [@二娃_ 《【自定义View】抖音网红文字时钟-上篇》（juejin.im/post/6844903824503603214）](https://juejin.im/post/6844903824503603214)

![公众号二维码](https://neptliang.github.io/img/Article/WeChatBlog.png)