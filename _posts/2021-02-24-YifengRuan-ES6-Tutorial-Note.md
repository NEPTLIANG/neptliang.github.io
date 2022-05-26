---
layout:       post
title:        "阮一峰《ECMAScript 6 入门教程》整理"
subtitle:     "null"
date:         2021-02-24 13:54:56
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - FrontEnd
---


# `let`和`const`命令

## 1. `let`命令

### 基本用法

* ES6新增了`let`命令，用来**声明变量**。

* 它的用法类似于`var`，但是所声明的变量，只在`let`命令**所在的代码块内**有效。

* **`for`循环的计数器**，就很合适使用`let`命令。计数器只在`for`循环体内有效，在循环体外引用就会报错。

* `let`声明的当前的`i`**只在本轮循环有效**，每一次循环的`i`其实都是一个新的变量，JavaScript引擎内部会记住上一轮循环的值，初始化本轮的变量`i`时，就在上一轮循环的基础上进行计算。

* 另外，`for`循环还有一个特别之处，就是设置循环变量的那部分**是一个父作用域**，而循环体内部是一个单独的子作用域。

### 不存在变量提升

* `var`命令会发生“变量提升”现象，即变量可以在声明之前使用，值为`undefined`。为了纠正这种现象，`let`命令改变了语法行为，它所声明的变量**一定要在声明后使用**，否则报错。

### 暂时性死区

* 只要块级作用域内存在`let`命令，它所声明的变量就“绑定”（binding）这个区域，不再受外部的影响。

    ES6明确规定，如果区块中存在`let`和`const`命令，这个区块对这些命令声明的变量，从一开始就形成了封闭作用域。凡是在声明之前就使用这些变量，就会报错。

    总之，在代码块内，使用`let`命令声明变量之前，该变量都是不可用的。这在语法上，称为“**暂时性死区**”（temporal dead zone，简称 TDZ）。

* “暂时性死区”也**意味着`typeof`不再是一个百分之百安全的操作**。

    变量使用let命令声明，所以在声明之前，都属于“死区”，只要用到该变量就会报错。因此，`typeof`运行时就会**抛出一个`ReferenceError`**。作为比较，如果一个变量根本没有被声明，使用`typeof`反而不会报错。所以，在没有let之前，typeof运算符是百分之百安全的，永远不会报错。现在这一点不成立了。这样的设计是为了让大家养成良好的编程习惯，变量一定要在声明之后使用，否则就报错。

    ES6规定暂时性死区和`let`、`const`语句不出现变量提升，主要是为了**减少运行时错误**，防止在变量声明前就使用这个变量，从而导致意料之外的行为。这样的错误在 ES5 是很常见的，现在有了这种规定，避免此类错误就很容易了。

    总之，暂时性死区的本质就是，只要一进入当前作用域，所要使用的变量就**已经存在了，但是不可获取**，只有等到声明变量的那一行代码出现，才可以获取和使用该变量。

### 不允许重复声明

* `let`**不允许**在相同作用域内，重复声明同一个变量。

## 2. 块级作用域

### 为什么需要块级作用域？

* ES5只有全局作用域和函数作用域，没有块级作用域，这带来很多不合理的场景。

    * 第一种场景，内层变量可能会覆盖外层变量。

        `if`代码块的外部使用外层的变量，内部使用内层的变量。但是，函数执行后，输出结果为undefined，原因在于变量提升，导致内层的变量覆盖了外层的变量。
    
    * 第二种场景，用来计数的循环变量泄露为全局变量。

### ES6的块级作用域

* **`let`实际上为JavaScript新增了块级作用域。**
    
    外层代码块不受内层代码块的影响。

    ES6允许块级作用域的任意嵌套。

    块级作用域的出现，实际上使得获得广泛应用的匿名立即执行函数表达式（匿名IIFE）不再必要了。

### 块级作用域与函数声明

* ES5规定，函数只能在顶层作用域和函数作用域之中声明，不能在块级作用域声明。但是，浏览器没有遵守这个规定，为了兼容以前的旧代码，还是支持在块级作用域之中声明函数。

* ES6引入了块级作用域，明确**允许在块级作用域之中声明函数**。ES6规定，块级作用域之中，函数声明语句的行为**类似于`let`**，在块级作用域之外不可引用。

    如果改变了块级作用域内声明的函数的处理规则，显然会对老代码产生很大影响。为了减轻因此产生的不兼容问题，ES6在附录B里面规定，**浏览器的实现可以不遵守上面的规定**，有自己的行为方式。

    * 允许在块级作用域内声明函数。
    * 函数声明类似于var，即会提升到全局作用域或函数作用域的头部。
    * 同时，函数声明还会提升到所在的块级作用域的头部。

    注意，上面三条规则只对ES6的浏览器实现有效，**其他环境的实现不用遵守**，还是将块级作用域的函数声明**当作`let`处理**。

    根据这三条规则，**浏览器的ES6环境中，块级作用域内声明的函数，行为类似于var声明的变量**。

    **考虑到环境导致的行为差异太大，应该避免在块级作用域内声明函数。如果确实需要，也应该写成函数表达式，而不是函数声明语句。**

* 另外，还有一个需要注意的地方。**ES6的块级作用域必须有大括号**，如果没有大括号，JavaScript引擎就认为不存在块级作用域。

* **`let`只能出现在当前作用域的顶层**

* 函数声明也是如此，严格模式下，**函数只能声明在当前作用域的顶层**。

## 3. `const`命令

### 基本用法

* `const`声明一个只读的**常量**。改变常量的值会报错。

* `const`声明的变量不得改变值，这意味着，`const`一旦声明变量，就**必须立即初始化**，不能留到以后赋值。只声明不赋值，就会报错。

* `const`的作用域与`let`命令相同：**只在声明所在的块级作用域内有效**。

* `const`命令声明的常量也是不提升，同样存在暂时性死区，**只能在声明的位置后面使用**。在常量声明之前就调用，结果报错。

* `const`声明的常量，也与`let`一样**不可重复声明**。

### 本质

* const实际上保证的，并不是变量的值不得改动，而是变量指向的那个内存地址所保存的数据不得改动。对于**简单类型**的数据（数值、字符串、布尔值），值就保存在变量指向的那个内存地址，因此等同于常量。

* 但对于**复合类型**的数据（主要是对象和数组），变量指向的内存地址，保存的只是一个指向实际数据的指针，`const`只能保证这个指针是固定的（即总是指向另一个固定的地址），至于它**指向的数据结构是不是可变的，就完全不能控制了**。因此，将一个对象声明为常量必须非常小心。

    常量储存的是一个地址，这个地址指向一个对象时，不可变的只是这个地址，即不能指向另一个地址，但对象本身是可变的，所以依然可以为其添加新属性。

    常量是一个数组时，这个数组本身是可写的，但是如果将另一个数组赋值给它，就会报错。

