---
layout:       post
title:        "珠峰培训JS高级课笔记"
subtitle:     "undefined"
date:         2022-11-13 22:00:00
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - FrontEnd
---


# 图片懒加载

## 概念

其他资源加载完成再加载图片

* **已经出现**在可视窗口中的图片需要**进行加载**
* 暂时**没有出现**在可视窗口中的图片应该暂时**先不加载**

随着页面的滚动，当图片所在盒子元素出现在可视窗口（一露头/出现一半/完全出现），再去加载图片。

## 原理

* 加载页面时，`img` 的 **`src` 不赋值**（这样就不会加载图片），**把图片的地址赋值给 `img` 的自定义属性**（方便后期想要加载图片时获取）
* 如果 `src` 不赋值，或者加载图片出错，浏览器会显示“碎图”，样式不美观，所以一开始可以**隐藏 `img`**（可以设置 `display`，也可以设置透明度为0（透明度变化可以设置过渡效果））
* 给图片所在的盒子设置背景**占位图**（或者背景颜色），在图片加载之前，用其占位。（盒子的宽高需事先设置好）

    例：

    ```CSS
    .pic-box {
        width: 600px;
        height: 400px;
        background: #ddd;
    }
    .pic-box img {
        opacity: 0;
        transition: opacity .3s;
    }
    ```

什么时候加载？

* **页面第一次渲染完之后**（即其他资源加载完成之后。例如：`window.onload`）
* 加载**出现在当前可视窗口内**的图片

如何加载？

1. **获取图片的前述自定义属性值**，拿到图片地址
2. 把地址**赋给图片的 `src`，如果图片可以正常加载成功，则让 `img` 显示**

### 传统方案

* 优势：兼容性好
* 劣势：实现麻烦

#### 思路

页面滚动中进行监听：`window.onscroll`

1. 设 `picBox` 为**图片所在的元素** pic-box：

    ```JS
    const picBox = document.querySelector(selector);
    ```

2. 设 `A` 为 **pic-box 顶部**到**可视窗口顶部**的距离：

    ```JS
    const A = picBox.getBoundingClientRect().top;
    ```

3. 设 `B` 为 **pic-box 底部**到**可视窗口顶部**的距离：

    ```JS
    const B = pciBox.getBoundingClientRect().bottom;
    ```

4. 设 `C` 为**可视窗口的高度**：

    ```JS
    const C = document.documentElement.clientHeight;
    ```

5. 判断是否是时候加载

    * 元素在可视窗口**完全出现**才加载：`B<=C && A>=0`

        ![图片懒加载-完全显示情况示意图](图片懒加载-完全)

    * 元素在可视窗口还没完全出现就加载：
        * **一露头**就加载：`A<=C && B>0`
        * **出现一半**时加载：类似上条，其中 `A` 和 `B` 都加元素盒子一半的高度

        ![图片懒加载-部分显示情况示意图](图片懒加载-部分)

#### 实现

##### JS 部分

```JS
const imgElement = document.getElementById('img');
const imgContainer = document.getElementById('container');

/**
 * 图片懒加载
 * @returns {void}
 */
const lazy = () => {
    if (!(imgElement instanceof HTMLImageElement)) { return; }
    const realSrc = imgElement.getAttribute('real-src') || '';
    // 1. 传统方法：直接对原 img 元素进行加载，无法处理图片加载失败的情况
    // imgElement.src = realSrc;
    // imgElement.onload = () => {
    //     // 图片加载成功
    //     imgElement.style.opacity = '1';
    // };
    // 2. 改进方法：确保图片地址是正确的情况下，再给页面中的IMG元素赋值，防止因图片加载失败出现“裂图”
    const img = new Image();
    img.src = realSrc;
    img.onload = () => {
        // 图片加载成功
        imgElement.src = realSrc;
        imgElement.style.opacity = '1';
    }
    // 设置自定义属性：标记当前图片已经处理过延迟加载
    imgContainer.loaded = true;
};

/**
 * 计算是否加载（完全出现再加载）
 * @returns {void}
 */
const compute = () => {
    // 如果图片已经加载过，则不再进行以下处理
    if (
        imgContainer.loaded
            || !(imgContainer instanceof HTMLElement)
    ) { return; }
    // console.log('Computing');   //test: 如果不判断图片是否已经加载，则每次滚动仍都会执行以下操作
    const C = document.documentElement.clientHeight;
    const {
        top: B,
        bottom: A,
    } = imgContainer?.getBoundingClientRect();
    if (A <= C && B >= 0) {
        lazy();
    }
}

// 页面其他资源加载完后计算一次 & 页面滚动过程中随时计算
/**
 * * scroll事件会在浏览器滚动条滚动过程中触发，并且按照浏览器的最快反应时间（一般5～7ms）的频率触发
 *      例如：我们滚动100ms，按照5ms触发一次，一共触发20次
 *      触发频率太高了，造成了没必要的计算和性能消耗
 * * 此时我们需要降低触发频率（不是降低浏览器的触发频率，而是把compute函数执行的频率降下来）
 *      此操作称为“函数节流”
 */
window.onload = compute;
window.onscroll = compute;
// window.onscroll = utils.throttle(compute, 300);
```

