---
layout:       post
title:        "B站 JavaScript 高级课笔记"
subtitle:     "undefined"
date:         2022-08-31 16:54:32
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - FrontEnd
---


# `0x01` 数据类型

* 类型判断

    * `typeof`

        可以判断`undefined`、`number`、`string`、`boolean`、**`function`**

        **无法区分`null`与`Object`**

        ```JS
        const fn = () => {};
        console.log(typeof fn);     //function
        ```

    * `instanceof`

        判断对象的具体类型，可以判断 **`Array`**、`Function`
        
        ```JS
        const arr = [];
        console.log(arr instanceof Array);      //true

        const fn = () => {};
        console.log(fn instanceof Function);    //true
        ```

    * `===`

        可以判断 **`null`**、`undefined`

        ```JS
        let n = null;
        console.log(typeof n);      //object
        console.log(n === null);    //true
        ```

* `undefined`与`null`的区别是？

    * `undefined`表示变量已定义但**未赋值**
    * `null`表示变量已定义且**已赋值**，值为`null`

* 什么情况下给变量赋`null`

    * **初始化**时赋值，表明将要赋值为对象
    * **代码执行结束前**，让对象成为垃圾对象（被垃圾回收器回收）

---


# `0x02` 数据，变量，内存

* 内存分类

    * **栈**：全局变量、局部变量（空间较小）
    * **堆**：对象（空间较大）

* 数据传递问题

    ```JS
    const obj1 = { name: 'Tom' };
    const obj2 = obj1;

    function fn(obj) {
        obj.name = 'A';
    }

    fn(obj1);
    console.log(obj2.name);     // A
    ```

* 释放内存

    * **全局变量**：不会自动释放
    * **局部变量**：函数执行完后自动释放
    * **对象**：对象没有被引用指向时，成为垃圾对象，再被垃圾回收器回收

    **自动释放≠回收**：
    
    * 为执行函数分配的**栈空间内存**：函数执行完**立即自动释放**
    * 存储对象的**堆空间内存**：由Worker**过一段时间进行一次回收**释放内存（只是时间间隔很短）

    ```JS
    function fn() {
        let b = {};     //“b”这个变量在函数执行完后立即自动释放，而“b”所指向的对象在后面的某个时刻被垃圾回收器回收
    }

    fn();
    ```

---


# `0x03` 对象

* 使用`['属性名']`方式访问属性的场景：

    * 属性名包含特殊字符：`-`或空格等
    * 属性名不确定

---


# `0x04` 函数

* 函数调用方式汇总

    * `fn()`: 直接调用
    * `obj.method()`: 作为方法调用
    * `new Constructor()`: 作为构造函数调用
    * `fn.call(obj)` / `fn.apply(obj)`: 临时作为某个对象的方法进行调用

* 立即执行函数表达式的作用：

    * 隐藏实现
    * **不会污染外部（全局）命名空间**

        ```JS
        (function () {
            const a = 1;
            console.log(a);
        })();

        (() => {
            const a = 2;
            console.log(a);
        })();

        const a = 3;
        console.log(a);
        ```

    * 选择性暴露函数（实现模块化）

        ```JS
        (function () {
            function fn1() {
                console.log(1);
            }
            function fn2() {
                console.log(2);
            }

            window.$ = function () {
                return { fn1 };
            };
        })();

        $().fn1();      // 1
        ```
    
