---
layout:       post
title:        "Less B站课程笔记"
subtitle:     "undefined"
date:         2022-08-26 16:17:00
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - FrontEnd
---


# `0x01` 引入

* 直接在页面引入JS脚本时的注意事项：

    依赖的JS脚本应写在HTML文档的前部，但是这里不是依赖Less，而是要Less读取HTML文档中的Less样式代码并转换成CSS，故引入Less的`<script>`标签应写在HTML文档的尾部；还有直接在HTML文档中写Less样式代码时，应给`<style>`标签设置`type="text/less"`

# `0x02` 注释

* 以`//`开头的注释**不会**被编译到CSS文件中
* 以`/**/`包裹的注释**会**被编译到CSS文件中

---


# `0x03` 变量

* 声明：使用`@`来声明变量
    
    ```Less
    @color: pink;
    ```

* 使用：

    1. 变量作为普通的**属性值**来使用：
        
        直接使用`@变量名`的形式

        ```Less
        .inner {
            background: @color
        }
        ```

    2. 变量作为**选择器**和**属性名**：

        使用`@{变量名}`的形式

        ```Less
        //声明
        @m: margin;
        @selector: #wrap;

        * {
            //作属性名
            @{m}: 0;    
        }
        //作选择器
        @{selector} { position: relative }      
        ```

        **一般不用**变量作选择器和属性名

    3. 作为URL：`@{url}`

* 块级作用域与延迟加载

    * Less中变量遵循**块级作用域**（花括号代码块）
    * Less中变量的**延迟加载**：等到作用域中的变量定义语句都解析完之后，才把结果赋给变量

    ```Less
    @var: 0;
    
    .class {
        @var: 1;

        .brass {
            @var: 2;
            line-height: @var;      // 因有延迟加载特性，故为3
            @var: 3
        }

        line-height: @var;      // 遵循块级作用域，故为1
    }
    ```

---


# `0x04` 嵌套规则

* `&`的使用

    `&`指上级选择器

    ```Less
    .inner {
        background: deeppink;

        &:hover {   //如果这里不写&，生成的:hover与.inner之间会有空格，:hover的样式就应用不到.inner上
            background: pink;
        }
    }
    ```

---


# `0x05` 混合

混合就是将一系列属性从一个规则集引入到另一个规则集的方式

混合的定义在Less规则有明确的规定，规范要求**必须以`.`开头**的形式来定义（以`#`开头的只有部分编译器支持）

* 普通混合

    ```Less
    //声明混合
    .centering {    //（不使用flex布局实现水平、垂直居中）
        position: absolute;
        left: 0;
        right: 0;
        top: 0;
        bottom: 0;
        margin: auto;
        /* background: pink;
        height: 100px;
        width: 100px; */
    }

    #wrap {
        /* position: relative;
        margin: 0 auto; */
        width: 300px;
        height: 400px;
        border: 1px solid;
        .inner1 {
            .centering;     //使用混合
        }                
        .inner2 {
            .centering;     //使用混合
        }
    }
    ```

* 不带输出的混合

    声明部分不会输出到CSS文件里，减小CSS文件大小

    ```Less
    .centering() {      //不会输出到输出的CSS文件里
        position: absolute;
    }

    .inner {
        .centering;
    }
    ```

* 带参数的混合

    ```Less
    .centering(@length, @color) {   //带参数的混合
        background: @color;
        height: @length;
        width: @length;
    }

    .inner1 {
        .centering(100px, red);
    }
    .inner2 {
        .centering(200px, blue);
    }
    ```

* 带参数并且有默认值的混合

    ```Less
    .centering(
        @length: 100px, 
        @color: red
    ) {     //带参数并且有默认值
        background: @color;
        height: @length;
        width: @length;
    }

    .inner1 {
        .centering();
    }
    .inner2 {
        .centering(200px, blue);
    }
    ```
    
* 命名参数

    指定把值赋给哪个参数

    ```Less
    .centering(@length: 100px, @color: red) {
        background: @color;
        height: @length;
        width: @length;
    }

    .inner {
        .centering(@color: black);      //命名参数
    }
    ```