##### DOM 与样式部分

```HTML
<div id="container" >
    <img id="img" real-src="https://images.universal-music.de/img/assets/590/590645/4/380/00028948654734-giulini-bruckner-packshotjpg.jpg" >
</div>

<script src="./index.js" ></script>

<style>
    #container {
        width: 210px;
        height: 90px;
        position: absolute;
        margin: 2000px auto;
        background-color: #bbb;
    }
    #img {
        opacity: 0;
        transition: opacity 1s;
    }
</style>
```

### 现代方案

* 优势：简洁方便
* 劣势：旧版浏览器不支持

#### 思路

使用 `IntersectionObserver` 实现

```JS
const element1 = document.querySelector('.container1');
const element2 = document.querySelector('.container2');
const element3 = document.querySelector('.container3');

// 创建监听器
const observer = new IntersectionObserver(changes => {
    /**
     * 回调函数执行：
     * * 创建监听器、且监听了DOM元素会立即执行一次
     *      （连续监听多个DOM只触发一次，但是如果监听是分隔开的，每新监听一个元素都会触发执行一次）
     * * 当监听的元素和可视窗口交叉状态改变，也会触发执行
     *      （默认是“一露头”或者“完全出现”，会触发；
     *      也可以基于第二个Options配置项中的threshold来指定规则）
     *      * threshold: [0]    // 一露头 & 完全出现
     *      * ...
     *      * threshold: [1]    // 完全出现 & 出现一点
     * ----
     * changes: 是一个数组，记录了每一个监听元素和可视窗口的交叉信息
     * * boundingClientRect: 记录当前监听元素的getBoundingClientRect获取的值
     * * isIntersecting: true / false，true代表出现在可视窗口中，false则反之
     * * target: 存储当前监听的这个DOM元素对象
     * * ...
     */
    const [item] = changes;
    console.log({item});
}, { threshold: [0, 0.5, 1] });

// 监听某个DOM元素和可视窗口的交叉状态改变；unobserve移除监听
observer.observe(element1);
observer.observe(element2);         //连续监听多个DOM只触发一次回调
setTimeout(() => {
    observer.observe(element3);     //如果监听是分隔开的，每新监听一个元素都会触发执行一次回调
}, 1000);
```

#### 实现

```JS
const imgContainer = document.querySelector('.container');
const imgElement = imgContainer.querySelector('img');
const lazy = () => {    // 延迟加载
    const realSrc = imgElement.getAttribute('real-src');
    imgElement.src = realSrc;
    imgElement.onload = () => {
        imgElement.style.opacity = '1';
    };
};

// 创建监听器监听图片元素的容器，控制延迟加载：无需再进行复杂运算、无需考虑函数节流……
const observer = new IntersectionObserver(([item]) => {
    if (!item.isIntersecting) { return; }
    // 完全出现在视口中时：进行延迟加载
    lazy();
    // 处理过的需要移除监听
    observer.unobserve(item.target);
}, { threshold: [1] });

observer.observe(imgContainer);
```

---


# 电商平台商品图放大镜效果

![布局图](shopping-zoom.png)

光标处于 mark 中间，鼠标移动时，控制 mark 在 abbre 中移动

注意点：
* mark 移动的距离不能超过 abbre
* 同时需要控制 detailImg 在 detail 中移动

长宽比例关系：

mark / abbre = detail / detailImg

可知 detailImg = detail / (mark / abbre)

**位置关系**：

* ![mark 位置示意图](shopping-zoom-calculate.png)

    mark 的位置 = 光标相对于页面的位置 - abbre 的偏移 - mark 宽高的一半

    因为**光标在盒子中间**

* mark 也不可以超过 abbre 的边界：

    * 最小 `top`、`left` = 0
    * 最大 `top`、`left` = abbre - mark

---


# 拖动排序