* 如果真的想将对象冻结，应该使用`Object.freeze`方法。
    
    常量指向一个冻结的对象，添加新属性不起作用，严格模式时还会报错。除了将对象本身冻结，对象的属性也应该冻结。

### ES6声明变量的六种方法

* ES5只有两种声明变量的方法：
    * `var`
    * `function`
* ES6 
    * `let`
    * `const`
    * `import`
    * `class`

* 所以，ES6 一共有 6 种声明变量的方法。

## 4. 顶层对象的属性

* 顶层对象，在**浏览器环境**指的是`window`对象，在**Node**指的是`global`对象。ES5之中，顶层对象的属性与全局变量是等价的。

    * 顶层对象的属性与全局变量挂钩，被认为是JavaScript语言最大的设计败笔之一。这样的设计带来了几个很大的问题，
        
        首先是没法在编译时就报出变量未声明的错误，只有运行时才能知道（因为全局变量可能是顶层对象的属性创造的，而属性的创造是动态的）；
        
        其次，程序员很容易不知不觉地就创建了全局变量（比如打字出错）；
        
        最后，顶层对象的属性是到处可以读写的，这非常不利于模块化编程。
        
        另一方面，window对象有实体含义，指的是浏览器的窗口对象，顶层对象是一个有实体含义的对象，也是不合适的。

* ES6为了改变这一点，一方面规定，为了保持兼容性，`var`命令和`function`命令声明的全局变量，**依旧是顶层对象的属性**；

* 另一方面规定，`let`命令、`const`命令、`class`命令声明的全局变量，**不属于顶层对象的属性**。

    也就是说，从 ES6 开始，全局变量将逐步与顶层对象的属性脱钩。

## 5. `globalThis`对象

* JavaScript语言存在一个顶层对象，它提供全局环境（即全局作用域），所有代码都是在这个环境中运行。但是，顶层对象在各种实现里面是不统一的。

    * **浏览器**里面，顶层对象是`window`，但Node和Web Worker没有`window`。
    * **浏览器和`Web Worker`里面**，`self`也指向顶层对象，但是 Node没有`self`。
    * **Node**里面，顶层对象是`global`，但其他环境都不支持。

    环境        |   `window`  | `self`     |`global`
    ------------|:----------:|:-----------:|:----:
    浏览器      |   ✔        |   ✔        |  ❌
    Web Worker  |   ❌       |   ✔        |  ❌
    Node        |   ❌       |   ❌       |   ✔

* 同一段代码为了能够在各种环境，都能取到顶层对象，现在一般是使用`this`变量，但是有局限性。

    * **全局环境**中，`this`会返回**顶层对象**。  
        但是，**Node.js 模块**中`this`返回的是**当前模块**，
        **ES6模块**中`this`返回的是`undefined`。
    * **函数里面**的`this`，如果函数不是作为对象的方法运行，而是**单纯作为函数运行**，`this`会指向**顶层对象**。
        但是，严格模式下，这时`this`会返回`undefined`。
    * 不管是严格模式，还是普通模式，`new Function('return this')()`，总是会返回全局对象。但是，如果浏览器用了CSP（Content Security Policy，内容安全策略），那么`eval`、`new Function`这些方法都可能无法使用。

    综上所述，很难找到一种方法，可以在所有情况下，都取到顶层对象。