* 不写分号的一个弊端：有时候解析执行会出问题

    在下面2种情况下不加分号会有问题：

    * 小括号开头语句的前一条语句
        
        ```JS
        // 语句后加了分号不报错
        const a = 1;
        (function () {
            console.log(1);     // 1
        })();

        const fn = () => (
            para2 => (
                () => console.log(para2)
            )
        )
        // 本质：解析成了fn()(func...)()
        fn()
        (function () {
            console.log(1)
        })()    // ƒ () { console.log(1) }

        // 语句后不加分号，报错：解析成了const b = 2(func...)()
        const b = 2
        (function () {      // Uncaught TypeError: 2 is not a function
            console.log(1)
        })()
        ```

        这里不写分号的话，解析的时候会把`(function() {`与上面的`const b = 2`连起来，会报错

    * 中括号开头语句的前一条语句

        ```JS
        // 语句后加了分号不报错
        const a = 1;
        [1, 2, 3].forEach(ele => console.log(ele));

        // 本质：解析成了const arr = [null, null, null, [4, 5, 6]][3].forEach(...)
        const arr = [
            null,
            null,
            null,
            [4, 5, 6]
        ]
        [1, 2, 3].forEach(ele => console.log(ele));

        // 语句后不加分号，报错：解析成了const b = 2[3].forEach(...)
        const b = 2
        [1, 2, 3].forEach(ele => console.log(ele));     // Uncaught TypeError: Cannot read properties of undefined (reading 'forEach')
        ```

        这里不写分号的话，解析的时候会把`[1, 2, 3].forEach(...)`与上面的`const b = 2`连起来，会报错

* 函数中的`this`

    * 任何函数本质上都是通过某个对象来调用的，如果没有直接指定就是`window`
    * `this`的值就是调用函数的对象
    * 如何确定`this`的值？
        * `fn()`: `window`
        * `obj.method()`: `obj`
        * `new Constructor()`: 新创建的对象
        * `fn.call(obj)`: `obj`
        * DOM元素的**事件回调**函数中：指向发生事件的**事件源**DOM元素
        * **定时器回调**函数中：`window`

    ```JS
    function Person(name) {
        console.log(this);
        this.name = name;
        this.getName = function () {
            console.log(this);
            return this.name;
        };
        this.setName = function(name) {
            console.log(this);
            this.name = name;
        };
    }

    // 普通函数中，this指向window
    Person('王德发');   // Window {window: Window, self: Window, document: document, name: '', location: Location, …}

    // 构造函数中，this指向新构造的对象
    const p = new Person('侯丽榭');     // Person {}

    // 对象的方法中，this指向对象本身
    p.getName();    // Person {name: '侯丽榭', getName: ƒ, setName: ƒ}

    const obj = { age: 18 };
    p.setName.call(obj, '王德发');      // { age: 18 }
    console.log(obj.name);      // 王德发
    
    // 取出对象的方法后，方法中的this指向window
    const { setName } = p;
    setName('王德发');      // Window {window: Window, self: Window, document: document, name: '王德发', location: Location, …}
    console.log(window.name);   // 王德发
    ```

---


# `0x05` 原型与原型链

## 原型（`prototype`）