```JS
const data = [
    { index: 0, hash: 'yule', name: '娱乐', background: 'red' },
    { index: 1, hash: 'yinyue', name: '音乐', background: 'lightsalmon' },
    { index: 2, hash: 'wudao', name: '舞蹈', background: 'lightseagreen' },
    { index: 3, hash: 'shenghuo', name: '生活', background: 'blue' },
];
let zIndex = 0;

const init = data => {
    const contentElement = document.getElementById('content');
    const tabBox = document.getElementById('tabBox');
    let contentStr = '';
    let tabStr = '';
    data.forEach(i => {
        contentStr += `<div id="${i.hash}" style="background: ${i.background}">
        ${i.name}</div>`;
        tabStr += `<li><a href="#${i.hash}">${i.name}</a></li>`;
    });
    contentElement.innerHTML = contentStr;
    console.log({tabStr})
    tabBox.innerHTML = tabStr;

    const liList = tabBox.getElementsByTagName('li');

    const move = function (e) {
        // 阻止拖动的默认行为
        e.preventDefault();
        // 跟随光标
        this.style.top = e.pageY - this.y + this.top + 'px';
        // 动画效果
        for (let i = 0; i < liList.length; i++) {
            // 如果这个liList[i]就是this，就不用比较了
            if (liList[i] === this) { continue; }
            // 当前项
            const currentLi = liList[i];
            console.log(this.offsetTop, currentLi.offsetTop, this.i, currentLi.i);
            if (this.offsetTop > currentLi.offsetTop
                && this.i < currentLi.i
            ) {
                /* 如果当前项的 offsetTop 大于了 currentLi 的 offsetTop，
                    就让 currentLi 把自己的位置让出来 */
                currentLi.style.marginTop = -currentLi.offsetHeight + 'px';
            }
            if (this.offsetTop < currentLi.offsetTop
                && this.i < currentLi.i
            ) {     //还原
                currentLi.style.marginTop = 0 + 'px';
            }
            if (this.offsetTop < currentLi.offsetTop
                && this.i > currentLi.i
            ) {     //下面的目录项往上移时的变化
                currentLi.style.marginTop = currentLi.offsetHeight + 'px';
            }
            if (this.offsetTop > currentLi.offsetTop
                && this.i > currentLi.i
            ) {     //还原
                currentLi.style.marginTop = 0 + 'px';
            }
            console.log(currentLi.style.marginTop);
        }
    }

    // 倒着是因为有样式问题，因为定位会脱离文档流
    for (let i = liList.length - 1; i >= 0; i--) {
        // console.log(liList[i].offsetTop, liList[i].offsetLeft, liList[i].offsetParent)
        liList[i].style.top = liList[i].offsetTop + 'px';
        liList[i].style.position = 'absolute';
        liList[i].i = data[i].index;

        liList[i].onmousedown = function (e) {
            this.y = e.pageY;
            this.top = Number.parseFloat(this.style.top);

            // 永远保持被拖动的盒子在最上层
            this.style.zIndex = ++zIndex;
            // 用事件委托绑定鼠标移动事件
            document.onmousemove = move.bind(this);
        }


        window.ondragstart = e => {     //须阻止页面触发 drag 相关事件，否则后面的 mouseup 不触发
            e.preventDefault();
            e.stopPropagation();
        };
        liList[i].onmouseup = function () {
            // 清除document上的移动事件
            document.onmousemove = null;

            for (let i = 0; i < liList.length; i++) {
                // 要把当前的 li 和被拖动的 li 的距离存起来
                liList[i].distance = Math.abs(this.offsetTop - liList[i].offsetTop);
            }

            // 根据distance排序
            let ary = [...liList].sort((a, b) => a.distance - b.distance);
            console.log(ary.map(i => i.style.marginTop));
            // 找出 ary 里 marginTop 不是 0px 的 li
            let close = ary.find(i => i.style.marginTop     //排除掉自己（未设置 marginTop 故为空）
                && i.style.marginTop !== '0px'
            );
            if (!close) {   //若顺序没有改变，则复位
                return this.style.top = this.top + 'px';
            }

            // 取到当前被拖动的 li
            let current = data.splice(this.i, 1)[0];
            data.splice(close.i, 0, current);

            data.forEach((item, index) => {
                item.index = index
            })

            init(data);
        }
    }
};

init(data);
```