* 匹配模式

    ```Less
    // 示例：CSS border样式画四个方向的等腰直角三角形
    // triangle.less:（单独写在一个文件作为库）
    // .mixinName(@_, @para1, @para2, ...): 所有同名匹配模式共用的样式，@_对应模式
    .triangle(@_, @w, @c) {     //注意参数个数须一致
        width: 0px;
        height: 0px;
        overflow: hidden;   //处理IE下形状异常问题（overflow: hidden可以处理很多IE样式问题）
    }

    .triangle(LEFT, @width, @color) {
        border-width: @width;
        border-style: dashed solid dashed dashed;   //Chrome下另外三边颜色设为transparent即可，但是IE不支持会变成黑色，故可设置边框样式为dashed，正好显示虚线间（也是虚线前）的空白部分
        border-color: transparent @color transparent transparent;
    }

    .triangle(RIGHT, @width, @color) {
        border-width: @width;
        border-style: dashed dashed dashed solid;
        border-color: transparent transparent transparent @color;
    }

    .triangle(TOP, @width, @color) {
        border-width: @width;
        border-style: dashed dashed solid dashed;
        border-color: transparent transparent @color transparent;
    }

    .triangle(BOTTOM, @width, @color) {
        border-width: @width;
        border-style: solid dashed dashed dashed;
        border-color: @color transparent transparent transparent;
    }
    ```

    ```Less
    // test.less:（业务代码调用前面封装的库）
    @import "./triangle.less";

    #wrap .triangle1 {
        .triangle(TOP, 50px, red);      //调用匹配模式混合
    }
    ```

* arguments变量

    ```Less
    .button(@width, @style, @color) {
        color: @color;
        border: @arguments;     //arguments变量
    }

    .submitButton {
        .button(5px, solid, green);
    }
    .cancleButton {
        .button(5px, solid, #333);
    }
    .disabledButton {
        .button(5px, solid, #bbb);
    }
    ```

# `0x06` 继承

* 混合的问题：每次使用混合，都会在使用的位置复制一份混合里的样式，输出的文件会很大

    而继承会把继承了样式的选择器合起来输出一份共用的样式

* 继承的问题：虽然性能比混合高，但是不如混合灵活，**不支持传参**

* 示例：继承实现的非flex布局垂直水平居中

    ```HTML
    <!-- test.html -->
    <link type="text/css" rel="stylesheet" href="./output.css" />
    <div id="wrap" >
        <div class="inner" >
            inner1
        </div>  
        <div class="inner" >
            inner2
        </div>
    </div>
    ```

    ```Less
    // extend/centering-extend-version.less
    .centering {
        position: absolute;
        left: 0;
        right: 0;
        top: 0;
        bottom: 0;
        margin: auto;
    }

    .centering:hover {
        background: yellow;
    }
    ```

    ```Less
    // test.less:
    @import "extend/centering-extend-version.less";

    #wrap {
        position: relative;
        width: 300px;
        height: 400px;
        border: 1px solid;

        // 继承
        .inner:extend(.centering all) {     //生成一个合并的选择器来放置共同样式，避免输出两份相同的样式；all声明把伪类选择器也继承下来
            &:nth-child(1) {
                width: 100px;
                height: 100px;
                background: red;
            }
            &:nth-child(2) {
                width: 50px;
                height: 50px;
                background: blue;
            }
        }
    }
    ```

    ```CSS
    /* 输出的部分CSS： */
    .centering,
    #wrap .inner {
        position: absolute;
        left: 0;
        right: 0;
        top: 0;
        bottom: 0;
        margin: auto;
    }
    .centering:hover,
    #wrap .inner:hover {
        background: yellow;
    }
    ```

# `0x07` 运算

* CSS的`calc`是运行时计算，影响运行性能，而Less的数学计算是编译时计算

* 单位相同时只需有一个单位

    ```Less
    #wrap {
        width: 100 + 100px;     //200px
    }
    ```

# `0x08` 避免编译

避免Less把`calc`中的表达式给编译了

```Less
.div1 {
    // height: 400px;
    // width: 400px;
    // background: red;

    padding-top: 20px + 10;
    padding-left: 20% + 10px;
    padding-right: calc(20% + 10px);
    padding-bottom: ~"calc(20% + 10px)";    //避免Less把 calc(20% + 10px) 中的 20% + 10px 给编译了
}
```


---

***`//End of Article`***

---


![公众号二维码](https://neptliang.github.io/img/Article/WeChatBlog.png)