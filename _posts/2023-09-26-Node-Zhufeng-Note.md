---
layout:       post
title:        "珠峰Node笔记"
subtitle:     "undefined"
date:         2023-09-26 11:05:43
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - FrontEnd
---


上课时间：

周三、周五晚<br/>（各2h） | 周日<br/>（2.5+3h）
:--------------------: | :---------:
8~10点                   | 上午9:30~12:00<br/> 下午2:00~5:00

实现Promise：[https://promisesaplus.com](https://promisesaplus.com)

# 关于函数

什么是高阶函数：把函数作为参数或者返回值的一类函数。

## before函数

```JS
Function.prototype.before = function(beforeFn) {
    return () => {
        beforeFn();
        this();
    }
}

function fn() {
    console.log('source');
}
const newFn = fn.before(say => {
    console.log('before');
});
newFn();
```

> VS Code 插件：Code Runner（作者：Jun Han）

### 高阶函数的应用

#### 什么是高阶函数

1. 一个函数**返回一个函数**，那么这个函数就是高阶函数
2. 一个函数的**参数是一个函数**，这个函数也是高阶函数

#### 应用

1. 我们可以**通过包装一个函数，对现有的逻辑进行扩展**

    ```JS
    Function.prototype.before = function(callback) {      // 2. 一个函数的参数是一个函数，这个函数也是高阶函数
        // 箭头函数的特点：1. 没有this；2. 没有arguments；3. 没有prototype
        // this = core
        return (...args) => {   // 1. 一个函数返回一个函数，那么这个函数就是高阶函数
            callback();
            this(...args);
        }
    }

    function core(a, b, c) {
        console.log('核心代码', a, b, c);
    }
    let newCore = core.before(function() {
        console.log('before');
    });
    newCore(1, 2, 3);   // 对原来的方法进行了扩展
    ```

2. **函数参数的预置**：对函数的参数进行保留（闭包）

    > 闭包：函数定义所在的作用域和执行的作用域不是同一个，此时就会产生闭包（个人认为相对比较靠谱的一个定义，《你不知道的 JavaScript》里也用的这个）

    函数的柯理化、偏函数都是基于高阶函数来实现的

    示例：
    
    * 判断类型
        
        判断类型有4种常见的方式：

        * `typeof`: 可以判断基本类型，存在的问题是 `typeof null === 'object'`
        * `instanceof`: 可以判断某个类型是否属于谁的实例
        * `Object.prototype.toString`: 麻烦之处在于需要在对象的原型中找到方法
        * `constructor`: 判断类型时相对比较少用，优点是可以方便地取到对象对应的构造函数来使用而无需判断类型：`new [].constructor` -> `new Array`, `new {}.constructor` -> `new Object`
        
        ```JS
        // 将参数预置到函数内部
        function isType(typing) {
            return function(val) {
                return Object.prototype.toString.call(val) === `[object ${typing}]`;
            }
        }

        let utils = {}; //这里的分号不能少，否则报错
        ['Number', 'Boolean', 'String'].forEach(type => {
            utils[`is${type}`] = isType(type);
        });

        // isType的范围比较大 -> 小范围的isNumber（即函数柯理化：将范围具体化，可以实现批量传递参数 通用的函数柯理化的实现）
        console.log(utils.isNumber(123));
        console.log(utils.isNumber('abc'));
        ```

        函数反柯理化：`Object.prototype.toString.call() -> toString()`

### 高阶函数解决异步问题

示例：文件读取

```JS
const fs = require('fs');   // 文件系统模块（file system）
const path = require('path');   // 路径模块，进行路径操作

function after(times, callback) {
    let obj = {};
    return function (key, value) {
        obj[key] = value;
        if (--times == 0) {
            callback(obj);
        }
    }
}

let callback = after(2, data => {
    console.log(data);
});
// fs.readFile() 默认path.resolve() 会根据执行的路径来解析绝对路径
// node中的异步api，回调的第一个参数都是error-first
fs.readFile(path.resolve(__dirname, 'a.txt'), 'utf-8', function(err, data) {
    callback('msg', data);
});
fs.readFile(path.resolve(__dirname, 'b.txt'), 'utf-8', function(err, data) {
    callback('age', data);
});

// 例如前端并发ajax，我们需要等待多个异步请求都完成后，将结果拿到并渲染页面
// 同步“异步回调的执行结果”

// 异步方法在处理错误的情况时，必须通过回调的参数来处理
```

**缺点**：只有在两个异步过程都完成后才能得到结果，但是**无法监控中间过程**

**改进方案：发布订阅**

### 发布订阅模式

* 发布：触发功能
* 订阅：订阅一些功能

```JS
const fs = require('fs');
const path = require('path');

let events = {
    _obj: {},
    _arr: [],   // 订阅中心，将所有的事件都订阅到数组中
    on(callback) {
        this._arr.push(callback);
    },
    emit(key, value) {      // 事件发生后，来依次触发回调
        this._obj[key] = value;
        this._arr.forEach(callback => callback(this._obj));
    }
}

events.on(() => {
    console.log('每次读取完毕后都会触发');
});
events.on(data => {
    if (Reflect.ownKeys(data).length === 2) {
        console.log('数据全部读取完毕');
    }
});
fs.readFile(path.resolve(__dirname, 'a.txt'), 'utf-8', function(err, data) {
    events.emit('msg', data);
});
fs.readFile(path.resolve(__dirname, 'b.txt'), 'utf-8', function(err, data) {
    events.emit('age', data);
});
```

通过发布订阅来实现解耦合，灵活，但是触发是**需要用户自己触发**的

而**观察者模式**是基于发布订阅模式的，但是**数据变化后可以自动通知触发发布**（Vue 响应式原理）

### 观察者模式

* **发布订阅**：用户要手动订阅、手动发布
* **观察者模式**：基于发布订阅，但是**主动**的，状态变化->主动通知

```JS
// 被观察者
class Observable {
    observers = [];
    state = null;

    attach(observer) {
        this.observers.push(observer);      //订阅
    }

    setState(state) {
        this.state = state;
        // 状态变化后，会通知观察者更新，将自己传过去
        this.observers.forEach(observer => observer.update(this));      //发布
    }
}

class Observer {    //观察者
    constructor(name) {
        this.name = name;
    }
    update(observable) {
        console.log(`${this.name}: ${observable.state}`);
    }
}
const observer1 = new Observer('小明');
const observer2 = new Observer('小红');

// 创建被观察者
const subject = new Observable();   
// 订阅
subject.attach(observer1);
subject.attach(observer2);

subject.setState('被打了');
```

### promise

* promise 是干什么的，解决的问题是什么？
    1. 回调写起来不好看（难以维护），“恶魔金字塔”（层层嵌套），嵌套逻辑不优雅（解决方案：链式调用、`then`）
    2. 错误处理无法统一，我们需要处理公共的错误逻辑（解决方案：`catch`）
    3. 尽量简化回调：即多个异步并发问题（解决方案：`Promise.all`、`Promise.finally`）

* promise 依旧是基于回调的，可能还是会有嵌套问题

* promise 是一个构造函数，默认需要传入一个 **executor（执行器）**。

    executor 会**立刻执行（同步）**，并且传入 resolve 和 reject 两个参数

* promise 有**三个状态**：
    * **fulfilled**: 成功
    * **reject**: 拒绝态
    * **pending**: 等待态（默认是等待态）

* 每个 promise 都有一个 `then` 方法，用来访问成功时的值和失败的原因

* 可以通过 resolve 和 reject 来改变状态，同时调用对应的回调，一个 promise 实例状态变化后，不能再重新地发生变化

* **executor 发生异常的时候，也会导致 promise 的失败**

```JS
// const Promise = require('./promise')    //导入下文自己实现的 Promise
const promise = new Promise((resolve, reject) => {
    // throw new Error('出错了');      //executor 发生异常的时候，也会导致promise的失败
    // resolve('ok');      
    // reject('no ok');    //一个promise实例状态变化后，不能再重新地发生变化
    // console.log('Executor');    //executor里的是同步代码

    setTimeout(() => {
        resolve();
    }, 1000);
});

promise.then(
    data => console.log(`Succeeded 1: ${data}`),
    reason => console.log(`Failed: ${reason}`)
);
promise.then(
    data => console.log(`Succeeded 2: ${data}`),
    reason => console.log(`Failed: ${reason}`)
);

console.log(1);
```

#### 实现 `Promise`

即上面代码块开头导入的 promise.js:

```JS
const PENDING = 'PENDING';
const FULFILLED = 'FULFULLED';
const REJECTED = 'REJECTED';

class Promise {
    // Promise 实例的状态默认是等待态
    status = PENDING;
    // 成功时的值
    value = undefined;
    // 失败时的原因
    reason = undefined;
    /* promise 调用 then 的时候，可能状态依旧是 pending，那么我们需要将回调先存放起来，
        * 等待过一会调用 resolve 时触发 onFulfilledList 执行
        * 等待过一会调用 reject 时触发 onRejectedList 执行
    */
    onFulfilledList = [];
    onRejectedList = [];

    constructor(executor) {
        const resolve = value => {
            // 只有状态为 pending 的时候，才允许修改状态、改变成功和失败的原因
            if (this.status !== PENDING) { return; }
            this.value = value;
            this.status = FULFILLED;
            // this.onFulfilledList.reduce(this.value, (prev, next) => next(prev));
            // Promise 成功时调用成功的回调
            this.onFulfilledList.forEach(cb => cb());
        }
        const reject = reason => {
            if (this.status !== PENDING) { return; }
            this.reason = reason;
            this.status = REJECTED;
            // this.onRejectedList.reduce(this.reason, (prev, next) => next(prev));
            // Promise 失败时调用失败的回调
            this.onRejectedList.forEach(cb => cb());
        }
        // 异常处理：executor 内抛出异常时，promise 走向 reject 并传出异常对象作为 reason
        try {
            // 调用 executor 时自动传入 resolve 和 reject
            executor(resolve, reject);
        } catch (e) {
            reject(e);
        }
    }

    then(onFulfilled, onRejected) {
        // 1. 如果调用 then 时，promise 已经确定了是成功还是失败了：
        if (this.status === FULFILLED) {
            // ToDo...
            onFulfilled(this.value)
        }
        if (this.status === REJECTED) {
            // ToDo...
            onRejected(this.reason);
        }
        // 2. 如果调用 then 时，promise 还没确定是成功还是失败：
        if (this.status === PENDING) {
            this.onFulfilledList.push(() => {
                // ToDo...
                onFulfilled(this.value);
            });
            this.onRejectedList.push(() => {
                // ToDo...
                onRejected(this.reason);
            });
        }
    }
}

module.exports = Promise;
```

#### 封装构造 Promise 操作

* 传统回调方式示例：

    ```JS
    const fs = require('fs');   // 文件系统模块（file system）
    const path = require('path');   // 路径模块，进行路径操作

    // 回调方式难对付的场景：上一个异步操作的输出是下一个异步操作的输入
    fs.readFile(path.resolve(__dirname, 'a.txt'), 'utf8', (err, data) => {      //从 a.txt 读取下一步要读取的文件的路径
        if (err) { return console.log(err); }   //回调方式的问题之一：异常处理不便
        fs.readFile(data, 'utf8', (err, data) => {
            if (err) { return console.log(err); }   //回调方式的问题之一：异常处理不便
            console.log(data);
        })
    })
    ```

* 封装操作如下：

    ```JS
    const fs = require('fs');
    const path = require('path');
    // const { promisify } = require('util');      //实现的是这个
    
    // 将node中的异步api转换成promise的形式（要求遵循error- first）
    function promisify(fn) {
        return function(...args) {
            return new Promise((resolve, reject) => {
                fn(...args, function(error, data) {
                    if (error) { return reject(error); }
                    resolve(data);
                });
            });
        }
    }

    //test
    const readFile = promisify(fs.readFile);
    // 1. then链的特点：当then中成功和失败的回调函数返回的是一个promise，内部会解析这个promise，并且将结果传递到外层的下一个then中
    // 2. 下一次then走成功还是失败，取决于当前promise的状态
    // 3. 如果成功和失败返回的不是一个promise，那么这个结果会直接传递到下一个then的成功
    // 4. 如果成功和失败的回调中抛出异常了，则会执行下一个then的失败
    readFile(path.resolve(__dirname, 'a.txt'), 'utf8').then(data => {
        return readFile(data + 1, 'utf8');      // 返回的promise是成功则走到下一个then的成功，如果是失败则走到下一个then的失败
    }).then(data => {
        console.log('Succeeded:', data);
    }, err => {
        console.log('Failed:', err);
        // return undefined;
        return true;
    }).then(data => {
        console.log('Succeeded:', data);
        throw new Error('出错');
    }).then(() => {}, err => {
        console.log(err);
    });
    ```

    让一个 promise（then）变成失败有两种方式：
    1. 抛出异常；
    2. 返回一个失败的promise。


---

***`//End of Article`***

---


![公众号二维码](https://neptliang.github.io/img/Article/WeChatBlog.png)