```HTML
<html>
    <head>
        <meta charset="utf-8">
    </head>

    <body>
        <div id="content" class="content"></div>
        <div>
            <ul id="tabBox" class="tab-box"></ul>
        </div>
    </body>

    <script src="./index.js"></script>

    <style>
        * {
            margin: 0;
            padding: 0;
        }
        ul,
        li {
            list-style: none;
        }
        a {
            display: block;
            text-decoration: none;
            color: #000;
        }
        #content {
            overflow: hidden;
            background: pink;
            margin: 0 auto;
            width: 800px;
        }
        #content div {
            height: 200px;
            font-size: 40px;
            line-height: 200px;
            text-align: center;
        }
        #tabBox {
            position: relative;
            width: 50px;
            position: fixed;
            left: 0;
            top: 50px;
            /* top: 0px; */
        }
        #tabBox a {
            text-align: center;
            float: left;
            width: 100px;
            border: 1px solid #000;
            height: 50px;
            font-size: 20px;
            line-height: 50px;
        }
        #tabBox li {
            float: left;
            user-select: none;
            transition: margin .2s ease 0s;
            display: block;
            background: #fff;
            margin-top: 0;
        }
    </style>
</html>
```

<!-- ---


# 闭包总结

面试回答维度：

* 基础知识
* 实战应用
* 高阶应用
* 源码阅读
* 插件组件封装

切忌背书式回答，需要润色一个故事

1. stack、heap、EC、VO、AO、GO、scope、scope-chain
2. GC（浏览器垃圾回收机制）：标记清除、引用计数
3. 实际应用及优缺点
4. 进阶专题：单例模式、惰性函数、柯里化函数、compose组合函数……
5. 再次进阶：手撕源码（jQuery源码、Lodash源码、Redux源码）
6. 臻于大成：插件组件封装

注：儒家思想“温良恭俭让”；不要展开得太细（留一些线索给面试官接着问即可）；剧本精神

否则很难在有限的时间内把更多自己的优点传达给面试官，所以要在回答的过程中暴露出面试官感兴趣的点

例：“谈谈对闭包的理解”

工作中遇到过一个bug，发现自己对闭包理解得不是很好，于是自己**利用下班时间查阅了大量资料，对闭包作了一个详细深入的了解**，接下来我把**我对闭包的理解**跟您说一下，**但是毕竟您的经验比我多，您看看有哪方面说得不到位，欢迎您指正**。

我认为闭包还是比较重要的，毕竟它是JS最底层的部分之一，在我们平时开发也好，**研究源码**的时候也好，闭包的应用场景我们还是能看到很多的。谈及对闭包的理解的话首先从它的**基础知识**开始吧。（“我认为闭包是怎么形成的”，抛专业名词：）闭包**形成的主要原因**是由于浏览器的垃圾回收机制，函数执行产生的上下文中的某些东西被外面的东西引用了，导致当前上下文不能被释放，所以产生了闭包，除了保护其内部的变量不受外界干扰，还能把变量值保留下来，供下级上下文使用（把上下文、垃圾回收机制、作用域、作用域链等基础知识梳理出来）。

**在我日常工作当中，用到闭包的地方**，比如循环事件绑定的时候，用let的方式，在绑定的方法中可以用到索引，其中let的原理本质上也是闭包；还有循环创建多个定时器，让它每间隔一秒做某件事，我也是使用闭包方案解决索引问题（找**两到三个对应例子**）。**还有其他例子，一时间想不起来，毕竟闭包还是挺常用的。**

除此之外，**工作之余我也会看一些源码、学习一些思想，给公司做一些插件、组件的封装，提高开发效率**。之前**看源码的时候**，比如jQuery的源码，它刚开始的时候检测环境，用的也是闭包；之前还看过Redux源码，它里面的_________也是用闭包实现的。而且之前**我工作过程中**，涉及到函数的防抖和节流，**封装方法**的时候，也用到了闭包、柯里化函数思想实现。还有就是我们之前的工作过程中，涉及一个函数的嵌套，层层嵌套，比较麻烦，所以写了一个compose，组合式函数，让它能扁平化地处理，也是用闭包的机制实现的。（找**四、五个案例，不用详细描述**，大概说出用的哪个办法、怎么弄，示意自己工作过程中封装过很多公共的函数，提高了开发效率，示意自己基础知识的把握，示意自己阅读源码、实战应用、高阶应用的经历。）

总之我认为闭包还是很常用、很重要的。**但是呢，因为它不释放，内存消耗也比较大，所以我们在项目中还是应该慎用闭包、合理使用闭包**。

**我现在暂时能想到的就这些，您看看还有哪些地方不足的，麻烦您指正一下。**

（让面试官感受到非技术方面的：主动学习、有想法会总结、不是仅仅Ctrl C-V、肯下功夫；技术方面：读过源码、做过封装） -->


---

***`//未完待xu`***

---


![公众号二维码](https://neptliang.github.io/img/Article/WeChatBlog.png)