* 函数的`prototype`属性

    每个函数都有一个`prototype`属性，它**默认指向一个`Object`空对象**（指没有开发者自行定义的属性），该对象被称为“原型对象”

    原型对象中有一个`constructor`属性，它指向上述函数对象

    ![函数的prototype属性](https://neptliang.github.io/img/Article/js-advansed/原型对象的constructor属性.drawio.png)

* 给构造函数的**原型对象**添加属性（一般都是通过这种方式给实例添加方法）

    作用：该函数构造的所有实例对象都会拥有原型中的属性（包括方法）

    ```JS
    function ObjConstructor() {}
    // 函数的prototype属性默认指向一个Object空对象（指没有开发者自行定义的属性）
    console.log(ObjConstructor.prototype);      // { constructor: ƒ }

    // 构造函数的原型对象中有一个constructor属性，它指向构造函数对象
    console.log(ObjConstructor.prototype.constructor === ObjConstructor);   // true

    // 给构造函数的原型对象添加属性（一般都是通过这种方式给实例添加方法），该构造函数的实例对象可以访问该属性/方法
    ObjConstructor.prototype.test = function () {
        console.log('tested');
    }

    const obj = new ObjConstructor();
    obj.test();     // tested
    ```

## 显式原型与隐式原型

* 每个**函数**都有一个`prototype`，可称为**显式原型（属性）**
* 每个**对象**都有一个`__proto__`，可称为**隐式原型（属性）**
* 实例对象的隐式原型的值为其**对应构造函数**的显式原型的值
* 总结：
    * 函数的 **`prototype`** 属性：
        * 是在**定义函数**时自动添加的
        * 默认值是一个**空`Object`对象**
    * 对象的 **`__proto__`** 属性：
        * 是在**创建对象**时自动添加的
        * 默认值为**其构造函数的`prototype`属性值**
    * 开发者可以直接操作显式原型`prototype`，但不可以直接操作隐式原型`__proto__`（ES6之前）

```JS
function ObjConstructor() {}    // 内部：this.prototype = {}
// 每个函数都有一个prototype，可称为显式原型（属性），默认指向一个空的Object对象
console.log(ObjConstructor.prototype);      // { constructor: ƒ }

const obj = new ObjConstructor();   // 内部：this.__proto__ = ObjConstructor.prototype
// 每个对象都有一个__proto__，可称为隐式原型（属性）
console.log(obj.__proto__);     // { constructor: ƒ }

// 实例对象的隐式原型的值为其对应构造函数的显式原型的值
console.log(ObjConstructor.prototype === obj.__proto__);    // true
```

![内存结构](https://neptliang.github.io/img/Article/js-advansed/显式原型与隐式原型.drawio.png)

* 调用对象上的方法时，先在**对象本身的属性**中找，找不到的话就到**对象的隐式原型（`__proto__`）** 中找

## 原型链

* 访问一个对象的属性的流程：

    * 先在自身属性中查找，找到的话就返回
    * 如果自身没有，则沿着`__proto__`链向上查找，找到的话就返回
    * 如果最终没找到，则返回`undefined`（而非`null`）

* 原型链的别名：隐式原型链

    作用：查找对象的属性（包括方法）

```JS
console.log(Object.prototype);      // { toString : ƒ, hasOwnProperty: ƒ, … }  
console.log(Object.prototype.__proto__);    // null

function ObjConstructor() {
    this.test1 = function () {
        console.log('Test 1');
    };
}

ObjConstructor.prototype.test2 = function () {
    console.log('Test 2');
}

const obj = new ObjConstructor();
obj.test1();    // Test 1
obj.test2();    // Test 2
console.log(obj.toString());    // [object Object]
obj.test3();    // Uncaught TypeError: obj.test3 is not a function
```

![原型链](https://neptliang.github.io/img/Article/js-advansed/原型链.drawio.png)

* 构造函数/原型/实例对象的关系

    * 一个特殊的构造函数——**`Object`构造函数**与其实例、原型对象间的关系

        ```JS
        const obj1 = new Object();
        const obj2 = {};
        ```

        ![构造函数/原型/实例对象的关系](https://neptliang.github.io/img/Article/js-advansed/构造函数:原型:实例对象的关系.drawio.png)

    * 另一个特殊的构造函数——**`Function`构造函数**与`Object()`、其实例、原型对象间的关系

        ```JS
        function ObjConstructor() {}
        const ObjConstructor2 = new Function();
        ```

        ![构造函数/原型/实例对象的关系2](https://neptliang.github.io/img/Article/js-advansed/构造函数:原型:实例对象的关系2.drawio.png)

        所有函数的`__proto__`都是一样的

* 函数的显式原型`prototype`指向的对象默认是空`Object`实例对象（但`Object`构造函数除外）

    ```JS
    function ObjConstructor() {}
    const obj = new ObjConstructor();

    console.log({
        ObjConstructor: ObjConstructor.prototype instanceof Object,
        Function: Function.prototype instanceof Object,
        Object: Object.prototype instanceof Object
    });     // { ObjConstructor: true, Function: true, Object: false }
    ```

    ![构造函数/原型/实例对象的关系3](https://neptliang.github.io/img/Article/js-advansed/构造函数:原型:实例对象的关系3.drawio.png)

* 所有函数都是`Function`构造函数的实例（包括`Function`构造函数本身）

    ```JS
    console.log(Function.__proto__ === Function.prototype);     // true
    ```

* `Object`的原型对象是原型链尽头

    ```JS
    console.log(Object.prototype.__proto__);    // null
    ```

### 属性问题

* **读取**对象的属性值时：**会**自动到原型链中查找

* **设置**对象的属性值时：**不会**查找原型链

    * 如果**当前对象中**没有此属性，直接在对象本身添加此属性并设置其值

    * 如果**对象的原型链上**有此属性，也是在对象本身再添加一份此属性并设置其值

```JS
function ObjConstructor() {}

ObjConstructor.prototype.a = 'xxx'

const obj1 = new ObjConstructor();
const obj2 = new ObjConstructor();

obj2.a = 'yyy';

console.log(obj1.a, obj2.a, { obj1, obj2 });    // xxx yyy { obj1: { [[Prototype]]: { a: "xxx" } }, obj2: { a: "yyy", [[Prototype]]: { a: "xxx" } } }
```

* **方法**一般定义在**原型**中，**属性**一般通过构造函数定义在**对象本身**上

    ```JS
    function Person(name, age) {
        // 属性一般通过构造函数定义在对象本身上
        this.name = name;
        this.age = age;
    }

    // 方法一般定义在原型中
    Person.prototype.setName = function (name) {
        this.name = name;
    };

    const wong = new Person('王德发', 20);
    wong.setName('侯丽榭');
    console.log(wong);      // { name: '侯丽榭', age: 20, [[Prototype]]: { setName: ƒ } }

    const jason = new Person('黄仁勋', 18);
    jason.setName('苏姿丰');
    console.log(jason);     // { name: '苏姿丰', age: 18, [[Prototype]]: { setName: ƒ } }

    console.log(wong.__proto__ === jason.__proto__);    // true
    ```

## `instanceof`

* `instanceof`是如何判断的？

    对于表达式 `a instanceof B`：

    如果函数B的**显式原型对象 `prototype`** 在a对象的**原型链**上，则返回`true`，否则返回`false`

    ```JS
    function ObjConstructor() {}
    const obj = new ObjConstructor();

    console.log({
        ObjConstructor: obj instanceof ObjConstructor,
        Object: obj instanceof Object
    });     // { ObjConstructor: true, Object: true }
    ```

    ![instanceof解析](https://neptliang.github.io/img/Article/js-advansed/instanceof.drawio.png)

* 
    ```JS
    function ObjConstructor() {};

    console.log({
        obj_fn: Object instanceof Function,
        fn_obj: Function instanceof Object,
        obj_obj: Object instanceof Object,
        fn_fn: Function instanceof Function,
        obj_ObjCon: Object instanceof ObjConstructor
    });     // { obj_fn: true, fn_obj: true, obj_obj: true, fn_fn: true, obj_ObjCon: false }
    ```
    
    ![instanceof解析2.1](https://neptliang.github.io/img/Article/js-advansed/instanceof2.1.drawio.png)
    
    所有函数的`prototype`原型对象默认都是`Object`的空实例，但`Object`构造函数的`prototype`原型对象除外，因为`Object`构造函数是`Function`的实例

## 例题

*    
    ```JS
    function ObjConstructor() {}

    ObjConstructor.prototype.n = 1;
    const obj1 = new ObjConstructor();

    ObjConstructor.prototype = {
        n: 2,
        m: 3
    };
    const obj2 = new ObjConstructor();

    console.log(obj1.n, obj1.m, obj2.n, obj2.m);    // 1 undefined 2 3
    ```

    ![原型与原型链例题](https://neptliang.github.io/img/Article/js-advansed/原型与原型链例题.drawio.png)

* 
    ```JS
    function ObjConstructor() {}

    Object.prototype.a = function () {
        console.log('aa');
    }

    Function.prototype.b = function () {
        console.log('bb');
    }

    const obj = new ObjConstructor();
    ObjConstructor.a();     // aa
    ObjConstructor.b();     // bb
    obj.a();    // aa
    obj.b();    // Uncaught TypeError: obj.b is not a function
    ```


---

***`//End of Article`***

---


![公众号二维码](https://neptliang.github.io/img/Article/WeChatBlog.png)