* **[ES2020](https://github.com/tc39/proposal-global)在语言标准的层面，引入`globalThis`作为顶层对象**。也就是说，任何环境下，`globalThis`都是存在的，都可以从它拿到顶层对象，指向全局环境下的`this`。

    垫片库`global-this`模拟了这个提案，可以在所有环境拿到`globalThis`。

---


# 变量的解构赋值

## 1. 数组的解构赋值

### 基本用法

* ES6允许按照一定模式，从数组和对象中提取值，对变量进行赋值，这被称为**解构（Destructuring）**。

    如果解构不成功，变量的值就等于`undefined`。

    另一种情况是不完全解构，即等号左边的模式，只匹配一部分的等号右边的数组。这种情况下，解构依然可以成功。

    如果等号的右边不是数组（或者严格地说，不是可遍历的结构，参见《Iterator》一章），那么将会报错。

    对于`Set`结构，也可以使用数组的解构赋值。

    `Generator`函数（参见《Generator函数》一章）原生具有`Iterator`接口。解构赋值会依次从这个接口获取值。

### 默认值

* 解构赋值允许指定默认值。

    注意，ES6 内部使用严格相等运算符（`===`），判断一个位置是否有值。所以，只有当一个数组成员严格等于`undefined`，默认值才会生效。

    如果默认值是一个表达式，那么这个表达式是惰性求值的，即只有在用到的时候，才会求值。变量能取到值时，表达式根本不会执行。

    默认值可以引用解构赋值的其他变量，但该变量必须已经声明。

## 2. 对象的解构赋值

### 简介

* 解构不仅可以用于数组，还可以用于对象。

* 对象的解构与数组有一个重要的不同。数组的元素是按次序排列的，变量的取值由它的位置决定；而对象的属性没有次序，**变量必须与属性同名**，才能取到正确的值。

* 如果解构失败，变量的值等于`undefined`。

* 对象的解构赋值，可以很方便地将现有对象的方法，赋值到某个变量。

* 与数组一样，解构也可以用于嵌套结构的对象。

* 如果解构模式是嵌套的对象，而且子对象所在的父属性不存在，那么将会报错。

* 注意，对象的解构赋值可以取到继承的属性。

### 默认值

* 对象的解构也可以指定默认值。默认值生效的条件是，对象的属性值严格等于`undefined`。

### 注意点

1. 如果要将一个已经声明的变量用于解构赋值，必须非常小心。
2. 解构赋值允许等号左边的模式之中，不放置任何变量名。虽然毫无意义，但是语法是合法的，可以执行。
3. 由于数组本质是特殊的对象，因此可以对数组进行对象属性的解构。

## 3. 字符串的解构赋值

* 字符串也可以解构赋值。这是因为此时，字符串被**转换成了一个类似数组的对象**。

* 类似数组的对象都有一个`length`属性，因此还可以对这个属性解构赋值。

## 4. 数值和布尔值的解构赋值

* 解构赋值时，如果等号右边是数值和布尔值，则会**先转为对象**。

* 解构赋值的规则是，只要等号右边的值不是对象或数组，就先将其转为对象。由于`undefined`和`null`无法转为对象，所以对它们进行解构赋值，都会报错。

## 5. 函数参数的解构赋值

* 函数的参数也可以使用解构赋值。

    函数参数的解构也可以使用默认值。

    `undefined`就会触发函数参数的默认值。

## 6. 圆括号问题

* 解构赋值虽然很方便，但是解析起来并不容易。对于编译器来说，一个式子到底是模式，还是表达式，没有办法从一开始就知道，必须解析到（或解析不到）等号才能知道。

    由此带来的问题是，如果模式中出现圆括号怎么处理。ES6的规则是，**只要有可能导致解构的歧义，就不得使用圆括号。**

* 但是，这条规则实际上不那么容易辨别，处理起来相当麻烦。因此，**建议只要有可能，就不要在模式中放置圆括号。**

### 不能使用圆括号的情况

* 以下三种解构赋值不得使用圆括号。

    1. 变量声明语句
    2. 函数参数  
        函数参数也属于变量声明，因此不能带有圆括号。
    3. 赋值语句的模式

### 可以使用圆括号的情况

* 可以使用圆括号的情况只有一种：**赋值语句的非模式部分**，可以使用圆括号。

## 7. 用途

* 变量的解构赋值用途很多。

    1. 交换变量的值
    2. 从函数返回多个值  
        函数只能返回一个值，如果要返回多个值，只能将它们放在数组或对象里返回。有了解构赋值，取出这些值就非常方便。
    3. 函数参数的定义  
        解构赋值可以方便地将一组参数与变量名对应起来。
    4. 提取`JSON`数据  
        解构赋值对提取`JSON`对象中的数据，尤其有用。
    5. 函数参数的默认值  
        指定参数的默认值
    6. 遍历`Map`结构  
        任何部署了`Iterator`接口的对象，都可以用`for...of`循环遍历。`Map`结构原生支持`Iterator`接口，配合变量的解构赋值，获取键名和键值就非常方便。
    7. 输入模块的指定方法  
        加载模块时，往往需要指定输入哪些方法。解构赋值使得输入语句非常清晰。

---


# `Promise`对象

## 1. `Promise`的含义

* `Promise`是**异步编程**的一种解决方案，比传统的解决方案——回调函数和事件——更合理和更强大。它由社区最早提出和实现，ES6将其写进了语言标准，统一了用法，原生提供了`Promise`对象。

    所谓`Promise`，简单说就是一个容器，里面保存着**某个未来才会结束的事件（通常是一个异步操作）的结果**。从语法上说，`Promise`是一个对象，**从它可以获取异步操作的消息**。`Promise`提供统一的API，各种异步操作都可以用同样的方法进行处理。

* `Promise`对象有**以下两个特点**。

    1. **对象的状态不受外界影响。**`Promise`对象代表一个异步操作，有**三种状态**：
        * **`pending`（进行中）**
        * **`fulfilled`（已成功）**
        * **`rejected`（已失败）**

        只有异步操作的结果，可以决定当前是哪一种状态，任何其他操作都无法改变这个状态。这也是Promise这个名字的由来，它的英语意思就是“承诺”，表示其他手段无法改变。

    2. **一旦状态改变，就不会再变，任何时候都可以得到这个结果。**`Promise`对象的状态改变，只有两种可能：
        
        * 从`pending`变为`fulfilled`
        * 从`pending`变为`rejected`

        只要这两种情况发生，状态就凝固了，不会再变了，会一直保持这个结果，**这时就称为`resolved`（已定型）**。如果改变已经发生了，你再对Promise对象添加回调函数，也会立即得到这个结果。这与事件（Event）完全不同，事件的特点是，如果你错过了它，再去监听，是得不到结果的。

> 注意，为了行文方便，本章后面的`resolved`统一只指`fulfilled`状态，不包含`rejected`状态。

* 有了`Promise`对象，就可以将异步操作以同步操作的流程表达出来，避免了层层嵌套的回调函数。此外，`Promise`对象提供统一的接口，使得控制异步操作更加容易。

* `Promise`也有一些**缺点**。

    * 首先，**无法取消`Promise`**，一旦新建它就会立即执行，无法中途取消。

    * 其次，**如果不设置回调函数，`Promise`内部抛出的错误，不会反应到外部。**
    
    * 第三，**当处于`pending`状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）。**

* 如果某些事件不断地反复发生，一般来说，使用[Stream](https://nodejs.org/api/stream.html)模式是比部署Promise更好的选择。

## 2. 基本用法

* ES6规定，`Promise`对象是一个**构造函数**，用来生成`Promise`实例。

* 下面代码创造了一个`Promise`实例。

    ```js
    const promise = new Promise(function(resolve, reject) {
    // ... some code

    if (/* 异步操作成功 */){
        resolve(value);
    } else {
        reject(error);
    }
    });
    ```

    * `Promise`构造函数**接受一个函数作为参数**，该函数的两个参数分别是`resolve`和`reject`。它们是两个函数，**由JavaScript引擎提供**，不用自己部署。

        * **`resolve`函数**的作用是，**将`Promise`对象的状态从“未完成”变为“成功”**（即从`pending`变为`resolved`），在异步操作成功时调用，并**将异步操作的结果，作为参数传递出去**；
        
        * **`reject`函数**的作用是，**将`Promise`对象的状态从“未完成”变为“失败”**（即从`pending`变为`rejected`），在异步操作失败时调用，并**将异步操作报出的错误，作为参数传递出去**。

* `Promise`实例生成以后，可以**用`then`方法**分别指定`resolved`状态和`rejected`状态的**回调函数**。

    ```js
    promise.then(function(value) {
    // success
    }, function(error) {
    // failure
    });
    ```

    `then`方法可以**接受两个回调函数作为参数**。
    
    * 第一个回调函数是`Promise`对象的状态**变为`resolved`时调用**

    * 第二个回调函数是`Promise`对象的状态**变为`rejected`时调用**。
    
    这两个函数都是可选的，不一定要提供。它们都**接受`Promise`对象传出的值作为参数**。

* `Promise`新建后就会立即执行。

* `then`方法指定的回调函数，将在当前脚本**所有同步任务执行完才会执行**。

* 如果调用`resolve`函数和`reject`函数时带有参数，那么它们的参数会**被传递给回调函数**。
    
    `reject`函数的参数通常是`Error`对象的实例，表示抛出的错误；`resolve`函数的参数除了正常的值以外，还可能是另一个`Promise`实例，即一个异步操作的结果是返回另一个异步操作。

* 注意，调用`resolve`或`reject`**并不会终结`Promise`的参数函数的执行**。

    一般来说，调用`resolve`或`reject`以后，`Promise`的使命就完成了，**后继操作应该放到`then`方法里面**，而不应该直接写在`resolve`或`reject`的后面。所以，**最好在它们前面加上`return`语句**，这样就不会有意外。

    ```js
    new Promise((resolve, reject) => {
        return resolve(1);
        // 后面的语句不会执行
        console.log(2);
    })
    ```

---


# `Iterator` 和 `for...of` 循环

## 1. `Iterator`（遍历器）的概念

* JavaScript原有的表示“集合”的数据结构，主要是数组（`Array`）和对象（`Object`），ES6又添加了`Map`和`Set`。这样就有了四种数据集合，用户还可以组合使用它们，定义自己的数据结构，比如数组的成员是`Map`，`Map`的成员是对象。这样就需要一种统一的接口机制，来处理所有不同的数据结构。

    遍历器（`Iterator`）就是这样一种机制。它是一种接口，为各种不同的数据结构提供统一的访问机制。任何数据结构只要部署`Iterator`接口，就可以完成遍历操作（即依次处理该数据结构的所有成员）。

* `Iterator`的**作用**有三个：
    * 一是为各种数据结构，提供一个统一的、简便的**访问接口**；
    * 二是使得数据结构的成员能够按某种次序**排列**；
    * 三是ES6创造了一种新的遍历命令`for...of`循环，`Iterator`接口主要**供`for...of`消费**。

* `Iterator`的遍历过程是这样的。

    1. 创建一个指针对象，**指向当前数据结构的起始位置**。也就是说，遍历器对象本质上，就是一个指针对象。
    2. 第一次调用指针对象的`next`方法，可以将指针**指向数据结构的第一个成员**。
    3. 第二次调用指针对象的`next`方法，指针就**指向数据结构的第二个成员**。
    4. 不断调用指针对象的`next`方法，直到它指向数据结构的结束位置。

* 每一次调用`next`方法，都会返回数据结构的当前成员的信息。具体来说，就是返回一个**包含`value`和`done`两个属性的对象**。

    * 其中，`value`属性是**当前成员的值**，
    * `done`属性是一个布尔值，表示**遍历是否结束**。

* 指针对象的`next`方法，用来移动指针。开始时，指针指向数组的**开始位置**。然后，每次调用`next`方法，指针就会指向数组的**下一个成员**。

    总之，调用指针对象的`next`方法，就可以**遍历事先给定的数据结构**。

* 由于`Iterator`只是把接口规格加到数据结构之上，所以，遍历器与它所遍历的那个数据结构，实际上是分开的，完全可以写出没有对应数据结构的遍历器对象，或者说用遍历器对象模拟出数据结构。

---


# `Generator`函数的语法

## 1. 简介

### 基本概念

* `Generator`函数是ES6提供的一种**异步编程解决方案**，语法行为与传统函数完全不同。本章详细介绍`Generator`函数的语法和API，它的异步编程应用请看《`Generator`函数的异步应用》一章。

* `Generator`函数有多种理解角度。
    
    * 语法上，首先可以把它理解成，`Generator`函数是一个状态机，**封装了多个内部状态**。

    * 执行`Generator`函数会返回一个遍历器对象，也就是说，`Generator`函数除了状态机，还是一个遍历器对象生成函数。返回的遍历器对象，可以依次**遍历`Generator`函数内部的每一个状态**。

* 形式上，`Generator`函数是一个普通函数，但是有两个特征。
    
    * 一是，`function`关键字与函数名之间有一个**星号**；
    * 二是，函数体内部**使用`yield`表达式**，定义不同的内部状态（yield在英语里的意思就是“产出”）。

    ```js
    function* helloWorldGenerator() {
        yield 'hello';
        yield 'world';
        return 'ending';
    }

    var hw = helloWorldGenerator();
    ```

    上面代码定义了一个`Generator`函数`helloWorldGenerator`，它内部有两个`yield`表达式（`hello`和`world`），**即该函数有三个状态：`hello`，`world`和`return`语句（结束执行）。**

* 然后，`Generator` 函数的调用方法与普通函数一样，也是**在函数名后面加上一对圆括号**。
    
    不同的是，调用`Generator`函数后，该函数并不执行，返回的也不是函数运行结果，而是一个**指向内部状态的指针对象**，也就是上一章介绍的遍历器对象（Iterator Object）。

    下一步，必须**调用遍历器对象的`next`方法，使得指针移向下一个状态**。
    
    也就是说，每次调用`next`方法，内部指针就从**函数头部或上一次停下来的地方开始执行**，直到遇到下一个`yield`表达式（或`return`语句）为止。
    
    换言之，`Generator`函数是**分段执行**的，`yield`表达式是**暂停执行的标记**，而`next`方法**可以恢复执行**。

    ```js
    hw.next()
    // { value: 'hello', done: false }

    hw.next()
    // { value: 'world', done: false }

    hw.next()
    // { value: 'ending', done: true }

    hw.next()
    // { value: undefined, done: true }
    ```

* 总结一下，调用`Generator`函数，返回一个**遍历器对象**，代表`Generator`函数的内部指针。以后，每次调用**遍历器对象的`next`方法**，就会返回一个**有着`value`和`done`两个属性的对象**。`value`属性表示当前的**内部状态的值**，是`yield`表达式后面那个表达式的值；`done`属性是一个布尔值，表示**是否遍历结束**。

* ES6没有规定，`function`关键字与函数名之间的星号，写在哪个位置。

    由于`Generator`函数仍然是普通函数，所以一般的写法是星号紧跟在`function`关键字后面。本书也采用这种写法。

---


# `Generator`函数的异步应用

* 异步编程对JavaScript语言太重要。JavaScript语言的执行环境是“单线程”的，如果没有异步编程，根本没法用，非卡死不可。本章主要介绍`Generator`函数如何完成异步操作。

## 1. 传统方法

* ES6 诞生以前，异步编程的方法，大概有下面四种。

    * 回调函数
    * 事件监听
    * 发布/订阅
    * `Promise`对象

* `Generator`函数将JavaScript异步编程带入了一个全新的阶段。

## 2. 基本概念

### 异步

* 所谓"异步"，简单说就是一个任务不是连续完成的，可以理解成该任务被人为分成两段，先执行第一段，然后转而执行其他任务，等做好了准备，再回过头执行第二段。

    比如，有一个任务是读取文件进行处理，任务的第一段是向操作系统发出请求，要求读取文件。然后，程序执行其他任务，等到操作系统返回文件，再接着执行任务的第二段（处理文件）。这种不连续的执行，就叫做异步。

* 相应地，连续的执行就叫做同步。由于是连续执行，不能插入其他任务，所以操作系统从硬盘读取文件的这段时间，程序只能干等着。

### 回调函数

* JavaScript语言对异步编程的实现，就是回调函数。所谓回调函数，就是把任务的第二段单独写在一个函数里面，等到重新执行这个任务的时候，就直接调用这个函数。回调函数的英语名字callback，直译过来就是"重新调用"。

* 一个有趣的问题是，为什么Node约定，回调函数的第一个参数，必须是错误对象`err`（如果没有错误，该参数就是`null`）？

    原因是执行分成两段，第一段执行完以后，任务所在的上下文环境就已经结束了。**在这以后抛出的错误，原来的上下文环境已经无法捕捉，只能当作参数，传入第二段。**

* 回调函数本身并没有问题，它的问题出现在**多个回调函数嵌套**。代码不是纵向发展，而是横向发展，很快就会乱成一团，无法管理。因为**多个异步操作形成了强耦合**，只要有一个操作需要修改，它的上层回调函数和下层回调函数，可能都要跟着修改。**这种情况就称为“回调函数地狱”（callback hell）。**

* `Promise`对象就是为了解决这个问题而提出的。它不是新的语法功能，而是一种新的写法，允许将回调函数的嵌套，改成链式调用。

* `Promise`的写法只是回调函数的改进，使用`then`方法以后，异步任务的两段执行看得更清楚了，除此以外，并无新意。

    `Promise`的最大问题是**代码冗余**，原来的任务被`Promise`包装了一下，**不管什么操作，一眼看去都是一堆`then`，原来的语义变得很不清楚**。那么，有没有更好的写法呢？

## 3. `Generator`函数

### 协程

* 传统的编程语言，早有异步编程的解决方案（其实是多任务的解决方案）。其中有一种**叫做“协程”（coroutine）**，意思是**多个线程互相协作，完成异步任务**。

    协程有点像函数，又有点像线程。它的**运行流程大致如下**。

    * 第一步，协程A开始执行。
    * 第二步，协程A执行到一半，**进入暂停，执行权转移到协程B**。
    * 第三步，（一段时间后）协程B**交还执行权**。
    * 第四步，协程A**恢复执行**。

    上面流程的协程A，就是异步任务，因为它分成两段（或多段）执行。

* 协程遇到`yield`命令就暂停，等到执行权返回，再从暂停的地方继续往后执行。它的最大优点，就是代码的写法非常像同步操作，如果去除`yield`命令，简直一模一样。

### 协程的`Generator`函数实现

* **`Generator`函数是协程在ES6的实现**，最大特点就是可以交出函数的执行权（即暂停执行）。

    **整个`Generator`函数就是一个封装的异步任务**，或者说是异步任务的容器。异步操作**需要暂停的地方，都用`yield`语句注明**。

* `next`方法的作用是**分阶段执行`Generator`函数**。每次调用`next`方法，会返回一个对象，表示**当前阶段的信息**（`value`属性和`done`属性）。`value`属性是`yield`语句后面表达式的值，表示**当前阶段的值**；`done`属性是一个布尔值，表示`Generator`函数**是否执行完毕**，即是否还有下一个阶段。

### `Generator`函数的数据交换和错误处理

* `Generator`函数可以暂停执行和恢复执行，这是它能封装异步任务的根本原因。除此之外，它还有两个**特性**，使它可以作为异步编程的完整解决方案：**函数体内外的数据交换**和**错误处理机制**。

    * `next`返回值的`value`属性，是`Generator`函数向外输出数据；**`next`方法还可以接受参数，向`Generator`函数体内输入数据**。

        ```js
        function* gen(x){
            var y = yield x + 2;
            return y;
        }

        var g = gen(1);
        g.next() // { value: 3, done: false }
        g.next(2) // { value: 2, done: true }
        ```

        上面代码中，第一个`next`方法的`value`属性，返回表达式`x + 2`的值`3`。第二个`next`方法带有参数`2`，这个参数可以传入`Generator`函数，**作为上个阶段异步任务的返回结果**，被函数体内的变量`y`接收。因此，这一步的`value`属性，返回的就是`2`（变量`y`的值）。
    
    * Generator 函数**内部**还可以部署错误处理代码，捕获**函数体外**抛出的错误。

        ```js
        function* gen(x){
            try {
                var y = yield x + 2;
            } catch (e){
                console.log(e);
            }
            return y;
        }

        var g = gen(1);
        g.next();
        g.throw('出错了');
        // 出错了
        ```

        上面代码的最后一行，`Generator`函数体外，使用**指针对象的`throw`方法**抛出的错误，可以被函数体内的`try...catch`代码块捕获。这意味着，**出错的代码与处理错误的代码，实现了时间和空间上的分离**，这对于异步编程无疑是很重要的。

### 异步任务的封装

* 下面看看如何使用`Generator`函数，执行一个真实的异步任务。

    ```js
    var fetch = require('node-fetch');

    function* gen(){
        var url = 'https://api.github.com/users/github';
        var result = yield fetch(url);
        console.log(result.bio);
    }
    ```

    上面代码中，`Generator`函数**封装了一个异步操作**，该操作先读取一个远程接口，然后从`JSON`格式的数据解析信息。就像前面说过的，这段代码非常像同步操作，除了加上了`yield`命令。

    执行这段代码的方法如下。

    ```js
    var g = gen();
    var result = g.next();

    result.value.then(function(data){
        return data.json();
    }).then(function(data){
        g.next(data);
    });
    ```

    上面代码中，首先执行`Generator`函数，获取遍历器对象，然后使用`next`方法（第二行），执行异步任务的第一阶段。由于`Fetch`模块返回的是一个`Promise`对象，因此要用`then`方法调用下一个`next`方法。

* 可以看到，虽然`Generator`函数**将异步操作表示得很简洁，但是流程管理却不方便**（即何时执行第一阶段、何时执行第二阶段）。

---


# `async`函数

## 1. 含义

* ES2017标准引入了`async`函数，使得异步操作变得更加方便。

    `async`函数是什么？一句话，它就是`Generator`函数的语法糖。

    ```js
    const asyncReadFile = async function () {
        const f1 = await readFile('/etc/fstab');
        const f2 = await readFile('/etc/shells');
        console.log(f1.toString());
        console.log(f2.toString());
    };
    ```

    * 一比较就会发现，`async`函数就是

        * 将`Generator`函数的星号（`*`）替换成`async`
        * 将`yield`替换成`await`
        
        仅此而已。

* `async`函数**对`Generator`函数的改进**，体现在以下四点。

    1. 内置执行器。

        `Generator`函数的执行必须靠执行器，所以才有了`co`模块，**而`async`函数自带执行器**。也就是说，`async`函数的执行，与普通函数一模一样，只要一行。

        ```js
        asyncReadFile();
        ```

        上面的代码调用了`asyncReadFile`函数，然后它就会**自动执行，输出最后结果**。这完全不像 `Generator`函数，需要调用`next`方法，或者用`co`模块，才能真正执行，得到最后结果。

    2. 更好的语义。

        * `async`和`await`，比起星号和`yield`，**语义更清楚了**。
            
            * `async`表示函数里**有异步操作**
            
            * `await`表示紧跟在后面的表达式**需要等待结果**。

    3. 更广的适用性。

        `co`模块约定，`yield`命令后面只能是`Thunk`函数或`Promise`对象，**而`async`函数的`await`命令后面，可以是`Promise`对象和原始类型的值**（数值、字符串和布尔值，但这时会自动转成立即`resolved`的`Promise`对象）。

    4. 返回值是`Promise`。

        `async`函数的**返回值是`Promise`对象**，这比`Generator`函数的返回值是`Iterator`对象方便多了。你可以**用`then`方法**指定下一步的操作。

* 进一步说，`async`函数完全可以看作**多个异步操作，包装成的一个`Promise`对象**，而`await`命令就是内部`then`命令的语法糖。

---


# Class 的基本语法

## 1. 简介

### 类的由来

* JavaScript 语言中，生成实例对象的传统方法是通过构造函数。下面是一个例子。

    ```js
    function Point(x, y) {
        this.x = x;
        this.y = y;
    }

    Point.prototype.toString = function () {
        return '(' + this.x + ', ' + this.y + ')';
    };

    var p = new Point(1, 2);
    ```

    上面这种写法跟传统的面向对象语言（比如 C++ 和 Java）差异很大，很容易让新学习这门语言的程序员感到困惑。

* **ES6 提供了更接近传统语言的写法，引入了 Class（类）这个概念**，作为对象的模板。通过`class`关键字，可以定义类。

    基本上，ES6 的`class`可以看作只是一个语法糖，它的绝大部分功能，ES5 都可以做到，新的`class`写法只是让对象原型的写法更加清晰、更像面向对象编程的语法而已。上面的代码用 ES6 的`class`改写，就是下面这样。

    ```js
    class Point {
        constructor(x, y) {
            this.x = x;
            this.y = y;
        }

        toString() {
            return '(' + this.x + ', ' + this.y + ')';
        }
    }
    ```

    上面代码定义了一个“类”，可以看到里面有一个`constructor()`方法，这就是**构造方法**，而`this`关键字则代表**实例对象**。这种新的 Class 写法，本质上与本章开头的 ES5 的构造函数Point是一致的。

    Point类除了构造方法，还定义了一个toString()**方法**。注意，定义toString()方法的时候，前面不需要加上`function`这个关键字，直接把函数定义放进去了就可以了。另外，方法与方法之间不需要逗号分隔，加了会报错。

* ES6 的类，完全可以看作构造函数的另一种写法。

    ```js
    class Point {
        // ...
    }

    typeof Point // "function"
    Point === Point.prototype.constructor // true
    ```

    上面代码表明，**类的数据类型就是函数**，类本身就指向构造函数。

* 使用的时候，也是直接对类**使用`new`命令**，跟构造函数的用法完全一致。

* 构造函数的`prototype`属性，在 ES6 的“类”上面继续存在。事实上，**类的所有方法都定义在类的`prototype`属性上面**。

    ```js
    class Point {
        constructor() {
            // ...
        }

        toString() {
            // ...
        }

        toValue() {
            // ...
        }
    }

    // 等同于

    Point.prototype = {
        constructor() {},
        toString() {},
        toValue() {},
    };
    ```

* 因此，**在类的实例上面调用方法，其实就是调用原型上的方法**。

    ```js
    class B {}
    const b = new B();

    b.constructor === B.prototype.constructor // true
    ```
    
    上面代码中，b是B类的实例，它的constructor()方法就是B类原型的constructor()方法。

* 由于类的方法都定义在`prototype`对象上面，所以类的新方法可以添加在`prototype`对象上面。**`Object.assign()`方法可以很方便地一次向类添加多个方法**。

    ```js
    class Point {
        constructor(){
            // ...
        }
    }

    Object.assign(Point.prototype, {
        toString(){},
        toValue(){}
    });
    ```

* **`prototype`对象的`constructor()`属性，直接指向“类”的本身**，这与 ES5 的行为是一致的。

    ```js
    Point.prototype.constructor === Point // true
    ```

* 类的内部所有定义的方法，都是**不可枚举**的（non-enumerable）。

    ```js
    class Point {
        constructor(x, y) {
            // ...
        }

        toString() {
            // ...
        }
    }

    Object.keys(Point.prototype)
    // []
    Object.getOwnPropertyNames(Point.prototype)
    // ["constructor","toString"]
    ```

    上面代码中，toString()方法是Point类内部定义的方法，它是不可枚举的。这一点与 ES5 的行为不一致。

    ```js
    var Point = function (x, y) {
        // ...
    };

    Point.prototype.toString = function () {
        // ...
    };

    Object.keys(Point.prototype)
    // ["toString"]
    Object.getOwnPropertyNames(Point.prototype)
    // ["constructor","toString"]
    ```

    上面代码采用 ES5 的写法，toString()方法就是可枚举的。

### `constructor` 方法

* `constructor()`方法是类的默认方法，通过`new`命令生成对象实例时，自动调用该方法。

    如果没有显式定义，一个空的`constructor()`方法会被默认添加。

* `constructor()`方法默认返回实例对象（即`this`），完全可以指定返回另外一个对象。

    ```js
    class Foo {
        constructor() {
            return Object.create(null);
        }
    }

    new Foo() instanceof Foo
    // false
    ```

    上面代码中，`constructor()`函数返回一个全新的对象，结果导致实例对象不是Foo类的实例。

* 类必须使用`new`调用，否则会报错。这是它跟普通构造函数的一个主要区别，后者不用`new`也可以执行。

### 类的实例

* 生成类的实例的写法，与 ES5 完全一样，也是使用`new`命令。前面说过，如果忘记加上`new`，像函数那样调用Class，将会报错。

* 与 ES5 一样，实例的属性**除非显式定义在其本身（即定义在`this`对象上），否则都是定义在原型上（即定义在`class`上）**。

    ```js
    //定义类
    class Point {

        constructor(x, y) {
            this.x = x;
            this.y = y;
        }

        toString() {
            return '(' + this.x + ', ' + this.y + ')';
        }

    }

    var point = new Point(2, 3);

    point.toString() // (2, 3)

    point.hasOwnProperty('x') // true
    point.hasOwnProperty('y') // true
    point.hasOwnProperty('toString') // false
    point.__proto__.hasOwnProperty('toString') // true
    ```

    上面代码中，x和y都是实例对象point自身的属性（因为定义在`this`对象上），所以`hasOwnProperty()`方法返回`true`，而toString()是原型对象的属性（因为定义在Point类上），所以`hasOwnProperty()`方法返回`false`。这些都与 ES5 的行为保持一致。

* 与 ES5 一样，类的所有实例共享一个原型对象。

    ```js
    var p1 = new Point(2,3);
    var p2 = new Point(3,2);

    p1.__proto__ === p2.__proto__
    //true
    ```

    上面代码中，p1和p2都是Point的实例，它们的原型都是`Point.prototype`，所以`__proto__`属性是相等的。

    这也意味着，**可以通过实例的`__proto__`属性为“类”添加方法**。

    > `__proto__` 并**不是语言本身的特性**，这是各大**厂商具体实现时添加的私有属性**，虽然目前很多现代浏览器的 JS 引擎中都提供了这个私有属性，但依旧**不建议在生产中使用该属性**，避免对环境产生依赖。生产环境中，我们**可以使用 `Object.getPrototypeOf` 方法来获取实例对象的原型**，然后再来为原型添加方法/属性。

    ```js
    var p1 = new Point(2,3);
    var p2 = new Point(3,2);

    p1.__proto__.printName = function () { return 'Oops' };

    p1.printName() // "Oops"
    p2.printName() // "Oops"

    var p3 = new Point(4,2);
    p3.printName() // "Oops"
    ```

    上面代码在p1的原型上添加了一个printName()方法，由于p1的原型就是p2的原型，因此p2也可以调用这个方法。而且，此后新建的实例p3也可以调用这个方法。这意味着，**使用实例的`__proto__`属性改写原型，必须相当谨慎，不推荐使用**，因为这会改变“类”的原始定义，影响到所有实例。

### 取值函数（getter）和存值函数（setter）

* 与 ES5 一样，在“类”的内部可以**使用`get`和`set`关键字，对某个属性设置存值函数和取值函数，拦截该属性的存取行为**。

    ```js
    class MyClass {
        constructor() {
            // ...
        }
        get prop() {
            return 'getter';
        }
        set prop(value) {
            console.log('setter: '+value);
        }
    }

    let inst = new MyClass();

    inst.prop = 123;
    // setter: 123

    inst.prop
    // 'getter'
    ```

    上面代码中，prop属性有对应的存值函数和取值函数，因此赋值和读取行为都被自定义了。

* 存值函数和取值函数是设置在属性的 **`Descriptor` 对象**上的。

    ```js
    class CustomHTMLElement {
        constructor(element) {
            this.element = element;
        }

        get html() {
            return this.element.innerHTML;
        }

        set html(value) {
            this.element.innerHTML = value;
        }
    }

    var descriptor = Object.getOwnPropertyDescriptor(
        CustomHTMLElement.prototype, "html"
    );

    "get" in descriptor  // true
    "set" in descriptor  // true
    ```

    上面代码中，存值函数和取值函数是定义在html属性的描述对象上面，这与 ES5 完全一致。

### 属性表达式

类的属性名，可以采用表达式。

```js
let methodName = 'getArea';

class Square {
  constructor(length) {
    // ...
  }

  [methodName]() {
    // ...
  }
}
```

上面代码中，Square类的方法名getArea，是从表达式得到的。

### Class 表达式

与函数一样，**类也可以使用表达式的形式定义**。

```js
const MyClass = class Me {
  getClassName() {
    return Me.name;
  }
};
```

上面代码使用表达式定义了一个类。需要注意的是，这个类的名字是Me，但是Me**只在 Class 的内部可用**，指代当前类。在 Class 外部，这个类**只能用MyClass引用**。

```js
let inst = new MyClass();
inst.getClassName() // Me
Me.name // ReferenceError: Me is not defined
```

上面代码表示，Me只在 Class 内部有定义。

**如果类的内部没用到的话，可以省略Me**，也就是可以写成下面的形式。

```js
const MyClass = class { /* ... */ };
```

采用 Class 表达式，可以写出**立即执行的 Class**。

```js
let person = new class {
  constructor(name) {
    this.name = name;
  }

  sayName() {
    console.log(this.name);
  }
}('张三');

person.sayName(); // "张三"
```

上面代码中，person是一个立即执行的类的实例。

### 注意点

#### （1）严格模式

**类和模块的内部，默认就是严格模式**，所以不需要使用use strict指定运行模式。只要你的代码写在类或模块之中，就**只有严格模式可用**。考虑到未来所有的代码，其实都是运行在模块之中，所以 ES6 实际上把整个语言升级到了严格模式。

#### （2）不存在提升

类**不存在变量提升（hoist）**，这一点与 ES5 完全不同。

```js
new Foo(); // ReferenceError
class Foo {}
```

上面代码中，Foo类**使用在前，定义在后，这样会报错**，因为 ES6 **不会把类的声明提升到代码头部**。这种规定的原因与下文要提到的继承有关，必须保证子类在父类之后定义。

```js
{
  let Foo = class {};
  class Bar extends Foo {
  }
}
```

上面的代码不会报错，因为Bar继承Foo的时候，Foo已经有定义了。但是，如果存在class的提升，上面代码就会报错，因为**class会被提升到代码头部，而let命令是不提升的**，所以导致Bar继承Foo的时候，Foo还没有定义。

#### （3）`name` 属性

由于本质上，ES6 的类只是 ES5 的构造函数的一层包装，所以函数的许多特性都被Class继承，包括`name`属性。

```js
class Point {}
Point.name // "Point"
```

name属性总是返回紧跟在`class`关键字后面的类名。

#### （4）Generator 方法

如果某个方法之前加上星号（`*`），就表示该方法是一个 Generator 函数。

```js
class Foo {
  constructor(...args) {
    this.args = args;
  }
  * [Symbol.iterator]() {
    for (let arg of this.args) {
      yield arg;
    }
  }
}

for (let x of new Foo('hello', 'world')) {
  console.log(x);
}
// hello
// world
```

上面代码中，Foo类的`Symbol.iterator`方法前有一个星号，表示该方法是一个 Generator 函数。`Symbol.iterator`方法返回一个Foo类的默认遍历器，`for...of`循环会自动调用这个遍历器。

#### （5）`this` 的指向

类的方法内部如果含有`this`，它**默认指向类的实例**。但是，必须非常小心，**一旦单独使用该方法，很可能报错**。

```js
class Logger {
  printName(name = 'there') {
    this.print(`Hello ${name}`);
  }

  print(text) {
    console.log(text);
  }
}

const logger = new Logger();
const { printName } = logger;
printName(); // TypeError: Cannot read property 'print' of undefined
```

上面代码中，printName方法中的`this`，默认指向Logger类的实例。但是，**如果将这个方法提取出来单独使用，`this`会指向该方法运行时所在的环境**（由于 `class` 内部是**严格模式**，所以 `this` 实际**指向的是`undefined`**），从而导致找不到print方法而报错。

**一个比较简单的解决方法是，在构造方法中绑定`this`**，这样就不会找不到print方法了。

```js
class Logger {
  constructor() {
    this.printName = this.printName.bind(this);
  }

  // ...
}
```

**另一种解决方法是使用箭头函数。**

```js
class Obj {
  constructor() {
    this.getThis = () => this;
  }
}

const myObj = new Obj();
myObj.getThis() === myObj // true
```

**箭头函数内部的`this`**总是指向**定义时所在的对象**。上面代码中，箭头函数位于构造函数内部，它的定义生效的时候，是在构造函数执行的时候。这时，箭头函数所在的运行环境，肯定是**实例对象**，所以`this`会总是指向实例对象。

还有一种解决方法是使用`Proxy`，获取方法的时候，自动绑定`this`。

```js
function selfish (target) {
  const cache = new WeakMap();
  const handler = {
    get (target, key) {
      const value = Reflect.get(target, key);
      if (typeof value !== 'function') {
        return value;
      }
      if (!cache.has(value)) {
        cache.set(value, value.bind(target));
      }
      return cache.get(value);
    }
  };
  const proxy = new Proxy(target, handler);
  return proxy;
}

const logger = selfish(new Logger());
```

---


# Class 的继承

## 1. 简介

Class 可以通过`extends`关键字实现继承，这比 ES5 的通过修改原型链实现继承，要清晰和方便很多。

```js
class Point {
}

class ColorPoint extends Point {
}
```

上面代码定义了一个ColorPoint类，该类通过`extends`关键字，继承了Point类的所有属性和方法。但是由于没有部署任何代码，所以这两个类完全一样，等于复制了一个Point类。下面，我们在ColorPoint内部加上代码。

```js
class ColorPoint extends Point {
constructor(x, y, color) {
    super(x, y); // 调用父类的constructor(x, y)
    this.color = color;
}

toString() {
    return this.color + ' ' + super.toString(); // 调用父类的toString()
}
}
```

上面代码中，`constructor`方法和`toString`方法之中，都出现了`super`关键字，它在这里表示**父类的构造函数，用来新建父类的`this`对象**。

**子类必须在`constructor`方法中调用`super`方法**，否则新建实例时会报错。这是因为子类自己的`this`对象，必须先通过父类的构造函数完成塑造，得到与父类同样的实例属性和方法，然后再对其进行加工，加上子类自己的实例属性和方法。如果不调用`super`方法，子类就得不到`this`对象。

```js
class Point { /* ... */ }

class ColorPoint extends Point {
constructor() {
}
}

let cp = new ColorPoint(); // ReferenceError
```

上面代码中，ColorPoint继承了父类Point，但是它的构造函数没有调用`super`方法，导致新建实例时报错。

ES5 的继承，实质是先创造子类的实例对象`this`，然后再将父类的方法添加到`this`上面（`Parent.apply(this)`）。ES6 的继承机制完全不同，实质是**先将父类实例对象的属性和方法，加到`this`上面（所以必须先调用`super`方法）**，然后再用子类的构造函数修改`this`。

**如果子类没有定义`constructor`方法，这个方法会被默认添加**，代码如下。也就是说，不管有没有显式定义，任何一个子类都有`constructor`方法。

```js
class ColorPoint extends Point {
}

// 等同于
class ColorPoint extends Point {
  constructor(...args) {
    super(...args);
  }
}
```

另一个需要注意的地方是，在子类的构造函数中，只有调用`super`之后，才可以使用`this`关键字，否则会报错。这是因为子类实例的构建，基于父类实例，只有`super`方法才能调用父类实例。

```js
class Point {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }
}

class ColorPoint extends Point {
  constructor(x, y, color) {
    this.color = color; // ReferenceError
    super(x, y);
    this.color = color; // 正确
  }
}
```

上面代码中，子类的`constructor`方法没有调用`super`之前，就使用`this`关键字，结果报错，而放在`super`方法之后就是正确的。

下面是生成子类实例的代码。

```js
let cp = new ColorPoint(25, 8, 'green');

cp instanceof ColorPoint // true
cp instanceof Point // true
```

上面代码中，**实例对象cp同时是ColorPoint和Point两个类的实例**，这与 ES5 的行为完全一致。

最后，**父类的静态方法，也会被子类继承**。

```js
class A {
  static hello() {
    console.log('hello world');
  }
}

class B extends A {
}

B.hello()  // hello world
```

上面代码中，hello()是A类的静态方法，B继承A，也继承了A的静态方法。

## 2. `Object.getPrototypeOf()`

`Object.getPrototypeOf`方法可以用来从子类上获取父类。

```js
Object.getPrototypeOf(ColorPoint) === Point
// true
```

因此，可以使用这个方法判断，一个类是否继承了另一个类。

## 类的 `prototype` 属性和`__proto__`属性

大多数浏览器的 ES5 实现之中，每一个**对象**都有`__proto__`属性，指向对应的**构造函数**的`prototype`属性。Class 作为构造函数的语法糖，同时有`prototype`属性和`__proto__`属性，因此同时存在两条继承链。

（1）子类的`__proto__`属性，表示构造函数的继承，总是指向**父类**。

（2）子类`prototype`属性的`__proto__`属性，表示方法的继承，总是指向**父类的`prototype`属性**。

```js
class A {
}

class B extends A {
}

B.__proto__ === A // true
B.prototype.__proto__ === A.prototype // true
```

上面代码中，子类B的`__proto__`属性指向父类A，子类B的`prototype`属性的`__proto__`属性指向父类A的`prototype`属性。

这样的结果是因为，类的继承是按照下面的模式实现的。

```js
class A {
}

class B {
}

// B 的实例继承 A 的实例
Object.setPrototypeOf(B.prototype, A.prototype);

// B 继承 A 的静态属性
Object.setPrototypeOf(B, A);

const b = new B();
```

《对象的扩展》一章给出过`Object.setPrototypeOf`方法的实现。

```js
Object.setPrototypeOf = function (obj, proto) {
  obj.__proto__ = proto;
  return obj;
}
```


---

***`//End of Article`***

---


# 参考文献

* [阮一峰《ECMAScript 6 入门教程》（es6.ruanyifeng.com）](https://es6.ruanyifeng.com/)


![公众号二维码](https://neptliang.github.io/img/Article/WeChatBlog.png)