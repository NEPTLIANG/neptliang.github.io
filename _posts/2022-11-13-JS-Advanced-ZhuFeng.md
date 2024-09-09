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


---

***`//未完待xu`***

---


![公众号二维码](https://neptliang.github.io/img/Article/WeChatBlog.png)