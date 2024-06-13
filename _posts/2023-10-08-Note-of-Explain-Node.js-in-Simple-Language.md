---
layout:       post
title:        "《深入浅出Node.js》笔记"
subtitle:     "undefined"
date:         2023-10-08 16:20:12
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - FrontEnd
---


# 《深入浅出Node.js》

**朴灵**

610个笔记


# 第2章 模块机制

## 2.1 CommonJS规范

>> 在模块中，存在一个 **`module` 对象**，它代表模块自身

>> 一个文件就是一个模块，将方法**挂载在`exports`对象上**作为属性即可定义导出的方式

## 2.2 Node的模块实现

> 模块分为两类：
> 1. Node提供的模块，称为**核心模块**；
> 2. 用户编写的模块，称为**文件模块**

> 模块标识符在Node中主要分为以下几类。
> * **核心模块**，如`http`、`fs`、`path`等。
> * `.`或`..`开始的**相对路径文件模块**。
> * 以`/`开始的**绝对路径文件模块**。
> * **非路径形式的文件模块**，如自定义的`connect`模块。

>> 如果是.node和.json文件，在传递给`require()`的标识符中**带上扩展名，会加快一点速度**

>> `require()`通过分析文件扩展名之后，可能没有查找到对应文件，但却得到一个目录，这在引入自定义模块和逐个模块路径进行查找时经常会出现，此时Node**会将目录当做一个包来处理**

>> package.json: **CommonJS包规范定义**的包描述文件

## 2.6 包与NPM

> **CommonJS为package.json文件定义了如下一些必需的字段。**
> * `name`。包名。
> * `description`。包简介。
> * `version`。版本号。http://semver.org/
> * `keywords`。关键词数组，NPM中主要用来做分类搜索。
> * `maintainers`。包维护者列表。

>> CommonJS包规范是**理论**，NPM是其中的一种**实践**。

>> 需要注意的是，全局模式并不是将一个模块包安装为一个全局包的意思，它并不意味着可以从任何地方通过require()来引用到它

>> 全局模式这个称谓其实并不精确，存在诸多误导。实际上，**`-g`是将一个包安装为全局可用的可执行命令**。它根据包描述文件中的bin字段配置，将实际脚本链接到与Node可执行文件相同的路径下

>> 对于一些没有发布到NPM上的包，或是因为网络原因导致无法直接安装的包，可以通过将包下载到本地，然后以本地安装

>> 如果不能通过官方源安装，可以**通过镜像源安装**。在执行命令时，添加`--registry=http://registry.url`即可

>> package.json中`scripts`字段的提出就是让包在安装或者卸载等过程中提供**钩子机制**

>> **一个优秀的包应当包含测试用例，并在package.json文件中配置好运行测试的命令**

>> 一个没有单元测试的包基本上是无法被信任的

> 虽然不够精确但是有效。总体而言，符合Kwalitee的模块要满足的条件与上述提及的考查点大致相同。
> * 具备良好的**测试**。
> * 具备良好的**文档**（README、API）。
> * 具备良好的**测试覆盖率**。
> * 具备良好的**编码规范**。
> * 更多条件。

## 2.7 前后端共用模块

>> 鉴于网络的原因，**CommonJS为后端JavaScript制定的规范并不完全适合前端的应用场景**。经过一段争执之后，AMD规范最终在前端应用场景中胜出


# 第3章 异步I/O

## 3.2 异步I/O实现现状

>> Brendan Eich援引18世纪英国文学家约翰逊所说，“它的优秀之处并非原创，它的原创之处并不优秀”，以之评价他自己创造的JavaScript

## 3.4 非I/O的异步API

>> **定时器的问题在于，它并非精确的（在容忍范围内）。** 尽管事件循环十分快，但是如果某一次循环占用的时间较多，那么下次循环时，它也许已经超时很久了。譬如通过`setTimeout()`设定一个任务在10毫秒后执行，但是在9毫秒后，有一个任务占用了5毫秒的CPU时间片，再次轮到定时器执行时，时间就已经过期4毫秒。

* 由于事件循环自身的特点，定时器的精确度不够。而事实上，采用定时器需要动用红黑树，创建定时器对象和迭代等操作，而`setTimeout(fn, 0)`的方式较为浪费性能。

* **`process.nextTick()`方法的操作相对较为轻量**，具体代码如下：

    ```JS
    process.nextTick = function(callback) {
        // on the way out, don't bother.
        // it won't get fired anyway
        if (process._exiting) return;

        if (tickDepth >= process.maxTickDepth)
            maxTickWarn();

        var tock = { callback: callback };
        if (process.domain) tock.domain = process.domain;
        nextTickQueue.push(tock);
        if (nextTickQueue.length) {
            process._needTickCallback();
        }
    };
    ```


# 第4章 异步编程

## 4.1 函数式编程

>> **高阶函数**是可以把函数作为参数，或是将函数作为返回值的函数

>> 高阶函数可以将函数作为输入或返回值的变化看起来虽细小，但是对于C/C++语言而言，通过指针也可以达到相同的效果

> **后续传递风格（Continuation Passing Style）** 的程序编写将函数的业务重点从返回值转移到了回调函数中：
> ```JS
> function foo(x, bar) {
>     return bar(x);
> }
> ```

>> 事件的处理方式正是基于高阶函数的特性来完成的

>> 通过指定部分参数来产生一个新的定制函数的形式就是偏函数

## 4.2 异步编程的优势与难点

>> **尝试对异步方法进行try/catch操作只能捕获当次事件循环内的异常，对callback执行时抛出的异常将无能为力**

> **Node在处理异常上形成了一种约定，将异常作为回调函数的第一个实参传回，如果为空值，则表明异步调用没有异常抛出**：
> ```JS
> async(function (err, results) {
>     // TODO
> });
> ```

> **在我们自行编写的异步方法上，也需要去遵循这样一些原则：**
> 1. 必须执行调用者传入的**回调函数**；
> 2. 正确传递回**异常**供调用者判断。

> 在异步方法的编写中，另一个容易犯的错误是对用户传递的回调函数进行异常捕获，示例代码如下：
> ```JS
> try {
>     req.body = JSON.parse(buf, options.reviver);
>     callback();
> } catch (err){
>     err.body = buf;
>     err.status = 400;
>     callback(err);
> }
> ```
> 在编写异步方法时，只要将异常正确地传递给用户的回调方法即可，无须过多处理。

## 4.3 异步编程解决方案

>> 目前，异步编程的主要解决方案有如下3种。
>> * 事件发布/订阅模式。
>> * Promise/Deferred模式。
>> * 流程控制库。

### 4.3.1 事件发布/订阅模式

>> 事件监听器模式是一种广泛用于异步编程的模式，是回调函数的事件化，**又称发布/订阅模式**。

>> Node自身提供的`events`模块（[http://nodejs.org/docs/latest/api/events.html](http://nodejs.org/docs/latest/api/events.html)）是发布/订阅模式的一个简单实现，Node中部分模块都继承自它

#### 1．继承events模块

>> 以下代码是Node中`Stream`对象继承`EventEmitter`的例子：
>> ```JS
>> var events = require('events');
>> 
>> function Stream() {
>>     events.EventEmitter.call(this);
>> }
>> util.inherits(Stream, events.EventEmitter);
>> ```
>> Node在`util`模块中封装了继承的方法，所以此处可以很便利地调用。

#### 2．利用事件队列解决雪崩问题

>> 在事件订阅/发布模式中，通常也有一个`once()`方法，**通过它添加的侦听器只能执行一次**，在执行之后就会将它与事件的关联移除。这个特性常常可以帮助我们过滤一些重复性的事件响应。

>> 雪崩问题，就是在高访问量、大并发量的情况下**缓存失效**的情景

>> 以下是一条数据库查询语句的调用：
>> ```JS
>> var select = function (callback) {
>>     db.select("SQL", function (results) {
>>         callback(results);
>>     });
>> };
>> ```
>>
>> 如果站点刚好启动，这时缓存中是不存在数据的，而如果访问量巨大，同一句SQL会被发送到数据库中反复查询，会影响服务的整体性能。一种改进方案是添加一个状态锁，相关代码如下：
>> ```JS
>> var status = "ready";
>> var select = function (callback) {
>>     if (status === "ready") {
>>         status = "pending";
>>         db.select("SQL", function (results) {
>>             status = "ready";
>>             callback(results);
>>         });
>>     }
>> };
>> ```
>>
>> 但是在这种情景下，连续地多次调用`select()时`，只有第一次调用是生效的，后续的`select()`是没有数据服务的，这个时候可以引入事件队列，相关代码如下：
>> ```JS
>> var proxy = new events.EventEmitter();
>> var status = "ready";
>> var select = function (callback) {
>>     proxy.once("selected", callback);
>>     if (status === "ready") {
>>         status = "pending";
>>         db.select("SQL", function (results) {
>>             proxy.emit("selected", results);
>>             status = "ready";
>>         });
>>     }
>> };
>> ```
>>
>> 这里我们利用了`once()`方法，将所有请求的回调都压入事件队列中，利用其执行一次就会将监视器移除的特点，保证每一个回调只会被执行一次
>> 
> **2023/08/09发表想法**  
>
> 每次`once`都入队一个回调，一旦`emit`就会执行全部回调并移除之：
> ```JS
> > const p = new events.EventEmitter();
> > p.once('selected', console.log)
> > p.once('selected', console.log)
> > p.emit('selected', 1)
> 1
> 1
> > p.emit('selected', 1)
> >
> ```

#### 3．多异步之间的协作方案

>> 由于多个异步场景中回调函数的执行并不能保证顺序，且回调函数之间互相没有任何交集，所以需要借助一个第三方函数和第三方变量来处理异步协作的结果。通常，我们把这个用于检测次数的变量叫做**哨兵变量**。

>> 你也许已经想到利用偏函数来处理哨兵变量和第三方函数的关系了，相关代码如下：
>> ```JS
>> var after = function (times, callback) {
>>     var count = 0, results = {};
>>     return function (key, value) {
>>         results[key] = value;
>>         count++;
>>         if (count === times) {
>>             callback(results);
>>         }
>>     };
>> };
>> 
>> var done = after(times, render);
>> ```
>>
>> 上述方案实现了多对一的目的。如果业务继续增长，我们依然可以继续利用发布/订阅方式来完成多对多的方案，相关代码如下：
>> ```JS
>> var emitter = new events.Emitter();
>> var done = after(times, render);
>> 
>> emitter.on("done", done);
>> emitter.on("done", other);
>> 
>> fs.readFile(template_path, "utf8", function (err, template) {
>>     emitter.emit("done", "template", template);
>> });
>> db.query(sql, function (err, data) {
>>     emitter.emit("done", "data", data);
>> });
>> l10n.get(function (err, resources) {
>>     emitter.emit("done", "resources", resources);
>> });
>> ```
>> 这种方案结合了前者用简单的偏函数完成多对一的收敛和事件订阅/发布模式中一对多的发散。

>> 在上面的方法中，有一个令调用者不那么舒服的问题，那就是调用者要去准备这个`done()`函数，以及在回调函数中需要从结果中把数据一个一个提取出来，再进行处理。另一个方案则是来自笔者自己写的EventProxy模块，它是对事件订阅/发布模式的扩充，可以自由订阅组合事件。

### 4.3.2 Promise/Deferred模式

>> 使用事件的方式时，执行流程需要被预先设定。即便是分支，也需要预先设定，这是由发布/订阅模式的运行机制所决定的。下面为普通的Ajax调用：
>> ```JS
>> $.get('/api', {
>>     success: onSuccess,
>>     error: onError,
>>     complete: onComplete
>> });
>> ```

>> 是否有一种先执行异步调用，延迟传递处理的方式呢？答案是Promise/Deferred模式。Promise/Deferred模式在JavaScript框架中最早出现于Dojo的代码中，被广为所知则来自于jQuery 1.5版本，该版本几乎重写了Ajax部分，使得调用Ajax时可以通过如下的形式进行：
>> ```JS
>> $.get('/api')
>>     .success(onSuccess)
>>     .error(onError)
>>     .complete(onComplete);
>> ```

>> **在原始的API中，一个事件只能处理一个回调，而通过Deferred对象，可以对事件加入任意的业务处理逻辑**，示例代码如下：
>> ```JS
>> $.get('/api')
>>     .success(onSuccess1)
>>     .success(onSuccess2);
>> ```

>> CommonJS草案目前已经抽象出了Promises/A、Promises/B、Promises/D这样典型的异步Promise/Deferred模型

>> 一旦出现深度的嵌套，就会让编程的体验变得不愉快，而Promise/Deferred模式在一定程度上**缓解**了这个问题

>> 这里我们将着重介绍Promises/A来以点代面介绍Promise/Deferred模式

#### 1. Promises/A

>> Promises/A提议对单个异步操作做出了这样的抽象定义，具体如下所示。
>> * Promise操作**只会处在3种状态的一种**：未完成态、完成态和失败态。
>> * Promise的**状态只会出现从未完成态向完成态或失败态转化**，不能逆反。完成态和失败态不能互相转化。
>> * Promise的**状态一旦转化，将不能被更改**。

>> 在API的定义上，Promises/A提议是比较简单的。一个Promise对象只要具备`then()`方法即可。但是对于`then()`方法，有以下简单的要求。
>> * 接受完成态、错误态的回调方法。在操作完成或出现错误时，将会调用对应方法。
>> * 可选地支持progress事件回调作为第三个方法。
>> * `then()`方法只接受`function`对象，其余对象将被忽略。
>> * `then()`方法**继续返回Promise对象**，以实现链式调用。

>> 为了演示Promises/A提议，这里我们尝试通过继承Node的`events`模块来完成一个简单的实现，相关代码如下：
>> ```JS
>> var Promise = function () {
>>     EventEmitter.call(this);
>> };
>> util.inherits(Promise, EventEmitter);
>> 
>> Promise.prototype.then = function (fulfilledHandler, errorHandler, progressHandler) {
>>     if (typeof fulfilledHandler === 'function') {
>>         // 利用once()方法，保证成功回调只执行一次
>>         this.once('success', fulfilledHandler);
>>     }
>>     if (typeof errorHandler === 'function') {
>>         // 利用once()方法，保证异常回调只执行一次
>>         this.once('error', errorHandler);
>>     }
>>     if (typeof progressHandler === 'function') {
>>         this.on('progress', progressHandler);
>>     }
>>     return this;
>> };
>> ```
>> 这里看到`then()`方法所做的事情是将回调函数存放起来。为了完成整个流程，还需要触发执行这些回调函数的地方，实现这些功能的对象通常被称为Deferred，即延迟对象，示例代码如下：
>> ```JS
>> var Deferred = function () {
>>     this.state = 'unfulfilled';
>>     this.promise = new Promise();
>> };
>> 
>> Deferred.prototype.resolve = function (obj) {
>>     this.state = 'fulfilled';
>>     this.promise.emit('success', obj);
>> };
>> 
>> Deferred.prototype.reject = function (err) {
>>     this.state = 'failed';
>>     this.promise.emit('error', err);
>> };
>> 
>> Deferred.prototype.progress = function (data) {
>>     this.promise.emit('progress', data);
>> };
>> ```
>>
>> 这里的状态和方法之间的对应关系如图4-5所示。利用Promises/A提议的模式，我们可以对一个典型的响应对象进行封装，相关代码如下：
>> ```JS
>> res.setEncoding('utf8');
>> res.on('data', function (chunk) {
>>     console.log('BODY: ' + chunk);
>> });
>> res.on('end', function () {
>>     // Done
>> });
>> res.on('error', function (err) {
>>     // Error
>> });
>> 上述代码可以转换为如下的简略形式：
>> res.then(function () {
>>     // Done
>> }, function (err) {
>>     // Error
>> }, function (chunk) {
>>     console.log('BODY: ' + chunk);
>> });
>> ```
>>
>> 要实现如此简单的API，只需要简单地改造一下即可，相关代码如下：
>> ```JS
>> var promisify = function (res) {
>>     var deferred = new Deferred();
>>     var result = '';
>>     res.on('data', function (chunk) {
>>         result += chunk;
>>         deferred.progress(chunk);
>>     });
>>     res.on('end', function () {
>>         deferred.resolve(result);
>>     });
>>     res.on('error', function (err) {
>>         deferred.reject(err);
>>     });
>>     return deferred.promise;
>> };
>> ```
>> 如此就得到了简单的结果。这里返回`deferred.promise`的目的是为了不让外部程序调用`resolve()`和`reject()`方法，更改内部状态的行为交由定义者处理。下面为定义好Promise后的调用示例：
>> ```JS
>> promisify(res).then(function () {
>>     // Done
>> }, function (err) {
>>     // Error
>> }, function (chunk) {
>>     // progress
>>     console.log('BODY: ' + chunk);
>> });
>> ```

>> Deferred主要是用于内部，用于**维护异步模型的状态**；Promise则作用于外部，**通过`then()`方法暴露给外部以添加自定义逻辑**

>> 与事件发布/订阅模式相比，Promise/Deferred模式的API接口和抽象模型都十分简洁。从图4-6中也可以看出，它将业务中不可变的部分封装在了Deferred中，将可变的部分交给了Promise

>> Promise是高级接口，事件是低级接口。低级接口可以构成更多更复杂的场景，高级接口一旦定义，不太容易变化，不再有低级接口的灵活性，但对于解决典型问题非常有效

>> Promises/A的模型抽象在几种Promise提议中相对简洁

#### 2. Promise中的多异步协作

>> 当我们需要处理多个异步调用时，又该如何处理呢？
>>
>> 类似于EventProxy，这里给出了一个简单的原型实现，相关代码如下：
>> ```JS
>> Deferred.prototype.all = function (promises) {
>>     var count = promises.length;
>>     var that = this;
>>     var results = [];
>>     promises.forEach(function (promise, i) {
>>         promise.then(function (data) {
>>             count--;
>>             results[i] = data;
>>             if (count === 0) {
>>                 that.resolve(results);
>>             }
>>         }, function (err) {
>>             that.reject(err);
>>         });
>>     });
>>     return this.promise;
>> };
>> ```

#### 3. Promise的进阶知识

>> 在有一组纯异步的API，为了完成一串事情，我们的代码大致如下：
>> ```JS
>> obj.api1(function (value1) {
>>     obj.api2(value1, function (value2) {
>>         obj.api3(value2, function (value3) {
>>             obj.api4(value3, function (value4) {
>>                 callback(value4);
>>             });
>>         });
>>     });
>> });
>> ```
>
>> 对于喜欢利用事件的开发者，我们展开后的代码又将会是怎样的情况呢？具体如下所示：
>> ```JS
>> var emitter = new event.Emitter();
>> 
>> emitter.on("step1", function () {
>>     obj.api1(function (value1) {
>>         emitter.emit("step2", value1);
>>     });
>> });
>> 
>> emitter.on("step2", function (value1) {
>>     obj.api2(value1, function (value2) {
>>         emitter.emit("step3", value2);
>>     });
>> });
>> 
>> emitter.on("step3", function (value2) {
>>     obj.api3(value2, function (value3) {
>>         emitter.emit("step4", value3);
>>     });
>> });
>> 
>> emitter.on("step4", function (value3) {
>>     obj.api4(value3, function (value4) {
>>         callback(value4);
>>     });
>> });
>> emitter.emit("step1");
>> ```
>> 利用事件展开后的效果变得越来越糟糕了。与纯粹嵌套相比，代码量明显增加了
>
>> 理想的编程体验应当是前一个的调用结果作为下一个调用的开始，是传说中的链式调用，相关代码如下：
>> ```JS
>> promise()
>>     .then(obj.api1)
>>     .then(obj.api2)
>>     .then(obj.api3)
>>     .then(obj.api4)
>>     .then(function (value4) {
>>         // Do something with value4
>>     }, function (error) {
>>         // Handle any error from step1 through step4
>>     })
>>     .done();
>> ```
>> 尝试改造一下代码以实现链式调用，具体如下所示：
>> ```JS
>> var Deferred = function () {
>>     this.promise = new Promise();
>> };
>> 
>> // 完成态
>> Deferred.prototype.resolve = function (obj) {
>>     var promise = this.promise;
>>     var handler;
>>     while ((handler = promise.queue.shift())) {
>>         if (handler && handler.fulfilled) {
>>             var ret = handler.fulfilled(obj);
>>             if (ret && ret.isPromise) {
>>                 ret.queue = promise.queue;
>>                 this.promise = ret;
>>                 return;
>>             }
>>         }
>>     }
>> };
>> 
>> // 失败态
>> Deferred.prototype.reject = function (err) {
>>     var promise = this.promise;
>>     var handler;
>>     while ((handler = promise.queue.shift())) {
>>         if (handler && handler.error) {
>>             var ret = handler.error(err);
>>             if (ret && ret.isPromise) {
>>                 ret.queue = promise.queue;
>>                 this.promise = ret;
>>                 return;
>>             }
>>         }
>>     }
>> };
>> 
>> // 生成回调函数
>> Deferred.prototype.callback = function () {
>>     var that = this;
>>     return function (err, file) {
>>         if (err) {
>>             return that.reject(err);
>>         }
>>         that.resolve(file);
>>     };
>> };
>> 
>> var Promise = function () {
>>     // 队列用于存储待执行的回调函数
>>     this.queue = [];
>>     this.isPromise = true;
>> };
>> 
>> Promise.prototype.then = function (fulfilledHandler, errorHandler, progressHandler) {
>>     var handler = {};
>>     if (typeof fulfilledHandler === 'function') {
>>         handler.fulfilled = fulfilledHandler;
>>     }
>>     if (typeof errorHandler === 'function') {
>>         handler.error = errorHandler;
>>     }
>>     this.queue.push(handler);
>>     return this;
>> };
>> ```
>
>> 要让Promise支持链式执行，主要通过以下两个步骤。
>> 1. 将所有的回调都存到队列中。
>> 2. Promise完成时，逐个执行回调，一旦检测到返回了新的Promise对象，停止执行，然后将当前Deferred对象的promise引用改变为新的Promise对象，并将队列中余下的回调转交给它。
>
>> 这里的代码主要用于研究Promise的实现原理。在更多细节的优化方面，Q或者when等Promise库做得更好，实际应用时请采用这些成熟库

### 4.3.3 流程控制库

>> 前面叙述了最为主流的模式——事件发布/订阅模式和Promise/Deferred模式，这些是经典的模式或者是写进规范里的解决方案，但一旦涉及模式或者规范，就需要为它们做较多的准备工作

>> 这一节将会介绍一些非模式化的应用，虽非规范，但更灵活

>> 除了事件和Promise外，还有一类方法是需要手工调用才能持续执行后续调用的，我们将此类方法叫做尾触发，常见的关键词是next。

>> 尾触发目前应用最多的地方是Connect的中间件

>> 先看一下Connect的API暴露方式，相关代码如下：
>> ```JS
>> var app = connect();
>> // Middleware
>> app.use(connect.staticCache());
>> app.use(connect.static(__dirname + '/public'));
>> app.use(connect.cookieParser());
>> app.use(connect.session());
>> app.use(connect.query());
>> app.use(connect.bodyParser());
>> app.use(connect.csrf());
>> app.listen(3001);
>> ```
>> 在通过`use()`方法注册好一系列中间件后，监听端口上的请求

>> 最简单的中间件如下：
>> ```JS
>> function (req, res, next) {
>>     // 中间件
>> }
>> ```
>>
>> 每个中间件传递请求对象、响应对象和尾触发函数，通过队列形成一个处理流

>> 中间件机制使得在处理网络请求时，可以像面向切面编程一样进行过滤、验证、日志等功能，而不与具体业务逻辑产生关联，以致产生耦合。

>> 下面我们来看Connect的核心实现，相关代码如下：
>> ```JS
>> function createServer() {
>>     function app(req, res){ app.handle(req, res); }
>>     utils.merge(app, proto);
>>     utils.merge(app, EventEmitter.prototype);
>>     app.route = '/';
>>     app.stack = [];
>>     for (var i = 0; i < arguments.length; ++i) {
>>         app.use(arguments[i]);
>>     }
>>     return app;
>> };
>> ```
>> 真正的核心代码是`app.stack = []；`这句。`stack`属性是这个服务器内部维护的中间件队列。通过调用`use()`方法我们可以将中间件放进队列中

>> 下面的代码为`use()`方法的重要部分：
>> ```JS
>> app.use = function(route, fn){
>>     // some code
>>     this.stack.push({ route: route, handle: fn });
>> 
>>     return this;
>> };
>> ```

>> 回到`app.handle()`方法，每一个监听到的网络请求都将从这里开始处理。该方法的代码如下：
>> ```JS
>> app.handle = function(req, res, out) {
>>     // some code
>>     next();
>> };
>> ```

>> 原始的`next()`方法较为复杂，下面是简化后的内容，其原理十分简单，取出队列中的中间件并执行，同时传入当前方法以实现递归调用，达到持续触发的目的：
>> ```JS
>> function next(err) {
>>     // some code
>>     // next callback
>>     layer = stack[index++];
>> 
>>     layer.handle(req, res, next);
>> }
>> ```

>> 如果每个步骤都采用异步来完成，实际上只是串行化的处理，没办法通过并行的异步调用来提升业务的处理效率。流式处理可以将一些串行的逻辑扁平化，但是并行逻辑处理还是需要搭配事件或者Promise完成的

## 4.4 异步并发控制

> 如果是对文件系统进行大量并发调用，操作系统的文件描述符数量将会被瞬间用光，抛出如下错误：
> ```SH
> Error: EMFILE, too many open files
> ```


# 第5章 内存控制

>> 在第3章中，差不多已介绍完Node是如何利用CPU和I/O这两个服务器资源

## 5.1 V8的垃圾回收机制与内存限制

>> Chrome的成功也离不开它背后的天才——Lars Bak。在Lars的工作履历里，绝大部分都是与虚拟机相关的工作。在开发V8之前，他曾经在Sun公司工作，担任HotSpot团队的技术领导，主要致力于开发高性能的Java虚拟机

>> 在Node中通过JavaScript使用内存时就会发现**只能使用部分内存**

>> 尽管在服务器端操作大内存也不是常见的需求场景，但有了限制之后，我们的行为就如同带着镣铐跳舞，**如果在实际的应用中不小心触碰到这个界限，会造成进程退出**。

>> 至于V8为何要限制堆的大小，表层原因为V8最初为浏览器而设计，不太可能遇到用大量内存的场景。对于网页来说，V8的限制值已经绰绰有余。深层原因是V8的垃圾回收机制的限制。按官方的说法，以1.5 GB的垃圾回收堆内存为例，V8做一次小的垃圾回收需要50毫秒以上，做一次非增量式的垃圾回收甚至要1秒以上。这是垃圾回收中引起JavaScript线程暂停执行的时间，在这样的时间花销下，应用的性能和响应能力都会直线下降。这样的情况不仅仅后端服务无法接受，前端浏览器也无法接受。因此，在当时的考虑下直接限制堆内存是一个好的选择。

>> V8依然提供了选项让我们使用更多的内存。Node在启动时可以传递`--max-old-space-size`或`--max-new-space-size`来调整内存限制的大小，上述参数**在V8初始化时生效，一旦生效就不能再动态改变**

>> 没有一种垃圾回收算法能够胜任所有的场景。因为在实际的应用中，对象的生存周期长短不一，不同的算法只能针对特定情况具有最好的效果

>> 现代的垃圾回收算法中按对象的存活时间将内存的垃圾回收进行不同的分代，然后分别对不同分代的内存施以更高效的算法

>> 在V8中，主要将内存分为**新生代**和**老生代**

>> 前面我们提及的`--max-old-space-size`命令行参数可以用于设置**老生代**内存空间的最大值，`--max-new-space-size`命令行参数则用于设置**新生代**内存空间的大小的

>> V8使用的内存**没有办法**根据使用情况自动扩充

>> **新生代**中的对象主要通过**Scavenge算法**进行垃圾回收。

>> 当一个对象**经过多次复制依然存活**时，它将会被认为是生命周期较长的对象。这种较长生命周期的对象随后会被**移动到老生代中，采用新的算法进行管理**。对象从新生代中移动到老生代中的过程称为晋升。

>> 对象从From空间中复制到To空间时，会检查它的内存地址来判断这个对象是否已经经历过一次Scavenge回收。如果已经经历过了，会将该对象从From空间复制到老生代空间中，如果没有，则复制到To空间中。

>> 另一个判断条件是To空间的内存占用比。当要从From空间复制一个对象到To空间时，如果**To空间已经使用了超过25%**，则这个对象直接晋升到老生代空间中

>> 设置25%这个限制值的原因是**当这次Scavenge回收完成后，这个To空间将变成From空间，接下来的内存分配将在这个空间中进行。如果占比过高，会影响后续的内存分配**。

>> 对于老生代中的对象，由于**存活对象占较大比重**，再采用Scavenge的方式会有两个问题：一个是存活对象较多，**复制存活对象的效率将会很低**；另一个问题依然是**浪费一半空间**的问题。这两个问题导致**应对生命周期较长的对象时**Scavenge会显得捉襟见肘。

>> V8在**老生代**中主要采用了**Mark-Sweep**和**Mark-Compact**相结合的方式进行垃圾回收

>> Mark-Sweep在标记阶段遍历堆中的所有对象，并**标记活着的对象**，在随后的清除阶段中，只**清除没有被标记的对象**。

>> Scavenge中只复制活着的对象，而Mark-Sweep只清理死亡对象。**活对象在**新生代**中只占较小部分，**死对象**在**老生代**中只占较小部分，这是两种回收方式能高效处理的原因

>> **Mark-Sweep最大的问题**是在进行一次标记清除回收后，**内存空间会出现不连续的状态**。这种内存碎片会对后续的内存分配造成问题，因为很可能出现需要分配一个大对象的情况，这时所有的碎片空间都无法完成此次分配，就会提前触发垃圾回收

>> **Mark-Compact**是标记整理的意思，是在Mark-Sweep的基础上演变而来的。它们的差别在于**对象在标记为死亡后，在整理的过程中，将活着的对象往一端移动**

>> 由于Mark-Compact需要移动对象，所以它的**执行速度不可能很快**

>> **V8主要使用Mark-Sweep，在空间不足以对从新生代中晋升过来的对象进行分配时才使用Mark-Compact**

>> 为了避免出现JavaScript应用逻辑与垃圾回收器看到的不一致的情况，**垃圾回收的3种基本算法都需要将应用逻辑暂停下来**，待执行完垃圾回收后再恢复执行应用逻辑，这种行为被称为“全停顿”（stop-the-world）。

>> **为了降低全堆垃圾回收带来的停顿时间**，V8先从标记阶段入手，将原本要一口气停顿完成的动作改为**增量标记（incremental marking）**

>> 垃圾回收与应用逻辑交替执行直到标记阶段完成

>> V8后续还引入了延迟清理（lazy sweeping）与增量式整理（incremental compaction），让清理与整理动作也变成增量式的。同时还计划引入并行标记与并行清理，进一步利用多核性能降低每次停顿的时间。

## 5.2 高效使用内存

>> **在V8中通过delete删除对象的属性有可能干扰V8的优化，所以通过赋值方式解除引用更好**

>> 在正常的JavaScript执行中，**无法立即回收的内存**有**闭包**和**全局变量引用**这两种情况。由于V8的内存限制，要十分小心此类变量是否无限制地增加，因为它**会导致老生代中的对象增多**。

## 5.3 内存指标

>> 通过`process.memoryUsage()`的结果可以看到，堆中的内存用量总是小于进程的常驻内存用量，这意味着Node中的内存使用并非都是通过V8进行分配的。我们将那些**不是通过V8分配的内存**称为**堆外内存**。

>> `Buffer`对象不同于其他对象，它不经过V8的内存分配机制，所以也不会有堆内存的大小限制。这意味着**利用堆外内存**可以**突破内存限制的问题**。

>> 从上面的介绍可以得知，Node的内存构成主要**由通过V8进行分配的部分**和**Node自行分配的部分**。受V8的垃圾回收限制的主要是V8的堆内存。

## 5.4 内存泄漏

>> 尽管内存泄漏的情况不尽相同，但其实质只有一个，那就是**应当回收的对象**出现意外而**没有被回收**，变成了**常驻在老生代中**的对象。

>> 通常，造成内存泄漏的原因有如下几个。
>> * 缓存。
>> * 队列消费不及时。
>> * 作用域未释放。

>> 缓存中存储的键越多，长期存活的对象也就越多，**这将导致垃圾回收在进行扫描和整理时，对这些对象做无用功**。

>> JavaScript开发者通常喜欢用对象的键值对来缓存东西，但这与严格意义上的缓存又有着区别，**严格意义的缓存有着完善的过期策略**

>> 为了解决缓存中的对象永远无法释放的问题，需要加入一种策略来**限制缓存的无限增长**。比如实现对键值数量的限制，一旦超过数量，就以先进先出的方式进行淘汰。当然，这种淘汰策略并不是十分高效，只能应付小型应用场景。如果需要更高效的缓存，可以参见Isaac Z. Schlueter采用**LRU**算法的缓存，地址为[https://github.com/isaacs/node-lru-cache](https://github.com/isaacs/node-lru-cache)

> 由于模块的缓存机制，模块是常驻老生代的。**在设计模块时，要十分小心内存泄漏的出现**。在下面的代码，每次调用leak()方法时，都导致局部变量leakArray不停增加内存的占用，且不被释放：
> ```JS
> var leakArray = [];
> exports.leak = function () {
>     leakArray.push("leak" + Math.random());
> };
> ```
> 如果模块不可避免地需要这么设计，那么请**添加清空队列的相应接口，以供调用者释放内存**。

>> 另外要考虑的事情是，进程之间无法共享内存。**如果在进程内使用缓存，这些缓存不可避免地有重复**

>> 如何使用大量缓存，目前比较好的解决方案是**采用进程外的缓存**

>> **目前，市面上较好的缓存有Redis和Memcached。**

>> **另一个不经意产生的内存泄漏则是队列**

>> 在大多数应用场景下，消费的速度远远大于生产的速度，内存泄漏不易产生。但是**一旦消费速度低于生产速度，将会形成堆积**

>> 日志通常会是海量的，**数据库构建在文件系统之上，写入效率远远低于文件直接写入**

>> 深度的解决方案应该是**监控队列的长度**，一旦堆积，应当通过监控系统产生报警并通知相关人员。另一个解决方案是**任意异步调用都应该包含超时机制**，一旦在限定的时间内未完成响应，通过回调函数传递超时异常

## 5.5 内存泄漏排查

>> 现在已经有许多工具用于定位Node应用的内存泄漏，下面是一些常见的工具。
>> *  **v8-profiler**。由Danny Coates提供，它可以用于对V8堆内存抓取快照和对CPU进行分析，但该项目已经有3年没有维护了。
>> *  **node-heapdump**。这是Node核心贡献者之一Ben Noordhuis编写的模块，它允许对V8堆内存抓取快照，用于事后分析。
>> *  **node-mtrace**。由Jimb Esser提供，它使用了GCC的mtrace工具来分析堆的使用。
>> *  **dtrace**。在Joyent的SmartOS系统上，有完善的dtrace工具用来分析内存泄漏。
>> *  **node-memwatch**。来自Mozilla的Lloyd Hilaiel贡献的模块，采用WTFPL许可发布。

### 5.5.1 node-heapdump

>> 通过在开发者工具的面板中查看内存分布，我们可以找到泄漏的数据，然后根据这些信息找到造成泄漏的代码

### 5.5.2 node-memwatch

#### 1. stats事件

>> 在进程中使用node-memwatch之后，每次进行全堆垃圾回收时，将会触发一次stats事件，这个事件将会传递**内存的统计信息**。

#### 2. leak事件

>> 如果经过连续5次垃圾回收后，内存仍然没有被释放，这意味着**有内存泄漏的产生**，node-memwatch会出发一个leak事件。

#### 3．堆内存比较

>> node-memwatch提供了抓取快照和比较快照的功能，它能够**比较堆上对象的名称和分配数量，从而找出导致内存泄漏的元凶**。

## 5.6 大内存应用

>> 由于V8的内存限制，我们无法通过`fs.readFile()`和`fs.writeFile()`直接进行大文件的操作，而**改用`fs.createReadStream()`和`fs.createWriteStream()`方法通过流的方式实现对大文件的操作**。

>> **如果不需要进行字符串层面的操作，则不需要借助V8来处理，可以尝试进行纯粹的`Buffer`操作**，这不会受到V8堆内存的限制。


# 第6章 理解`Buffer`

## 6.1 `Buffer`结构

>> `Buffer`对象的内存分配不是在V8的堆内存中，而是在Node的C++层面实现内存的申请的。因为处理大量的字节数据不能采用需要一点内存就向操作系统申请一点内存的方式，这可能造成大量的内存申请的系统调用，对操作系统有一定压力

>> Node在内存的使用上应用的是在C++层面申请内存、在JavaScript中分配内存的策略

>> 为了高效地使用申请来的内存，**Node采用了slab分配机制**。slab是一种**动态内存管理机制**

>> slab具有如下3种状态。
>> * **full**：完全分配状态。
>> * **partial**：部分分配状态。
>> * **empty**：没有被分配状态。

>> Node以8 KB为界限来区分`Buffer`是大对象还是小对象：
>> ```JS
>> Buffer.poolSize = 8 * 1024;
>> ```

### 1．分配小Buffer对象

>> 如果指定`Buffer`的大小少于8 KB, Node会按照小对象的方式进行分配。`Buffer`的分配过程中主要使用一个局部变量pool作为中间处理对象，处于分配状态的slab单元都指向它。

### 2．分配大Buffer对象

>> 如果需要超过8 KB的`Buffer`对象，将会直接分配一个`SlowBuffer`对象作为slab单元，这个slab单元将会被这个大Buffer对象独占。

>> `SlowBuffer`类是在C++中定义的，虽然引用buffer模块可以访问到它，但是不推荐直接操作它，而是用`Buffer`替代

## 6.2 `Buffer`的转换

> `Buffer`对象与字符串之间相互转换**目前支持的字符串编码类型有如下这几种**。
> * ASCII
> * UTF-8
> * UTF-16LE/UCS-2
> * Base64
> * Binary
> * Hex

> **字符串转`Buffer`对象**主要是通过构造函数完成的：
> ```JS
> new Buffer(str, [encoding]);
> ```

>> 一个`Buffer`对象可以存储不同编码类型的字符串转码的值，调用write()方法可以实现该目的，代码如下：
> ```JS
> buf.write(string, [offset], [length], [encoding])
> ```

> `Buffer`对象的`toString()`可以**将`Buffer`对象转换为字符串**，代码如下：
> ```JS
> buf.toString([encoding], [start], [end])
> ```

>> `Buffer`提供了一个`isEncoding()`函数来**判断编码是否支持转换**

>> GBK、GB2312和BIG-5编码都不在支持的行列中。对于不支持的编码类型，可以借助Node生态圈中的模块完成转换。`iconv`和`iconv-lite`两个模块可以支持更多的编码类型转换，包括Windows 125系列、ISO-8859系列、IBM/DOS代码页系列、Macintosh系列、KOI8系列，以及Latin1、US-ASCII，也支持宽字节编码GBK和GB2312

## 6.3 `Buffer`的拼接

>> `data`事件中获取的`chunk`对象即是`Buffer`对象

>> 一旦输入流中有宽字节编码时，问题就会暴露出来。如果你在通过Node开发的网站上看到乱码符号，那么该问题的起源多半来自于这里。

>> 外国人的语境通常是指英文环境，在他们的场景下，这个`toString()`不会造成任何问题。但对于宽字节的中文，却会形成问题

>> 对于任意长度的`Buffer`而言，宽字节字符串都有可能存在被截断的情况

> **可读流还有一个设置编码的方法`setEncoding()`，示例如下：**
> ```JS
> readable.setEncoding(encoding)
> ```
> **该方法的作用是让`data`事件中传递的不再是一个`Buffer`对象，而是编码后的字符串**

>> 在调用`setEncoding()`时，可读流对象在内部设置了一个decoder对象。decoder对象来自于`string_decoder`模块`StringDecoder`的实例对象。

>> `string_decoder`模块很奇妙，但是它也并非万能药，**它目前只能处理UTF-8、Base64和UCS-2/UTF-16LE这3种编码**。所以，通过`setEncoding()`的方式不可否认能解决大部分的乱码问题，但并不能从根本上解决该问题

>> 淘汰掉`setEncoding()`方法后，剩下的解决方案只有将多个小`Buffer`对象**拼接为一个`Buffer`对象，然后通过`iconv-lite`一类的模块来转码**这种方式。

>> 正确的拼接方式是用一个数组来存储接收到的所有`Buffer`片段并记录下所有片段的总长度，然后调用`Buffer.concat()`方法生成一个合并的`Buffer`对象。

## 6.4 `Buffer`与性能

>> 通过**预先转换静态内容为`Buffer`对象**，可以有效地减少CPU的重复使用

>> 在Node构建的Web应用中，可以选择将页面中的动态内容和静态内容分离，静态内容部分可以通过预先转换为`Buffer`的方式，使性能得到提升

>> **文件自身是二进制数据，所以在不需要改变内容的场景下，尽量只读取`Buffer`，然后直接传输**

> **在文件的读取时，有一个`highWaterMark`设置对性能的影响至关重要**。在`fs.createReadStream(path, opts)`时，我们可以传入一些参数，代码如下：
> ```JS
> {
>     flags: 'r',
>     encoding: null,
>     fd: null,
>     mode: 0666,
>     highWaterMark: 64 * 1024
> }
> ```

>> `highWaterMark`设置过小，**可能导致系统调用次数过多**


# 第7章 网络编程

## 7.1 构建TCP服务

>> 可以开始创建一个TCP服务器端来接受网络请求，代码如下：
>> ```JS
>> var net = require('net');
>> 
>> var server = net.createServer(function (socket) {
>>     // 新的连接
>>     socket.on('data', function (data) {
>>     socket.write("你好");
>>     });
>> 
>>     socket.on('end', function () {
>>     console.log(’连接断开’);
>>     });
>>     socket.write("欢迎光临《深入浅出Node.js》示例：\n");
>> });
>> 
>> server.listen(8124, function () {
>>     console.log('server bound');
>> });
>> ```

> 对于通过`net.createServer()`创建的服务器而言，它是一个`EventEmitter`实例，**它的自定义事件有如下几种**。
> * `listening`：在调用 **`server.listen()`** 绑定端口或者Domain Socket后触发
> * `connection`：每个客户端套接字**连接到服务器端**时触发
> * `close`：当**服务器关闭**时触发
> * `error`：当**服务器发生异常**时，将会触发该事件。比如侦听一个使用中的端口，将会触发一个异常，如果不侦听`error`事件，服务器将会抛出异常。

> 对于每个连接而言是典型的可写可读`Stream`对象。`Stream`对象可以用于服务器端和客户端之间的通信，既可以通过`data`事件从一端读取另一端发来的数据，也可以通过`write()`方法从一端向另一端发送数据。它具有如下自定义事件
> * `data`：当一端**调用`write()`发送数据时，另一端会触发**`data`事件
> * `end`：当连接中的**任意一端发送了`FIN`数据**时，将会触发该事件。
> * `connect`：该事件用于客户端，**当套接字与服务器端连接成功**时会被触发。
> * `drain`：当任意一端**调用`write()`发送数据时，当前这端会触发**该事件。
> * `error`：当**异常发生**时，触发该事件。
> * `close`：当**套接字完全关闭**时，触发该事件。
> * `timeout`：当**一定时间后连接不再活跃**时，该事件将会被触发，通知用户当前该连接已经被闲置了。

>> 小数据包将会被**Nagle算法**合并，以此来优化网络。这种优化虽然使网络带宽被有效地使用，但是**数据有可能被延迟发送**

>> 可以调用`socket.setNoDelay(true)`**去掉Nagle算法**，使得`write()`可以立即发送数据到网络中

>> 在关闭掉Nagle算法后，**另一端可能会将接收到的多个小数据包合并，然后只触发一次data事件**

## 7.2 构建UDP服务

> **UDP套接字一旦创建，既可以作为客户端发送数据，也可以作为服务器端接收数据**。下面的代码创建了一个UDP套接字：
> ```JS
> var dgram = require('dgram');
> var socket = dgram.createSocket("udp4");
> ```

> **调用`dgram.bind(port, [address])`方法**对网卡和端口进行绑定
> ```JS
> var dgram = require("dgram");
> 
> var server = dgram.createSocket("udp4");
> 
> server.on("message", function (msg, rinfo) {
>     console.log("server got: " + msg + " from " +
>         rinfo.address + ":" + rinfo.port);
> });
> 
> server.on("listening", function () {
>     var address = server.address();
>     console.log("server listening " +
>         address.address + ":" + address.port);
> });
> 
> server.bind(41234);
> ```

> **创建一个客户端**与服务器端进行对话，代码如下：
> ```JS
> var dgram = require('dgram');
> 
> var message = new Buffer("深入浅出Node.js");
> var client = dgram.createSocket("udp4");
> client.send(message, 0, message.length, 41234, "localhost", function(err, bytes) {
>     client.close();
> });
> ```

>> 当套接字对象用在客户端时，可以调用`send()`方法**发送消息**到网络中。

> UDP套接字相对TCP套接字使用起来更简单，它只是一个`EventEmitter`的实例，而非`Stream`的实例。它具备如下自定义事件
> * `message`：当UDP套接字侦听网卡端口后，**接收到消息**时触发该事件
> * `listening`：当UDP套接字**开始侦听**时触发该事件。
> * `close`：**调用`close()`方法**时触发该事件
> * `error`：当**异常发生**时触发该事件，如果不侦听，异常将直接抛出，使进程退出。

## 7.3 构建HTTP服务

>> 如果要**构造高效的网络应用**，就应该从**传输层**进行着手。但是对于**经典的应用场景**，则无须从传输层协议入手构造自己的应用，比如HTTP或SMTP等，这些**经典的应用层协议**对于普通应用而言绰绰有余

> Node官网上的经典例子就展示了如何用寥寥几行代码**实现一个HTTP服务器**，代码如下：
> ```JS
> var http = require('http');
> http.createServer(function (req, res) {
>     res.writeHead(200, {'Content-Type': 'text/plain'});
>     res.end('Hello World\n');
> }).listen(1337, '127.0.0.1');
> console.log('Server running at http://127.0.0.1:1337/');
> ```

>> HTTP得以发展是W3C和IETF两个组织合作的结果，他们最终发布了一系列RFC标准，目前最知名的HTTP标准为RFC 2616。

>> `curl`，通过`-v`选项，可以显示这次网络通信的所有报文信息

> **请求报文头第一行**`GET / HTTP/1.1`被解析之后分解为如下属性
> * **`req.method`** 属性：值为`GET`，是为请求方法
> * **`req.url`** 属性：值为`/`。
> * **`req.httpVersion`** 属性：值为`1.1`。

>> **其余报头**是很规律的`Key: Value`格式，被解析后放置在`req.headers`属性上

>> HTTP响应封装了对底层连接的写操作，可以将其看成一个可写的流对象。它影响**响应报文头部**信息的API为`res.setHeader()`和`res.writeHead()`。

>> 我们可以调用`setHeader`进行多次设置，但**只有调用`writeHead`后，报头才会写入到连接中**。

>> **http模块会自动帮你设置一些头信息，如下所示：**
>> ```HTTP
>> < Date: Sat, 06 Apr 2013 08:01:44 GMT
>> < Connection: keep-alive
>> < Transfer-Encoding: chunked
>> <
>> ```

>> **报文体**部分则是调用`res.write()`和`res.end()`方法实现

>> `res.end()`会先调用`write()`发送数据，然后发送信号告知服务器这次响应结束

>> 无论服务器端在处理业务逻辑时是否发生异常，**务必在结束时调用`res.end()`结束请求**

>> 可以通过延迟`res.end()`的方式实现客户端与服务器端之间的长连接，但结束时务必关闭连接

> HTTP服务器也抽象了一些**事件**，以供应用层使用，同样典型的是，服务器也是一个`EventEmitter`实例
> * **`connection`** 事件：**连接建立**时，服务器触发一次`connection`事件。
> * **`request`** 事件：当请求数据发送到服务器端，在**解析出HTTP请求头后**，将会触发该事件
> * **`close`** 事件：与TCP服务器的行为一致，调用`server.close()`方法停止接受新的连接，当已有的连接都断开时，触发该事件
> * **`checkContinue`** 事件
> * **`connect`** 事件：当客户端发起`CONNECT`请求时触发；如果不监听该事件，发起该请求的连接将会关闭。
> * **`upgrade`** 事件：当客户端要求升级连接的协议时，客户端会在请求头中带上`Upgrade`字段，服务器端会在接收到这样的请求时触发该事件
> * **`clientError`** 事件：连接的客户端触发`error`事件时，这个错误会传递到服务器端，此时触发该事件。

### 7.3.3 HTTP客户端

>> `http`模块提供了一个底层API:`http.request(options, connect)`，用于**构造HTTP客户端**

>> `auth`:Basic认证，这个值将被计算成**请求头中的`Authorization`部分**。

>> **报文体**的内容由请求对象的`write()`和`end()`方法实现

> 后续响应报文体以只读流的方式提供，如下所示：
> ```JS
> function(res) {
>     console.log('STATUS: ' + res.statusCode);
>     console.log('HEADERS: ' + JSON.stringify(res.headers));
>     res.setEncoding('utf8');
>     res.on('data', function (chunk) {
>         console.log(chunk);
>     });
> }
> ```

>> 如果你在服务器端通过`ClientRequest`调用网络中的其他HTTP服务，记得关注**代理对象对网络请求的限制**。一旦请求量过大，连接限制将会限制服务性能。

> 既可以自行构造代理对象，代码如下：
> ```JS
> var agent = new http.Agent({
>     maxSockets: 10
> });
> var options = {
>     hostname: '127.0.0.1',
>     port: 1334,
>     path: '/',
>     method: 'GET',
>     agent: agent
> };
> ```
> 可以设置`agent`选项为`false`值，以脱离连接池的管理

>> `Agent`对象的`sockets`和`requests`属性分别表示当前连接池中使用中的连接数和处于等待状态的请求数，在业务中监视这两个值有助于发现业务状态的繁忙程度。

> HTTP客户端也有相应的事件。
> * `response`：与服务器端的`request`事件对应的客户端在请求发出后**得到服务器端响应**时，会触发该事件。
> * `socket`：当底层连接池中建立的连接分配给当前请求对象时，触发该事件。
> * `connect`：当客户端向服务器端发起`CONNECT`请求时，如果服务器端响应了200状态码，客户端将会触发该事件。
> * `upgrade`：客户端向服务器端发起`Upgrade`请求时，如果服务器端响应了`101 Switching Protocols`状态，客户端将会触发该事件。
> * `continue`：客户端向服务器端发起`Expect: 100-continue`头信息，以试图发送较大数据量，如果服务器端响应`100 Continue`状态，客户端将触发该事件。

## 7.4 构建WebSocket服务

>> 通过`onmessage()`方法**接收服务器端传来的数据**

>> **WebSocket的握手部分是由HTTP完成的**

> 与普通的HTTP请求协议略有区别的部分在于如下这些协议头：
> ```HTTP
> Upgrade: websocket
> Connection: Upgrade
> ```
> 上述两个字段表示**请求服务器端升级协议为WebSocket**

>> `Sec-WebSocket-Key`用于**安全校验**

> 下面两个字段指定**子协议**和**版本号**：
> ```HTTP
> Sec-WebSocket-Protocol: chat, superchat
> Sec-WebSocket-Version: 13
> ```

> 服务器端在处理完请求后，响应如下报文：
> ```HTTP
> HTTP/1.1 101 Switching Protocols
> Upgrade: websocket
> Connection: Upgrade
> Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo=
> Sec-WebSocket-Protocol: chat
> ```
> * 告之客户端正在更换协议
> * 更新应用层协议为WebSocket协议
> * 客户端将会校验`Sec-WebSocket-Accept`的值
> * 选中的子协议

>> **握手完成**后，客户端的`onopen()`将会被触发执行

> * 当客户端**调用 `send()`** 发送数据时，服务器端**触发 `onmessage()`**；   
> * 当服务器端**调用 `send()`** 发送数据时，客户端的 **`onmessage()` 触发**

>> 调用`send()`发送一条数据时，协议可能将这个数据封装为一帧或多帧数据，然后逐帧发送

>> 服务器一旦收到无掩码帧（比如中间拦截破坏），连接将关闭

>> 如果客户端收到带掩码的数据帧，连接也将关闭

>> 图7-7中为WebSocket数据帧的定义，每8位为一列，也即1个字节。其中每一位都有它的意义。

>> 尽管Node没有内置WebSocket的库，但是社区的`ws`模块封装了WebSocket的底层实现。socket.io即是在它的基础上构建实现的。

## 7.5 网络服务与安全

>> SSL作为一种安全协议，它在传输层提供对网络连接加密的功能。对于应用层而言，它是透明的，数据在传递到应用层之前就已经完成了加密和解密的过程

>> IETF将其标准化，称为TLS（Transport Layer Security，安全传输层协议）

>> Node在网络安全上提供了3个模块，分别为`crypto`、`tls`、`https`
> * `crypto`主要用于**加密解密**，SHA1、MD5等加密算法都在其中有体现
> * `tls`模块提供了**与net模块类似**的功能
> * 对于`https`而言，它完全**与`http`模块接口一致，区别也仅在于它建立于安全的连接之上**

>> Node在底层采用的是`openssl`实现TLS/SSL的

>> 要生成公钥和私钥可以通过`openssl`完成

> 生成**私钥**，如下所示：
> ```sh
> # // 生成服务器端私钥
> $ openssl genrsa -out server.key 1024
> # // 生成客户端私钥
> $ openssl genrsa -out client.key 1024
> ```

> 通过它继续生成**公钥**，如下所示：
> ```sh
> $ openssl rsa -in server.key -pubout -out server.pem
> $ openssl rsa -in client.key -pubout -out client.pem
> ```

>> 数据传输过程中还需要对得到的公钥进行认证，以确认得到的公钥是出自目标服务器。如果不能保证这种认证，中间人可能会将伪造的站点响应给用户

>> TLS/SSL引入了数字证书来进行认证

> 数字证书中包含了
> * **服务器**的名称和主机名、
> * **服务器**的公钥、
> * **签名颁发机构**的名称、
> * 来自**签名颁发机构**的签名

>> CA（Certificate Authority，数字证书认证中心）

>> 通过CA机构颁发证书通常是一个烦琐的过程，需要付出一定的精力和费用。对于中小型企业而言，多半是采用自签名证书来构建安全网络的。

> 以下为生成私钥、生成CSR文件、通过私钥自签名生成证书的过程：
> ```sh
> $ openssl genrsa -out ca.key 1024
> $ openssl req -new -key ca.key -out ca.csr
> $ openssl x509 -req -in ca.csr -signkey ca.key -out ca.crt
> ```

> 如下是生成CSR文件所用的命令：
> ```sh
> $ openssl req -new -key server.key -out server.csr
> ```
> * **2023/08/28发表想法**  
>   上面是生成CA证书，这里是生成服务器证书

> 最终颁发一个带有CA签名的证书，如下所示：
> ```sh
> $ openssl x509 -req -CA ca.crt -CAkey ca.key -CAcreateserial -in server.csr -out server.crt
> ```

>> 如果是知名的CA机构，它们的证书一般预装在浏览器中。

>> 签名证书是一环一环地颁发的

>> 在CA那里的证书是不需要上级证书参与签名的，这个证书我们通常称为根证书

> 通过Node的 **`tls` 模块**来创建一个安全的TCP服务，这个服务是一个简单的echo服务，代码如下：
> ```JS
> var tls = require('tls');
> var fs = require('fs');
> 
> var options = {
>     key: fs.readFileSync('./keys/server.key'),
>     cert: fs.readFileSync('./keys/server.crt'),
>     requestCert: true,
>     ca: [ fs.readFileSync('./keys/ca.crt') ]
> };
> 
> var server = tls.createServer(options, function (stream) {
>     console.log('server connected', stream.authorized ? 'authorized' : 'unauthorized');
>     stream.write("welcome! \n");
>     stream.setEncoding('utf8');
>     stream.pipe(stream);
> });
> server.listen(8000, function() {
>     console.log('server bound');
> });
> ```

> 通过下面的命令可以测试证书是否正常：
> ```sh
> $ openssl s_client -connect 127.0.0.1:8000
> ```

>> `tls`模块也提供了`connect()`方法来**构建客户端**

> 需要为客户端生成属于自己的私钥和签名，代码如下：
> ```sh
> # // 创建私钥
> $ openssl genrsa -out client.key 1024
> # // 生成CSR
> $ openssl req -new -key client.key -out client.csr
> # // 生成签名证书
> $ openssl x509-req -CA ca.crt -CAkey ca.key -CAcreateserial -in client.csr -out client.crt
> ```

> **创建客户端**，代码如下：
> ```JS
> var tls = require('tls');
> var fs = require('fs');
> 
> var options = {
>     key: fs.readFileSync('./keys/client.key'),
>     cert: fs.readFileSync('./keys/client.crt'),
>     ca: [ fs.readFileSync('./keys/ca.crt') ]
> };
> 
> var stream = tls.connect(8000, options, function () {
>     console.log('client connected', stream.authorized ? 'authorized' : 'unauthorized');
>     process.stdin.pipe(stream);
> });
> 
> stream.setEncoding('utf8');
> stream.on('data', function(data) {
>     console.log(data);
> });
> stream.on('end', function() {
>     server.close();
> });
> ```

> **创建HTTPS服务**只比HTTP服务多一个选项配置，其余地方几乎相同，代码如下：
> ```JS
> var https = require('https');
> var fs = require('fs');
> 
> var options = {
>     key: fs.readFileSync('./keys/server.key'),
>     cert: fs.readFileSync('./keys/server.crt')
> };
> 
> https.createServer(options, function (req, res) {
>     res.writeHead(200);
>     res.end("hello world\n");
> }).listen(8000);
> ```

> 给`curl`设置`--cacert`选项，告知CA证书使之完成对服务器证书的验证，如下所示：
> ```sh
> $ curl --cacert keys/ca.crt https://localhost:8000/
> hello world
> ```

> 用Node来实现**HTTPS的客户端**，与HTTP的客户端相差不大，除了指定证书相关的参数外，如下所示：
> ```JS
> var https = require('https');
> var fs = require('fs');
> 
> var options = {
>     hostname: 'localhost',
>     port: 8000,
>     path: '/',
>     method: 'GET',
>     key: fs.readFileSync('./keys/client.key'),
>     cert: fs.readFileSync('./keys/client.crt'),
>     ca: [fs.readFileSync('./keys/ca.crt')]
> };
> 
> options.agent = new https.Agent(options);
> 
> var req = https.request(options, function(res) {
>     res.setEncoding('utf-8');
>     res.on('data', function(d) {
>         console.log(d);
>     });
> });
> req.end();
> 
> req.on('error', function(e) {
>     console.log(e);
> });
> ```


# 第8章 构建Web应用

## 8.1 基础功能

>> 在解析请求报文的时候，将**报文头**抽取出来，设置为``req.method``

> **路径**部分存在于报文的第一行的第二部分，如下所示：
> ```HTTP
> GET /path?foo=bar HTTP/1.1
> ```
> HTTP_Parser将其解析为`req.url`

>> hash部分会被丢弃

> 还有一种比较常见的分发场景是根据路径来选择控制器，它预设路径为**控制器**和**行为**的组合，无须额外配置路由信息，如下所示：
> ```URL
> /controller/action/a/b/c
> ```

### 8.1.3 查询字符串

> Node提供了 **`querystring` 模块**用于处理这部分数据，如下所示：
> ```JS
> var url = require('url');
> var querystring = require('querystring');
> var query = querystring.parse(url.parse(req.url).query);
> ```

> 更简洁的方法是给`url.parse()`传递第二个参数，如下所示：
> ```JS
> var query = url.parse(req.url, true).query;
> ```
> 它会将`foo=bar&baz=val`**解析为一个JSON对象**

> 如果查询字符串中的键出现多次，那么它的值会是一个数组，如下所示：
> ```JS
> // foo=bar&foo=baz
> var query = url.parse(req.url, true).query;
> // {
> //   foo: ['bar', 'baz']
> // }
> ```
> **业务的判断一定要检查值是数组还是字符串，否则可能出现`TypeError`异常的情况**

### 8.1.4 Cookie

>> HTTP是一个无状态的协议，现实中的业务却是需要一定的状态的

> 响应的Cookie值在 **`Set-Cookie` 字段**中。它的格式与请求中的格式不太相同，规范中对它的定义如下所示：
> ```HTTP
> Set-Cookie: name=value; Path=/; Expires=Sun, 23-Apr-23 09:01:35 GMT; Domain=.domain.com;
> ```
> 其中`name=value`是必须包含的部分，其余部分皆是可选参数。这些可选参数将会影响浏览器在后续将Cookie发送给服务器端的行为。以下为主要的几个选项
> * `path`表示这个Cookie**影响到的路径**，当前访问的路径不满足该匹配时，浏览器则不发送这个Cookie。
> * `Expires`和`Max-Age`是用来告知浏览器这个Cookie**何时过期**的，**如果不设置该选项，在关闭浏览器时会丢失掉这个Cookie**。`Expires`的值是一个UTC格式的时间字符串
> * `HttpOnly`告知浏览器**不允许通过脚本`document.cookie`去更改**这个Cookie值
> * `Secure`。当`Secure`值为`true`时，在HTTP中是无效的，**在HTTPS中才有效**

> **`res.setHeader`的第二个参数可以是一个数组**，如下所示：
> ```JS
> res.setHeader('Set-Cookie', [serialize('foo', 'bar'), serialize('baz', 'val')]);
> ```
> 这会在报文头部中形成两条`Set-Cookie`字段

>> 客户端每次请求都会发送这些Cookie到服务器端，一旦设置的Cookie过多，将会导致报头较大。大多数的Cookie并不需要每次都用上，因为这会造成带宽的部分浪费

> YSlow的性能优化规则中有这么一条：
> * 减小Cookie的大小

>> **如果在域名的根节点设置Cookie，几乎所有子路径下的请求都会带上这些Cookie**，这些Cookie在某些情况下是有用的，但是在有些情况下是完全无用的

>> 静态文件的业务定位几乎不关心状态，Cookie对它而言几乎是无用的

> YSlow中有另外一条规则用来避免Cookie带来的性能影响。
> * **为静态组件使用不同的域名**
> 
> 很多网站的静态文件会有特别的域名，使得业务相关的Cookie不再影响静态资源

>> 看起来减少DNS查询和使用不同的域名是冲突的两条规则，但是好在现今的浏览器都会进行DNS缓存，以削弱这个副作用的影响

>> **Cookie对于敏感数据的保护是无效的**

>> **Session**的数据只保留在服务器端，客户端无法修改，这样**数据的安全性得到一定的保障，数据也无须在协议中每次都被传递**。

>> 但是**将口令放在Cookie中还是可以的**。因为口令一旦被篹改，就丢失了映射关系，也无法修改服务器端存在的数据了
> * **2023/08/29发表想法**  
>   ChatGPT：这里的“口令”指的就是Session ID

>> **约定一个键值作为Session的口令**

> 一旦服务器检查到用户请求Cookie中没有携带该值，它就会为之**生成一个值**，这个值是唯一且不重复的值，并设定超时时间。以下为生成session的代码：
> ```JS
> var sessions = {};
> var key = 'session_id';
> var EXPIRES = 20 * 60 * 1000;
> 
> var generate = function () {
>     var session = {};
>     session.id = (new Date()).getTime() + Math.random();
>     session.cookie = {
>         expire: (new Date()).getTime() + EXPIRES
>     };
>     sessions[session.id] = session;
>     return session;
> };
> ```

>> **每个请求到来时，检查Cookie中的口令与服务器端的数据，如果过期，就重新生成**，如下所示：
> ```JS
> function (req, res) {
>     var id = req.cookies[key];
>     if (! id) {
>         req.session = generate();
>     } else {
>         var session = sessions[id];
>         if (session) {
>             if (session.cookie.expire > (new Date()).getTime()) {
>                 // 更新超时时间
>                 session.cookie.expire = (new Date()).getTime() + EXPIRES;
>                 req.session = session;
>             } else {
>                 // 超时了，删除旧的数据，并重新生成
>                 delete sessions[id];
>                 req.session = generate();
>             }
>         } else {
>             // 如果session过期或口令不对，重新生成session
>             req.session = generate();
>         }
>     }
>     handle(req, res);
> }
> ```

> 在**响应给客户端时设置新的值**，以便下次请求时能够对应服务器端的数据。这里我们hack响应对象的`writeHead()`方法，在它的内部注入设置Cookie的逻辑，如下所示：
> ```JS
> var writeHead = res.writeHead;
> res.writeHead = function () {
>     var cookies = res.getHeader('Set-Cookie');
>     var session = serialize(key, req.session.id);
>     cookies = Array.isArray(cookies) ? cookies.concat(session) : [cookies, session];
>     res.setHeader('Set-Cookie', cookies);
>     return writeHead.apply(this, arguments);
> };
> ```

>> 这种实现方案依赖Cookie实现，而且也是目前大多数Web应用的方案。如果客户端禁止使用Cookie，这个世界上大多数的网站将无法实现登录等操作。

>> 通过查询字符串来实现浏览器端和服务器端数据的对应**带来的风险远大于基于Cookie实现的风险**

>> 还有一种比较有趣的处理Session的方式是利用HTTP请求头中的`ETag`，同样对于更换浏览器和电脑后也是无效的

#### 1. Session与内存

>> 将数据存放在内存中将会带来极大的隐患，如果用户增多，我们很可能就接触到了**内存限制**的上限，并且内存中的数据量加大，必然会**引起垃圾回收的频繁扫描，引起性能问题**

>> 我们可能为了利用多核CPU而启动多个进程，这个细节在第9章中有详细描述。用户请求的连接将可能随意分配到各个进程中，Node的**进程与进程之间是不能直接共享内存的，用户的Session可能会引起错乱**

>> 为了解决性能问题和Session数据无法跨进程共享的问题，常用的方案是**将Session集中化**，将原本可能分散在多个进程里的数据，统一转移到集中的数据存储中

>> 目前常用的工具是Redis、Memcached等

>> 尽管采用专门的缓存服务会比直接在内存中访问慢，但其影响小之又小，带来的好处却远远大于直接在Node中保存数据。

> **一旦Session需要异步的方式获取，代码就需要略作调整**，变成异步的方式，如下所示：
> ```JS
> function (req, res) {
>     var id = req.cookies[key];
>     if (! id) {
>         req.session = generate();
>         handle(req, res);
>     } else {
>         store.get(id, function (err, session) {
>             if (session) {
>                 if (session.cookie.expire > (new Date()).getTime()) {
>                     // 更新超时时间
>                     session.cookie.expire = (new Date()).getTime() + EXPIRES;
>                     req.session = session;
>                 } else {
>                     // 超时了，删除旧的数据，并重新生成
>                     delete sessions[id];
>                     req.session = generate();
>                 }
>             } else {
>                 // 如果session过期或口令不对，重新生成session
>                 req.session = generate();
>             }
>             handle(req, res);
>         });
>     }
> }
> ```

> **在响应时，将新的session保存回缓存中**，如下所示：
> ```JS
> var writeHead = res.writeHead;
> res.writeHead = function () {
>     var cookies = res.getHeader('Set-Cookie');
>     var session = serialize('Set-Cookie', req.session.id);
>     cookies = Array.isArray(cookies) ? cookies.concat(session) : [cookies, session];
>     res.setHeader('Set-Cookie', cookies);
>     // 保存回缓存
>     store.save(req.session);
>     return writeHead.apply(this, arguments);
> };
> ```

#### 2. Session与安全

>> 一旦口令被伪造，服务器端的数据也可能间接被利用。

>> **将这个口令通过私钥加密进行签名**，使得伪造的成本较高。

>> 只要在响应时将口令和签名进行对比，如果签名非法，我们将服务器端的数据立即过期即可，如下所示：
>> ```JS
>> // 将值通过私钥签名，由．分割原值和签名
>> var sign = function (val, secret) {
>>     return val + '.' + crypto
>>         .createHmac('sha256', secret)
>>         .update(val)
>>         .digest('base64')
>>         .replace(/\=+$/, '');
>> };
>> ```

> 接收请求时，检查签名，如下所示：
> ```JS
> // 取出口令部分进行签名，对比用户提交的值
> var unsign = function (val, secret) {
>     var str = val.slice(0, val.lastIndexOf('.'));
>     return sign(str, secret) == val ? str : false;
> };
> ```

>> 但是如果攻击者通过某种方式获取了一个真实的口令和签名，他就能实现身份的伪装。一种方案是**将客户端的某些独有信息与口令作为原值，然后签名**

>> 但是原始用户与攻击者之间也存在上述信息相同的可能性，如局域网出口IP相同，相同的客户端信息等，不过纳入这些考虑能够提高安全性

>> 将口令存在Cookie中不容易被他人获取，但是一些别的漏洞可能导致这个口令被泄漏，典型的有XSS漏洞

### 8.1.6 缓存

> 为了提高性能，YSlow中也提到几条关于缓存的规则。
> * 添加`Expires`或`Cache-Control`到报文头中。
> * 配置`ETags`。
> * 让**Ajax**可缓存。

>> RFC 2616规范对此有一定的描述，只有遵循约定，整个缓存机制才能有效建立。

>> **`POST`、`DELETE`、`PUT`这类带行为性的请求操作一般不做任何缓存，大多数缓存只应用在`GET`请求中**

> 条件请求，就是在普通的`GET`请求报文中，附带`If-Modified-Since`字段，如下所示：
> ```HTTP
> If-Modified-Since: Sun, 03 Feb 2013 06:01:12 GMT
> ```
> 时间戳有一些缺陷存在。
> * 文件的时间戳改动但内容并不一定改动。
> * 时间戳只能精确到秒级别，更新频繁的内容将无法生效。

>> `ETag`的全称是Entity Tag，由服务器端生成，服务器端可以决定它的生成规则

> **如果根据文件内容生成散列值，那么条件请求将不会受到时间戳改动造成的带宽浪费**。下面是根据内容生成散列值的方法：
> ```JS
> var getHash = function (str) {
>     var shasum = crypto.createHash('sha1');
>     return shasum.update(str).digest('base64');
> };
> ```

> ETag的请求和响应是`If-None-Match`/`ETag`，如下所示：
> ```JS
> var handle = function (req, res) {
>     fs.readFile(filename, function(err, file) {
>         var hash = getHash(file);
>         var noneMatch = req.headers['if-none-match'];
>         if (hash === noneMatch) {
>             res.writeHead(304, "Not Modified");
>             res.end();
>         } else {
>             res.setHeader("ETag", hash);
>             res.writeHead(200, "Ok");
>             res.end(file);
>         }
>     });
> };
> ```

> 尽管条件请求可以在文件内容没有修改的情况下节省带宽，但是它依然会发起一个HTTP请求，使得客户端依然会花一定时间来等待响应。

>> 如同YSlow规则里提到的，在响应里设置`Expires`或`Cache-Control`头，浏览器将根据该值进行缓存。

>> 在服务器端设置`Expires`可以告知浏览器要缓存文件内容，如下代码所示：

>> `Expires`是一个GMT格式的时间字符串。浏览器在接到这个过期值后，只要本地还存在这个缓存文件，在到期时间之前它都不会再发起请求。

>> 但是`Expires`的缺陷在于浏览器与服务器之间的时间可能不一致

> **`Cache-Control`** 以更丰富的形式，实现相同的功能，如下所示：
> ```JS
> var handle = function (req, res) {
>     fs.readFile(filename, function(err, file) {
>         res.setHeader("Cache-Control", "max-age=" + 10 * 365 * 24 * 60 * 60 * 1000);
>         res.writeHead(200, "Ok");
>         res.end(file);
>     });
> };
> ```

>> **`Cache-Control`能够避免浏览器端与服务器端时间不同步带来的不一致性问题**，只要进行类似倒计时的方式计算过期时间即可

>> `Cache-Control`的值还能设置`public`、`private`、`no-cache`、`no-store`等**能够更精细地控制缓存的选项**

>> 由于在HTTP1.0时还不支持`max-age`，如今的服务器端在模块的支持下多半**同时对`Expires`和`Cache-Control`进行支持**。在浏览器中如果两个值同时存在，且被同时支持时，`max-age`会覆盖`Expires`。

>> 一旦**内容有所更新时，我们就让浏览器发起新的URL请求，使得新内容能够被客户端更新**

> 一般的更新机制有如下两种。
> * 每次发布，路径中跟随Web应用的版本号：http://url.com/`?v=20130501`。
> * 每次发布，路径中跟随该文件内容的hash值：http://url.com/`?hash=afadfadwe`。

>> **大体来说，根据文件内容的hash值进行缓存淘汰会更加高效**

### 8.1.7 Basic认证

> 如果一个页面需要Basic认证，它会检查请求报文头中的`Authorization`字段的内容，该字段的值由认证方式和加密值构成，如下所示：
> ```sh
> $ curl -v "http://user:pass@www.baidu.com/"
> > GET / HTTP/1.1
> > Authorization: Basic dXNlcjpwYXNz
> > User-Agent: curl/7.24.0 (x86_64-apple-darwin12.0) libcurl/7.24.0 OpenSSL/0.9.8r zlib/1.2.5
> > Host: www.baidu.com
> > Accept: */*
> ```

>> 如果用户首次访问该网页，URL地址中也没携带认证内容，那么浏览器会响应一个401未授权的状态码

>> **Basic认证有太多的缺点，它虽然经过Base64加密后在网络中传送，但是这近乎于明文，十分危险，一般只有在HTTPS的情况下才会使用**。不过Basic认证的支持范围十分广泛，几乎所有的浏览器都支持它。

## 8.2 数据上传

> 通过报头的`Transfer-Encoding`或`Content-Length`即可判断请求中是否带有内容，如下所示：
> ```JS
> var hasBody = function(req) {
    > return 'transfer-encoding' in req.headers || > 'content-length' in req.headers;
> };
> ```
> * **2023/08/30发表想法**  
>   “内容”：即请求体

> **报文内容部分会通过`data`事件触发**，我们只需以流的方式处理即可，如下所示：
> ```JS
> function (req, res) {
>     if (hasBody(req)) {
>         var buffers = [];
>         req.on('data', function (chunk) {
>             buffers.push(chunk);
>         });
>         req.on('end', function () {
>             req.rawBody = Buffer.concat(buffers).toString();
>             handle(req, res);
>         });
>     } else {
>         handle(req, res);
>     }
> }
> ```
> 将接收到的Buffer列表转化为一个Buffer对象后，再转换为没有乱码的字符串

>> 默认的**表单提交**，请求头中的`Content-Type`字段值为`application/x-www-form-urlencoded`

> 它的**报文体**内容跟查询字符串相同：
> ```URL
> foo=bar&baz=val
> ```
> 因此**解析**它十分容易：
> ```JS
> var handle = function (req, res) {
>     if (req.headers['content-type'] === 'application/x-www-form-urlencoded') {
>         req.body = querystring.parse(req.rawBody);
>     }
>     todo(req, res);
> };
> ```

>> 常见的提交还有JSON和XML文件等，判断和解析他们的原理都比较相似，都是依据`Content-Type`中的值决定，其中**JSON**类型的值为`application/json`, **XML**的值为`application/xml`

> 在`Content-Type`中可能还附带如下所示的**编码**信息：
> ```HTTP
> Content-Type: application/json; charset=utf-8
> ```
> 所以在做判断时，需要注意区分，如下所示：
> ```JS
> var mime = function (req) {
>     var str = req.headers['content-type'] || '';
>     return str.split('; ')[0];
> };
> ```

> 社区有支持**XML**文件到JSON对象转换的库，这里以`xml2js`模块为例，如下所示：
> ```JS
> var xml2js = require('xml2js');
> 
> var handle = function (req, res) {
>     if (mime(req) === 'application/xml') {
>         xml2js.parseString(req.rawBody, function (err, xml) {
>             if (err) {
>                 // 异常内容，响应Bad request
>                 res.writeHead(400);
>                 res.end('Invalid XML');
>                 return;
>             }
>             req.body = xml;
>             todo(req, res);
>         });
>     }
> };
> ```

> 在前端HTML代码中，特殊表单与普通表单的差异在于该表单中可以含有 **`file` 类型**的控件，以及需要指定表单属性`enctype`为`multipart/form-data`，如下所示：
> ```HTML
> <form action="/upload" method="post" enctype="multipart/form-data">
>     <label for="username">Username:</label> <input type="text" name="username" id="username" />
>     <label for="file">Filename:</label> <input type="file" name="file" id="file" />
>     <br />
>     <input type="submit" name="submit" value="Submit" />
> </form>
> ```

> 浏览器在遇到`multipart/form-data`表单提交时，构造的请求报文与普通表单完全不同。首先它的报头中最为特殊的如下所示：
> ```HTTP
> Content-Type: multipart/form-data; boundary=AaB03x
> Content-Length: 18231
> ```

> 值得注意的一点是，由于是文件上传，那么像普通表单、JSON或XML那样先接收内容再解析的方式将变得不可接受。**接收大小未知的数据量时，我们需要十分谨慎**，如下所示：
> ```JS
> function (req, res) {
>     if (hasBody(req)) {
>         var done = function () {
>             handle(req, res);
>         };
>         if (mime(req) === 'application/json') {
>             parseJSON(req, done);
>         } else if (mime(req) === 'application/xml') {
>             parseXML(req, done);
>         } else if (mime(req) === 'multipart/form-data') {
>             parseMultipart(req, done);
>         }
>     } else {
>         handle(req, res);
>     }
> }
> ```
> 这里要介绍到的**模块**是`formidable`。它**基于流式处理解析报文**，将接收到的文件写入到系统的临时文件夹中，并返回对应的路径，如下所示：
> ```JS
> var formidable = require('formidable');
> function (req, res) {
>     if (hasBody(req)) {
>         if (mime(req) === 'multipart/form-data') {
>             var form = new formidable.IncomingForm();
>             form.parse(req, function(err, fields, files) {
>             req.body = fields;
>             req.files = files;
>             handle(req, res);
>             });
>         }
>     } else {
>         handle(req, res);
>     }
> }
> ```

> 在解析表单、JSON和XML部分，我们采取的策略是先保存用户提交的所有数据，然后再解析处理，最后才传递给业务逻辑。这种策略存在潜在的问题是，它**仅仅适合数据量小的提交请求**，一旦数据量过大，将发生内存被占光的情况。
>
> 要解决这个问题主要有两个方案。
> * **限制上传内容的大小**，一旦超过限制，停止接收数据，并响应`400`状态码。
> * 通过**流式解析**，将数据流导向到磁盘中，Node只保留文件路径等小数据

> 这里介绍一下Connect中采用的上传数据量的限制方式，如下所示：
> ```JS
> var bytes = 1024;
> 
> function (req, res) {
>     var received = 0;
>     var len = req.headers['content-length'] ? parseInt(req.headers['content-length'], 10) : null;
> 
>     // 如果内容超过长度限制，返回请求实体过长的状态码
>     if (len && len > bytes) {
>         res.writeHead(413);
>         res.end();
>         return;
>     }
>     // limit
>     req.on('data', function (chunk) {
>         received += chunk.length;
>         if (received > bytes) {
>             // 停止接收数据，触发end()
>             req.destroy();
>         }
>     });
> 
>     handle(req, res);
> };
> ```
> **由包含`Content-Length`的请求报文判断是否长度超过限制**的，超过则直接响应`413`状态码
>
> 对于没有`Content-Length`的请求报文，略微简略一点，**在每个`data`事件中判定**即可。

> * **2023/08/30发表想法**  
>   ChatGPT：“base64编码是一种将二进制数据转换为ASCII字符的编码方式，每3个字节会被编码为4个可打印字符，因此，编码后的字符串长度会比原字节长度大1/3。”
> 
> * **2023/08/30发表想法**  
>   即 Token
>
> **解决CSRF攻击的方案有添加随机值的方式**，如下所示：
> ```JS
> var generateRandom = function(len) {
>     return crypto.randomBytes(Math.ceil(len * 3 / 4))
>         .toString('base64')
>         .slice(0, len);
> };
> ```
> 也就是说，为每个请求的用户，**在Session中赋予**一个随机值，如下所示：
> ```JS
> var token = req.session._csrf || (req.session._csrf = generateRandom(24));
> ```
> **在做页面渲染的过程中，将这个_csrf值告之前端**，如下所示：
> ```HTML
> <form id="test" method="POST" action="http://domain_a.com/guestbook">
>     <input type="hidden" name="content" value="vim是这个世界上最好的编辑器" />
>     <input type="hidden" name="_csrf" value="<%=_csrf%>" />
> </form>
> ```
> **在接收端做一次校验**就能轻易地识别出该请求是否为伪造的，如下所示：
> ```JS
> function (req, res) {
>     var token = req.session._csrf || (req.session._csrf = generateRandom(24));
> 
>     var _csrf = req.body._csrf;
>     if (token ! == _csrf) {
>         res.writeHead(403);
>         res.end("禁止访问");
>     } else {
>         handle(req, res);
>     }
> }
> ```
> _csrf字段**也可以存在于查询字符串或者请求头中**。

## 8.3 路由解析

### 8.3.1 文件路径型

>> 现今大多数的服务器都能很智能地根据后缀同时服务动态和静态文件。这种方式在Node中不太常见，主要原因是文件的后缀都是．js，分不清是后端脚本，还是前端脚本，这可不是什么好的设计。而且Node中Web服务器与应用业务脚本是一体的，无须按这种方式实现。

### 8.3.2 MVC

> **MVC模型**的主要思想是将业务逻辑按职责分离，主要分为以下几种。
> * **模型（Model）**，数据相关的操作和封装。
> * **视图（View）**，视图的渲染。
> * **控制器（Controller）**，一组行为的集合。
>
> 这是目前最为经典的分层模式，大致而言，它的工作模式如下说明。
> * 路由解析，根据URL**寻找到对应的控制器**和行为。
> * 行为**调用相关的模型**，进行数据操作。
> * 数据操作结束后，**调用视图**和相关数据进行页面渲染，输出到客户端。

>> 如何根据URL做**路由映射**，这里有两个分支实现。一种方式是**通过手工关联映射**，一种是**自然关联映射**。前者会有一个对应的路由文件来将URL映射到对应的控制器，后者没有这样的文件。

#### 1．手工映射

>> 较为原始
>
>> 对URL的要求十分灵活，几乎没有格式上的限制

> 添加一个**映射**的方法
> ```JS
> var routes = [];
> 
> var use = function (path, action) {
>     routes.push([path, action]);
> };
> ```
>
> 在入口程序中**判断URL**，然后执行对应的逻辑
> ```JS
> function (req, res) {
>     var pathname = url.parse(req.url).pathname;
>     for (var i = 0; i < routes.length; i++) {
>         var route = routes[i];
>         if (pathname === route[0]) {
>             var action = route[1];
>             action(req, res);
>             return;
>         }
>     }
>     // 处理404请求
>     handle404(req, res);
> }
> ```

##### ●正则匹配

> 在通过`use`注册路由时需要将路径转换为一个**正则表达式**，然后通过它来进行匹配
> ```JS
> var pathRegexp = function(path) {
>     path = path
>         .concat(strict ? '' : '/? ')
>         .replace(/\/\(/g, '(? :/')
>         .replace(/(\/)? (\.)? :(\w+)(? :(\(.*? \)))? (\? )? (\*)? /g, function(_, slash, format, key, capture,
>     optional, star){
>             slash = slash || '';
>             return ''
>             + (optional ? '' : slash)
>             + '(? :'
>             + (optional ? slash : '')
>             + (format || '') + (capture || (format && '([^/.]+? )' || '([^/]+? )')) + ')'
>             + (optional || '')
>             + (star ? '(/*)? ' : '');
>         })
>         .replace(/([\/.])/g, '\\$1')
>         .replace(/\*/g, '(.*)');
>     return new RegExp('^' + path + '$');
> }
> ```

> 重新改进**注册**部分：
> ```JS
> var use = function (path, action) {
>     routes.push([pathRegexp(path), action]);
> };
> ```
>
> 以及**匹配**部分：
> ```JS
>         function (req, res) {
>           var pathname = url.parse(req.url).pathname;
>           for (var i = 0; i < routes.length; i++) {
>             var route = routes[i];
>             // 正则匹配
>             if (route[0].exec(pathname)) {
>               var action = route[1];
>               action(req, res);
>               return;
>             }
>           }
>           // 处理404请求
>           handle404(req, res);
>         }
> ```

> 还需要进一步**将匹配到的内容抽取出来**
>
> 第一步就是将**键值**抽取出来，如下所示：
> ```JS
> var pathRegexp = function(path) {
>     var keys = [];
> 
>     path = path
>         .concat(strict ? '' : '/? ')
>         .replace(/\/\(/g, '(? :/')
>         .replace(/(\/)? (\.)? :(\w+)(? :(\(.*? \)))? (\? )? (\*)? /g, function(_, slash, format, key, capture,
>             optional, star){
>             // 将匹配到的键值保存起来
>             keys.push(key);
>             slash = slash || '';
>             return ''
>             + (optional ? '' : slash)
>             + '(? :'
>             + (optional ? slash : '')
>             + (format || '') + (capture || (format && '([^/.]+? )' || '([^/]+? )')) + ')'
>             + (optional || '')
>             + (star ? '(/*)? ' : '');
>         })
>         .replace(/([\/.])/g, '\\$1')
>         .replace(/\*/g, '(.*)');
> 
>     return {
>         keys: keys,
>         regexp: new RegExp('^' + path + '$')
>     };
> }
> ```

> 根据抽取的键值和实际的URL得到键值匹配到的实际值，并设置到req.params处，如下所示：
> ```JS
> function (req, res) {
>     var pathname = url.parse(req.url).pathname;
>     for (var i = 0; i < routes.length; i++) {
>         var route = routes[i];
>         // 正则匹配
>         var reg = route[0].regexp;
>         var keys = route[0].keys;
>         var matched = reg.exec(pathname);
>         if (matched) {
>             // 抽取具体值
>             var params = {};
>             for (var i = 0, l = keys.length; i < l; i++) {
>                 var value = matched[i + 1];
>                 if (value) {
>                     params[keys[i]] = value;
>                 }
>             }
>             req.params = params;
> 
>             var action = route[1];
>             action(req, res);
>             return;
>         }
>     }
>     // 处理404请求
>     handle404(req, res);
> }
> ```

#### 2．自然映射

>> 无须去维护路由映射

> 将如下路径进行了划分处理：
> ```URL
> /controller/action/param1/param2/param3
> ```
>
> 以/`user`/`setting`/`12`/`1987`为例，它会按约定去找controllers目录下的user文件，将其`require`出来后，调用这个文件模块的`setting()`方法，而其余的值作为参数直接传递给这个方法。
> ```JS
> function (req, res) {
>     var pathname = url.parse(req.url).pathname;
>     var paths = pathname.split('/');
>     var controller = paths[1] || 'index';
>     var action = paths[2] || 'index';
>     var args = paths.slice(3);
>     var module;
>     try {
>         // require的缓存机制使得只有第一次是阻塞的
>         module = require('./controllers/' + controller);
>     } catch (ex) {
>         handle500(req, res);
>         return;
>     }
>     var method = module[action]
>     if (method) {
>         method.apply(null, [req, res].concat(args));
>     } else {
>         handle500(req, res);
>     }
> }
> ```

> 由于这种自然映射的方式没有指明参数的名称，所以无法采用`req.params`的方式提取，但是直接通过参数获取更简洁，如下所示：
> ```JS
> exports.setting = function (req, res, month, year) {
>     // 如果路径为/user/setting/12/1987，那么month为12, year为1987
>     // TODO
> };
> ```

>> 手工映射也能将值作为参数进行传递，而不是通过req.params。但是这个观点见仁见智，这里不做比较和讨论

### 8.3.3 RESTful

>> MVC模式大行其道了很多年，直到RESTful的流行，大家才意识到URL也可以设计得很规范，请求方法也能作为逻辑分发的单元。

>> REST的全称是**Representational State Transfer**，中文含义为**表现层状态转化**。

>> 在RESTful设计中，资源的具体**格式**由请求报头中的`Accept`字段和服务器端的支持情况来决定。

>> 靠谱的服务器端应该要顾及这个字段，然后根据自己能响应的格式做出响应

>> **在响应报文中，通过`Content-Type`字段告知客户端是什么格式**

>> 所以REST的设计就是，通过URL设计资源、请求方法定义资源的操作，通过`Accept`决定资源的表现形式。

>> RESTful与MVC设计并不冲突，而且是更好的改进。相比MVC, RESTful只是将HTTP请求方法也加入了路由的过程，以及在URL路径上体现得更资源化。

> 在RESTful的场景下，我们需要**区分请求方法**设计。示例如下所示：
> ```JS
> var routes = {'all': []};
> var app = {};
> app.use = function (path, action) {
>     routes.all.push([pathRegexp(path), action]);
> };
> 
> ['get', 'put', 'delete', 'post'].forEach(function (method) {
>     routes[method] = [];
>     app[method] = function (path, action) {
>         routes[method].push([pathRegexp(path), action]);
>     };
> });
> ```

> 将**匹配**的部分抽取为match()方法，如下所示：
> ```JS
> var match = function (pathname, routes) {
>     for (var i = 0; i < routes.length; i++) {
>     var route = routes[i];
>     // 正则匹配
>     var reg = route[0].regexp;
>     var keys = route[0].keys;
>     var matched = reg.exec(pathname);
>     if (matched) {
>         // 抽取具体值
>         var params = {};
>         for (var i = 0, l = keys.length; i < l; i++) {
>             var value = matched[i + 1];
>             if (value) {
>                 params[keys[i]] = value;
>             }
>         }
>         req.params = params;
> 
>         var action = route[1];
>         action(req, res);
>         return true;
>     }
>     }
>     return false;
> };
> ```

> 改进我们的**分发**部分，如下所示：
> ```JS
> function (req, res) {
>     var pathname = url.parse(req.url).pathname;
>     // 将请求方法变为小写
>     var method = req.method.toLowerCase();
>     if (routes.hasOwnPerperty(method)) {
>         // 根据请求方法分发
>         if (match(pathname, routes[method])) {
>             return;
>         } else {
>             // 如果路径没有匹配成功，尝试让all()来处理
>             if (match(pathname, routes.all)) {
>                 return;
>             }
>         }
>     } else {
>         // 直接让all()来处理
>         if (match(pathname, routes.all)) {
>             return;
>         }
>     }
>     // 处理404请求
>     handle404(req, res);
> }
> ```

>> 通过自然映射也能完成RESTful的支持，但是根据Controller/Action的约定必须要转化为Resource/Method的约定，此处已经引出实现思路，不再详述

## 8.4 中间件

>> **中间件（middleware）**

>> 如今中间件的含义借指了这种**封装底层细节，为上层提供更方便服务**的意义，并非限定在操作系统层面。

>> 中间件的行为比较类似Java中过滤器（filter）的工作原理，就是在进入具体的业务处理之前，先让过滤器处理。

>> 每个中间件处理掉相对简单的逻辑，最终汇成强大的基础框架

>> 由于中间件就是前述的那些基本功能，所以它的上下文也就是请求对象和响应对象：req和res。

> 这里我们还是采用Connect的设计，通过尾触发的方式实现。一个基本的中间件会是如下的形式：
> ```JS
> var middleware = function (req, res, next) {
>     // TODO
>     next();
> }
> ```

> 接下来我们需要组织起这些中间件。这里我们将路由分离开来，将中间件和具体业务逻辑都看成业务处理单元，**改进use()方法**如下所示：
> ```JS
> app.use = function (path) {
>     var handle = {
>         // 第一个参数作为路径
>         path: pathRegexp(path),
>         // 其他的都是处理单元
>         stack: Array.prototype.slice.call(arguments, 1)
>     };
>     routes.all.push(handle);
> };
> ```

> **匹配**部分也需要进行修改，如下所示：
> ```JS
> var match = function (pathname, routes) {
>     for (var i = 0; i < routes.length; i++) {
>         var route = routes[i];
>         // 正则匹配
>         var reg = route.path.regexp;
>         var matched = reg.exec(pathname);
>         if (matched) {
>             // 抽取具体值
>             // 代码省略
>             // 将中间件数组交给handle()方法处理
>             handle(req, res, route.stack);
>             return true;
>         }
>     }
>     return false;
> };
> ```

> 中间件具体如何**调动**都交给了handle()方法处理，该方法封装后，递归性地执行数组中的中间件，每个中间件执行完成后，按照约定调用传入next()方法以触发下一个中间件执行（或者直接响应），直到最后的业务逻辑。代码如下所示：
> ```JS
> var handle = function (req, res, stack) {
>     var next = function () {
>         // 从stack数组中取出中间件并执行
>         var middleware = stack.shift();
>         if (middleware) {
>             // 传入next()函数自身，使中间件能够执行结束后递归
>             middleware(req, res, next);
>         }
>     };
> 
>     // 启动执行
>     next();
> };
> ```

> 持续**改进我们的use()方法**以适应参数的变化，如下所示：
> ```JS
> app.use = function (path) {
>     var handle;
>     if (typeof path === 'string') {
>         handle = {
>             // 第一个参数作为路径
>             path: pathRegexp(path),
>             // 其他的都是处理单元
>             stack: Array.prototype.slice.call(arguments, 1)
>         };
>     } else {
>         handle = {
>             // 第一个参数作为路径
>             path: pathRegexp('/'),
>             // 其他的都是处理单元
>             stack: Array.prototype.slice.call(arguments, 0)
>         };
>     }
>     routes.all.push(handle);
>     };
> ```

> 持续改进我们的**匹配**过程，与前面一旦一次匹配后就不再执行后续匹配不同，还会继续后续逻辑，这里我们将所有匹配到中间件的都暂时保存起来，如下所示：
> ```JS
> var match = function (pathname, routes) {
>     var stacks = [];
>     for (var i = 0; i < routes.length; i++) {
>         var route = routes[i];
>         // 正则匹配
>         var reg = route.path.regexp;
>         var matched = reg.exec(pathname);
>         if (matched) {
>             // 抽取具体值
>             // 代码省略
>             // 将中间件都保存起来
>             stacks = stacks.concat(route.stack);
>         }
>     }
>     return stacks;
> };
> ```

> 持续改进**分发**的过程：
> ```JS
> function (req, res) {
>     var pathname = url.parse(req.url).pathname;
>     // 将请求方法变为小写
>     var method = req.method.toLowerCase();
>     // 获取all()方法里的中间件
>     var stacks = match(pathname, routes.all);
>     if (routes.hasOwnPerperty(method)) {
>         // 根据请求方法分发，获取相关的中间件
>         stacks.concat(match(pathname, routes[method]));
>     }
> 
>     if (stacks.length) {
>         handle(req, res, stacks);
>     } else {
>         // 处理404请求
>         handle404(req, res);
>     }
> }
> ```

> **为next()方法添加err参数，并捕获中间件直接抛出的同步异常**，如下所示：
> ```JS
> var handle = function (req, res, stack) {
>     var next = function (err) {
>         if (err) {
>             return handle500(err, req, res, stack);
>         }
>         // 从stack数组中取出中间件并执行
>         var middleware = stack.shift();
>         if (middleware) {
>             // 传入next()函数自身，使中间件能够执行结束后递归
>             try {
>                 middleware(req, res, next);
>             } catch (ex) {
>                 next(err);
>             }
>         }
>     };
> 
>     // 启动执行
>     next();
> };
> ```

> 由于异步方法的异常不能直接捕获（在第4章中有过阐述），**中间件异步产生的异常需要自己传递出来**，如下所示：
> ```JS
> var session = function (req, res, next) {
>     var id = req.cookies.sessionid;
>     store.get(id, function (err, session) {
>         if (err) {
>             // 将异常通过next()传递
>             return next(err);
>         }
>         req.session = session;
>         next();
>     });
> };
> ```

> **用于处理异常的中间件**的设计与普通中间件略有差别，它的参数有4个，如下所示：
> ```JS
> var middleware = function (err, req, res, next) {
>     // TODO
>     next();
> };
> ```

> handle500()方法将会对中间件**按参数进行选取**，然后递归执行
> ```JS
> var handle500 = function (err, req, res, stack) {
>     // 选取异常处理中间件
>     stack = stack.filter(function (middleware) {
>         return middleware.length === 4;
>     });
> 
>     var next = function () {
>         // 从stack数组中取出中间件并执行
>         var middleware = stack.shift();
>         if (middleware) {
>             // 传递异常对象
>             middleware(err, req, res, next);
>         }
>     };
> 
>     // 启动执行
>     next();
> };
> ```

### 8.4.2 中间件与性能

> 中间件的编写和使用是需要一番考究的。下面是两个**主要的能提升的点**。
> * **编写高效的中间件。**
> * **合理利用路由**，避免不必要的中间件执行。

#### 1．编写高效的中间件

> 一旦中间件被匹配，那么每个请求都会使该中间件执行一次，哪怕它只浪费1毫秒的执行时间，都会让我们的QPS显著下降。**常见的优化方法**有几种。
> * 使用高效的方法。必要时通过jsperf.com测试基准性能。
> * **缓存**需要重复计算的结果（需要**控制缓存用量**，原因在第5章阐述过）。
> * **避免不必要的计算**。比如HTTP报文体的解析，对于GET方法完全不需要。

#### 2．合理使用路由

>> 需要做的是**提升匹配成功率**，那么就不能使用默认的`/`路径来进行匹配了，因为它的误伤率太高。给它添加一个更好的路由路径是个不错的选择

### 8.4.3 小结

>> 从某种角度来讲它就是Unix哲学的一个实现，专注简单，小而美，然后通过组合使用，发挥出强大的能量。

## 8.5 页面渲染

### 8.5.1 内容响应

#### 1. MIME

>> 内容响应的过程中，**响应报头中的Content-\*字段十分重要**。

>> 浏览器正是通过不同的`Content-Type`的值来决定采用不同的渲染方式

>> JSON文件的值为`application/json`、XML文件的值为`application/xml`、PDF文件的值为`application/pdf`

>> **社区有专有的mime模块**可以用判段文件类型

> `Content-Type`的值中还可以包含一些参数，如**字符集**。示例如下：
> ```HTTP
> Content-Type: text/javascript; charset=utf-8
> ```

#### 2．附件下载

>> `Content-Disposition`字段影响的行为是**客户端会根据它的值判断是应该将报文数据当做即时浏览的内容，还是可下载的附件**。当内容只需即时查看时，它的值为`inline`，当数据可以存为附件时，它的值为`attachment`

> `Content-Disposition`字段还能通过参数**指定保存时应该使用的文件名**。示例如下：
> ```HTTP
> Content-Disposition: attachment; filename="filename.ext"
> ```

> 如果我们要**设计一个响应附件下载的API**（res.sendfile），我们的方法大致是如下这样的：
> ```JS
> res.sendfile = function (filepath) {
>     fs.stat(filepath, function(err, stat) {
>         var stream = fs.createReadStream(filepath);
>         // 设置内容
>         res.setHeader('Content-Type', mime.lookup(filepath));
>        // 设置长度
>        res.setHeader('Content-Length', stat.size);
>        // 设置为附件
>        res.setHeader('Content-Disposition' 'attachment; filename="' + path.basename(filepath) + '"');
>         res.writeHead(200);
>         stream.pipe(res);
>     });
> };
> ```

#### 3．响应JSON

> 响应JSON数据，我们也可以如下这样进行封装：
> ```JS
> res.json = function (json) {
>     res.setHeader('Content-Type', 'application/json');
>     res.writeHead(200);
>     res.end(JSON.stringify(json));
> };
> ```

#### 4．响应跳转

> 也可以封装出一个快捷的方法实现跳转，如下所示：
> ```JS
> res.redirect = function (url) {
>     res.setHeader('Location', url);
>     res.writeHead(302);
>     res.end('Redirect to ' + url);
> };
> ```

### 8.5.3 模板

> 最早的服务器端动态页面开发，是在CGI程序或servlet中输出HTML片段，通过网络流输出到客户端，客户端将其渲染到用户界面上。这种逻辑代码与HTML输出的代码混杂在一起的开发方式，导致**一个小小的UI改动都要大动干戈，甚至需要重新编译**。为了改良这种情况，使HTML与逻辑代码分离开来，催生出一些**服务器端动态网页技术，如ASP、PHP、JSP**。
>
> 这样的方法虽然一定程度上减轻了开发维护的难度，但是**页面里还是充斥着大量的逻辑代码**。这催生了MVC在动态网页技术中的发展

> 如ASP、PHP、JSP，它们其实就是最早的模板技术
>
> 这个时期的模板极度依赖上下文，甚至要处理整个HTTP的请求对象。
>
> 随后模板语言的发展使得模板可以脱离上下文环境，只有数据对象就可以执行。
>
> 这类模板的缺点在于**它的实现与宿主语言有很大的关联性**，由于各种语言采用的模板语言不同，包含各种特殊标记，导致移植性较差。

>> 如今异构系统越来越多，模板能够应用到多门编程语言中的这种需求也开始呈现出来。

>> 由于Node与前端都采用相同的执行语言JavaScript，所以一套模板语言也无须为它编写两套不同的模板引擎就能轻松地跨前后端共用

>> 模板技术并不是什么神秘的技术，它干的实际上是拼接字符串这样很底层的活，只是各种模板有着各自的优缺点和技巧

>> ❑ 语法分解。提取出普通字符串和表达式，这个过程通常用正则表达式匹配出来，`<% =%>`的正则表达式为`/<%=([\s\S]+?)%>/g`。

>> 为了能够最终与数据一起执行生成字符串，我们需要将原始的模板字符串转换成一个函数对象。这个过程称为模板编译

>> 为了提升模板渲染的性能速度，我们通常会采用**模板预编译**的方式。

>> with关键字是JavaScript中饱受Douglas Crockford指责的设计，细节在本书附录C中有详细描述。

>> 大多数模板都提供了**转义**的功能。转义就是将能形成HTML标签的字符转换成安全的字符，**这些字符主要有`&`、`<`、`>`、`"`、`'`**

>> **转义函数**如下：
> ```JS
> var escape = function (html) {
>     return String(html)
>         .replace(/&(? ! \w+; )/g, '&amp; ')
>         .replace(/</g, '&lt; ')
>         .replace(/>/g, '&gt; ')
>         .replace(/"/g, '&quot; ')
>         .replace(/'/g, '&#039; '); // IE下不支持&apos;（单引号）转义
> };
> ```

>> **不确定要输出HTML标签的字符最好都转义**

>> 为了让转义和非转义表现得更方便，`<%=%>`和`<%-%>`分别表示为转义和非转义的情况

>> **在模板技术的使用中，时刻不要忘记转义，尤其是与输入有关的变量一定要转义**

>> 为了让上述模板变得强大一点，我们为它添加逻辑代码，使得模板可以像ASP、PHP那样控制页面渲染。

>> **与文件系统集成**之后，再**引入缓存**，可以很好地解决性能问题，接口也大大得到简化

>> 由于模板文件内容都不太大，也不属于动态改动的，所以使用**进程的内存**来缓存编译结果，并不会引起太大的垃圾回收问题。

>> 实现**子模板**的诀窍就是先将include语句进行替换，再进行整体性编译
>
>> 在正式编译前进行子模板替换

>> 这些重复的内容主要用来布局，为了能将这些布局模板重用起来，模板技术必须支持**布局视图**。

> 从前文的实现细节中我们可以看到一些模板引擎的优化步骤，主要有如下几种。
> * **缓存**模板文件。
> * **缓存**模板文件编译后的函数。

> 对于字符串处理，本节中用的是字符串直接相加，有的模板引擎采用数组存储的方式，最后将所有字符串相连。
>
> * **2023/10/17发表想法**  
>   ChatGPT：这两种方法是处理字符串拼接的两种常见方式。在每一次字符串拼接时，JavaScript会新创建一个字符串对象，这就意味着需要进行内存分配和垃圾回收，这在处理大量字符串拼接的情况下，可能会影响性能。
> 
>   1. 字符串直接相加：简单易懂，但是在大量拼接时性能较差。
>       ```javascript
>       let str = "";
>       for (let i = 0; i < 10000; i++) {
>         str += "test";
>       }
>       ```
>   2. 数组存储、最后连接：处理大量拼接时性能较好，因为避免了频繁的内存分配和垃圾回收。
>       ```javascript
>       let arr = [];
>       for (let i = 0; i < 10000; i++) {
>         arr.push("test");
>       }
>       let str = arr.join("");
>       ```
>   总的来说，如果你需要进行大量的字符串拼接，通常建议使用数组的push方法和join方法，它比使用+或+=拼接字符串的效率更高。如果只是进行简单的字符串拼接，那么直接使用+或+=通常更简单、更直观。
>
> * **2023/10/17发表想法**  
>   关于JavaScript字符串处理的性能比较，许多相关的讨论和文章可以支持这一观点，例如：
> 
>   1. StackOverflow中的一个回答详细地讨论了这个问题： [Which method is better for concatenation strings in JavaScript](https://stackoverflow.com/questions/7299010/which-method-should-be-used-for-javascript-string-concatenation-especially-for-pe)
> 
>   2. 知乎上有专家对此进行了详细讨论：[在JavaScript中，什么时候用Join连接字符串比用+好？](https://www.zhihu.com/question/20670550)
> 
>   3. Google前员工对此做了详细的研究和解释：["High Performance JavaScript (Build Faster Web Application Interfaces)" by Nicholas C. Zakas](https://www.oreilly.com/library/view/high-performance-javascript/9781449382308/)
> 
>   上面这些参考内容都在一定程度上证明了在处理大量字符串连接时，使用数组的`join()`方法通常比直接使用"+"拼接字符串性能更好。
> 
>   这并不意味着任何时候都应使用`join()`，其实在处理小字符串或者只有少量的拼接操作时，使用"+"来连接字符串在绝大多数现代JavaScript引擎中效率都是非常高的，只有在特定的使用场景（例如在循环内进行大量的字符串拼接）中，`join()`才会表现出明显优势。
> 
> * **2023/10/18发表想法**  
>   尼古拉斯·泽卡斯《高性能 JavaScript》5.1.2 数组项连接：“在大多数浏览器中，数组项连接比其他字符串连接方法更慢，但事实上，它却是在IE7及更早版本浏览器中合并大量字符串唯一高效的途径”

>> 指定变量而不用with可以减少切换上下文

>> 如今各种各样的模板具备不同的特性和性能。最知名的有EJS、Jade等

### 8.5.4 Bigpipe

>> Bigpipe的解决思路则是将页面分割成多个部分（pagelet），先向用户输出没有数据的布局（框架），将每个部分逐步输出到前端，再最终渲染填充框架，完成整个网页的渲染。
>
>> 与普通的数据输出不同，这里的数据输出之后需要被前端脚本处理，是故需要对它进行封装处理

>> 要完成Bigpipe这样逐步渲染页面的过程，其实通过Ajax也能完成，但是**Ajax的背后是HTTP调用，要耗费更多的网络连接，Bigpipe获取数据则与当前页面共用相同的网络连接，开销十分小**。

>> 完成Bigpipe所要涉及的细节较多，比MVC中的直接渲染要复杂许多，建议在网站**重要的且数据请求时间较长的**页面中使用。

## 8.6 总结

>> 现在知名和成熟的Web框架有Connect、Express等，本章中的内容在这些框架中都有实现


# 第9章 玩转进程

## 9.1 服务模型的变迁

### 9.1.1 石器时代：同步

>> 它的服务模式是一次只为一个请求服务，所有请求都得按次序等待服务。这意味除了当前的请求被处理外，其余请求都处于耽误的状态。
>
>> 假设每次响应服务耗用的时间稳定为N秒，这类服务的QPS为1/N

### 9.1.2 青铜时代：复制进程

>> 一个简单的改进是通过进程的复制同时服务更多的请求和用户。这样每个连接都需要一个进程来服务，即100个连接需要启动100个进程来进行服务，**这是非常昂贵的代价**。
>
>> 在进程复制的过程中，需要复制进程内部的状态，对于每个连接都进行这样的复制的话，**相同的状态将会在内存中存在很多份**，造成浪费。并且这个过程由于要复制较多的数据，**启动是较为缓慢的**

>> 预复制（prefork）被引入服务模型中，即预先复制一定数量的进程。同时将进程复用，避免进程创建、销毁带来的开销
>
>> 这个模型**并不具备伸缩性**，一旦并发请求过高，内存使用随着进程数的增长将会被耗尽

>> 假设通过进行复制和预复制的方式搭建的服务器有资源的限制，且进程数上限为M，那这类服务的QPS为M/N。

### 9.1.3 白银时代：多线程

>> 线程相对进程的**开销要小许多**，并且线程之间**可以共享数据**，内存浪费的问题可以得到解决，并且**利用线程池**可以减少创建和销毁线程的开销。

>> 每个线程都拥有自己独立的堆栈，**这个堆栈都需要占用一定的内存空间**
>
>> 操作系统内核在切换线程的同时也要切换线程的上下文，**当线程数量过多时，时间将会被耗用在上下文切换中**

>> 如果忽略掉多线程上下文切换的开销，假设线程所占用的资源为进程的1/L，受资源上限的影响，**它的QPS则为M * L/N**。
>
> * **2023/10/19发表想法**  
    M: 线程数上限  
    N: 假设每次响应服务耗用的时间稳定为N秒

### 9.1.4 黄金时代：事件驱动

>> Apache就是采用多线程/多进程模型实现的，当并发增长到上万时，内存耗用的问题将会暴露出来，这即是著名的C10k问题。

>> 为了解决高并发问题，基于事件驱动的服务模型出现了，像Node与Nginx均是基于事件驱动的方式实现的，**采用单线程避免了不必要的内存开销和上下文切换开销**。

>> **基于事件的服务模型存在的问题即是本章起始时提及的两个问题：CPU的利用率和进程的健壮性**

>> 单线程的架构并不少见，其中尤以PHP最为知名

>> 对于Node来说，所有请求的上下文都是统一的，它的稳定性是亟需解决的问题

>> 影响事件驱动服务模型性能的点在于CPU的计算能力，它的上限决定这类服务模型的性能上限，但它**不受多进程或多线程模式中资源上限的影响，可伸缩性远比前两者高**

## 9.2 多进程架构

>> Node提供了`child_process`模块

>> 提供了`child_process.fork()`函数供我们实现**进程的复制**

>> 图9-1就是著名的Master-Worker模式，又称**主从模式**

>> 图9-1中的进程分为两种：**主进程**和**工作进程**。这是典型的分布式架构中用于并行处理业务的模式，具备较好的可伸缩性和稳定性

>> 主进程不负责具体的业务处理，而是负责**调度或管理工作进程**，它是趋向于稳定的

>> 工作进程负责**具体的业务处理**，因为业务的多种多样，甚至一项业务由多人开发完成，所以工作进程的稳定性值得开发者关注。

>> 通过`fork()`复制的进程都是一个**独立**的进程，这个进程中有着独立而全新的V8实例。

>> 切记 **`fork()` 进程是昂贵的**

>> **启动多个进程只是为了充分将CPU资源利用起来，而不是为了解决并发问题**

>> `child_process`模块给予Node可以随意创建子进程（child_process）的能力。它提供了**4个方法用于创建子进程**。
>> *  `spawn()`：启动一个子进程来执行命令。
>> *  `exec()`：启动一个子进程来执行命令，与spawn()不同的是其接口不同，它有一个回调函数获知子进程的状况。
>> *  `execFile()`：启动一个子进程来执行可执行文件。
>> *  `fork()`：与spawn()类似，不同点在于它创建Node的子进程只需指定要执行的JavaScript文件模块即可。

>> 这里的可执行文件是指可以直接执行的文件，如果是JavaScript文件通过`execFile()`运行，它的首行内容必须添加如下代码：
>> ```sh
>> #! /usr/bin/env node
>> ```

>> 在前端浏览器中，JavaScript主线程与UI渲染共用同一个线程。执行JavaScript的时候UI渲染是停滞的，渲染UI时，JavaScript是停滞的，两者互相阻塞。长时间执行JavaScript将会造成UI停顿不响应

>> HTML5提出了WebWorker API。**WebWorker允许创建工作线程并在后台运行**，使得一些阻塞较为严重的计算不影响主线程上的UI渲染。

>> 主线程与工作线程之间通过 **`onmessage()` 和 `postMessage()`** 进行通信
> 
> **2023/09/07发表想法**  
> H5 API（浏览器）

>> 子进程对象则由`send()`方法实现**主进程向子进程发送数据**，`message`事件实现**收听子进程发来的数据**
> 
> **2023/09/07发表想法**  
> Node API

>> 通过消息传递内容，而不是共享或直接操作相关资源，这是较为轻量和无依赖的做法。

>> Node中对应示例如下所示：
>> ```JS
>> // parent.js
>> var cp = require('child_process');
>> var n = cp.fork(__dirname + '/sub.js');
>> 
>> n.on('message', function (m) {
>>     console.log('PARENT got message:', m);
>> });
>> 
>> n.send({hello: 'world'});
>> // sub.js
>> process.on('message', function (m) {
>>     console.log('CHILD got message:', m);
>> });
>> 
>> process.send({foo: 'bar'});
>> ```

>> 创建子进程之后，为了实现父子进程之间的通信，父进程与子进程之间将会创建IPC通道。通过IPC通道，父子进程之间才能通过`message`和`send()`传递消息

>> IPC的全称是Inter-Process Communication，即进程间通信。

>> **注意** 只有启动的子进程是Node进程时，子进程才会根据环境变量去连接IPC通道，对于其他类型的子进程则无法实现进程间通信，除非其他进程也按约定去连接这个已经创建好的IPC通道。

>> **主进程监听主端口（如80），主进程对外接收所有的网络请求，再将这些请求分别代理到不同的端口的进程上**

>> 进程每接收到一个连接，将会用掉一个文件描述符

>> 操作系统的文件描述符是有限的，代理方案浪费掉一倍数量的文件描述符的做法影响了系统的扩展能力。

>> Node在版本v0.5.9引入了进程间发送句柄的功能。`send()`方法除了能通过IPC发送数据外，还能发送句柄，第二个可选参数就是句柄，如下所示：

>> 句柄是一种可以用来标识资源的引用，它的内部包含了指向对象的文件描述符。

>> 可以去掉代理这种方案，使主进程接收到socket请求后，将这个socket直接发送给工作进程

>> 主进程发送完句柄并关闭监听之后，成为了如图9-6所示的结构。主进程发送完句柄并关闭监听后的结构我们神奇地发现，多个子进程可以同时监听相同端口，再没有EADDRINUSE异常发生了。

>> 目前子进程对象`send()`方法可以发送的句柄类型包括如下几种。
> * net.Socket。TCP套接字。
> * net.Server。TCP服务器，任意建立在TCP服务上的应用层服务都可以享受到它带来的好处。
> * net.Native。C++层面的TCP套接字或IPC管道。
> * dgram.Socket。UDP套接字。
> * dgram.Native。C++层面的UDP套接字。

>> 最终发送到IPC通道中的信息都是字符串，`send()`方法能发送消息和句柄并不意味着它能发送任意对象

>> 子进程可以读取到父进程发来的消息，将字符串通过`JSON.parse()`解析还原为对象后，才触发`message`事件将消息体传递给应用层使用

>> 消息对象还要被进行过滤处理

>> 独立启动的进程中，TCP服务器端socket套接字的文件描述符并不相同，导致监听到相同的端口时会抛出异常

>> 对于`send()`发送的句柄还原出来的服务而言，它们的文件描述符是相同的，所以监听相同端口不会引起异常

>> 通过这些基础技术，用`child_process`模块在单机上搭建Node集群是件相对容易的事情。

## 9.3 集群稳定之路

>> Node还有如下这些事件。
>> * `error`：当子进程无法被复制创建、无法被杀死、无法发送消息时会触发该事件。
>> * `exit`：子进程退出时触发该事件，子进程如果是正常退出，这个事件的第一个参数为退出码，否则为`null`。如果进程是通过`kill()`方法被杀死的，会得到第二个参数，它表示杀死进程时的信号。
>> * `close`：在子进程的标准输入输出流中止时触发该事件，参数与`exit`相同。
>> * `disconnect`：在父进程或子进程中调用`disconnect()`方法时触发该事件，在调用该方法时将关闭监听IPC通道。
>>
>> 上述这些事件是父进程能监听到的与子进程相关的事件

>> 还能通过`kill()`方法给子进程发送消息。`kill()`方法并不能真正地将通过IPC相连的子进程杀死，它只是给子进程发送了一个系统信号。默认情况下，父进程将通过`kill()`方法给子进程发送一个`SIGTERM`信号。它与进程默认的`kill()`方法类似，如下所示：
>> ```JS
>> // 子进程
>> child.kill([signal]);
>> // 当前进程
>> process.kill(pid, [signal]);
>> ```
> 
> **2023/09/12发表想法**  
> 15) SIGTERM

>> 在命令行中执行`kill -l`可以看到详细的信号列表

>> Node提供了这些信号对应的信号事件，每个进程都可以监听这些信号事件。

>> 进程在收到响应信号时，应当做出约定的行为

### 9.3.2 自动重启

>> 通过监听子进程的exit事件来获知其退出的信息，接着前文的多进程架构，我们**在主进程上要加入一些子进程管理的机制**，比如**重新启动一个工作进程来继续服务**。示意图如图9-8所示。代码如下所示：
>> ```JS
>> // master.js
>> var fork = require('child_process').fork;
>> var cpus = require('os').cpus();
>> 
>> var server = require('net').createServer();
>> server.listen(1337);
>> 
>> var workers = {};
>> var createWorker = function () {
>>     var worker = fork(__dirname + '/worker.js');
>>     // 退出时重新启动新的进程
>>     worker.on('exit', function () {
>>         console.log('Worker ' + worker.pid + ' exited.');
>>         delete workers[worker.pid];
>>         createWorker();
>>     });
>>     // 句柄转发
>>     worker.send('server', server);
>>     workers[worker.pid] = worker;
>>     console.log('Create worker. pid: ' + worker.pid);
>> };
>> 
>> for (var i = 0; i < cpus.length; i++) {
>>     createWorker();
>> }
>> 
>> // 进程自己退出时，让所有工作进程退出
>> process.on('exit', function () {
>>     for (var pid in workers) {
>>         workers[pid].kill();
>>     }
>> });
>> ```

>> 在实际业务中，可能有隐藏的bug导致工作进程退出，那么我们需要仔细地处理这种异常，如下所示：
```JS
// worker.js
var http = require('http');
var server = http.createServer(function (req, res) {
    res.writeHead(200, {'Content-Type': 'text/plain'});
    res.end('handled by child, pid is ' + process.pid + '\n');
});

var worker;
process.on('message', function (m, tcp) {
    if (m === 'server') {
        worker = tcp;
        worker.on('connection', function (socket) {
            server.emit('connection', socket);
        });
    }
});

process.on('uncaughtException', function () {
    // 停止接收新的连接
    worker.close(function () {
        // 所有已有连接断开后，退出进程
        process.exit(1);
    });
});
```

>> 不能等到工作进程退出后才重启新的工作进程。当然也不能暴力退出进程，因为这样会导致已连接的用户直接断开

>> 在退出的流程中增加一个自杀（suicide）信号。**工作进程在得知要退出时，向主进程发送一个自杀信号**，然后才停止接收新的连接，当所有连接断开后才退出

>> **主进程在接收到自杀信号后，立即创建新的工作进程服务**。代码改动如下所示：
>> ```JS
>> // worker.js
>> process.on('uncaughtException', function (err) {
>>     process.send({act: 'suicide'});
>>     // 停止接收新的连接
>>     worker.close(function () {
>>         // 所有已有连接断开后，退出进程
>>         process.exit(1);
>>     });
>> });
>> ```

>> 主进程将重启工作进程的任务，从`exit`事件的处理函数中转移到`message`事件的处理函数中，如下所示：
>> ```JS
>> var createWorker = function () {
>>     var worker = fork(__dirname + '/worker.js');
>>     // 启动新的进程
>>     worker.on('message', function (message) {
>>         if (message.act === 'suicide') {
>>             createWorker();
>>         }
>>     });
>>     worker.on('exit', function () {
>>         console.log('Worker ' + worker.pid + ' exited.');
>>         delete workers[worker.pid];
>>     });
>>     worker.send('server', server);
>>     workers[worker.pid] = worker;
>>     console.log('Create worker. pid: ' + worker.pid);
>> };
>> ```

>> **为已有连接的断开设置一个超时时间**是必要的，在限定时间里强制退出的设置如下所示：
>> ```JS
>> process.on('uncaughtException', function (err) {
>>     process.send({act: 'suicide'});
>>     // 停止接收新的连接
>>     worker.close(function () {
>>         // 所有已有连接断开后，退出进程
>>         process.exit(1);
>>     });
>>     // 5秒后退出进程
>>     setTimeout(function () {
>>         process.exit(1);
>>     }, 5000);
>> });
>> ```

>> 进程中如果出现未能捕获的异常，就意味着有那么一段代码在健壮性上是不合格的。

>> **退出进程前，通过日志记录下问题所在**是必须要做的事情，它可以帮我们很好地定位和追踪代码异常出现的位置，如下所示：
>> ```JS
>> process.on('uncaughtException', function (err) {
>>     // 记录日志
>>     logger.error(err);
>>     // 发送自杀信号
>>     process.send({act: 'suicide'});
>>     // 停止接收新的连接
>>     worker.close(function () {
>>         // 所有已有连接断开后，退出进程
>>         process.exit(1);
>>     });
>>     // 5秒后退出进程
>>     setTimeout(function () {
>>         process.exit(1);
>>     }, 5000);
>> });
>> ```

>> 工作进程不能无限制地被重启，如果启动的过程中就发生了错误，或者启动后接到连接就收到错误，会导致工作进程被频繁重启，这种频繁重启不属于我们捕捉未知异常的情况

>> 在满足一定规则的限制下，不应当反复重启。比如**在单位时间内规定只能重启多少次，超过限制就触发`giveup`事件**，告知放弃重启工作进程这个重要事件

>> 我们引入一个队列来做标记，在每次重启工作进程之间进行打点并判断重启是否太过频繁，如下所示：
>> ```JS
>> // 重启次数
>> var limit = 10;
>> // 时间单位
>> var during = 60000;
>> var restart = [];
>> var isTooFrequently = function () {
>>     // 记录重启时间
>>     var time = Date.now();
>>     var length = restart.push(time);
>>     if (length > limit) {
>>         // 取出最后10个记录
>>         restart = restart.slice(limit * -1);
>>     }
>>     // 最后一次重启到前10次重启之间的时间间隔
>>     return restart.length >= limit && restart[restart.length -1] - restart[0] < during;
>> };
>> 
>> var workers = {};
>> var createWorker = function () {
>>     // 检查是否太过频繁
>>     if (isTooFrequently()) {
>>         // 触发giveup事件后，不再重启
>>         process.emit('giveup', length, during);
>>         return;
>>     }
>>     var worker = fork(__dirname + '/worker.js');
>>     worker.on('exit', function () {
>>         console.log('Worker ' + worker.pid + ' exited.');
>>         delete workers[worker.pid];
>>     });
>>     // 重新启动新的进程
>>     worker.on('message', function (message) {
>>         if (message.act === 'suicide') {
>>             createWorker();
>>         }
>>     });
>>     // 句柄转发
>>     worker.send('server', server);
>>     workers[worker.pid] = worker;
>>     console.log('Create worker. pid: ' + worker.pid);
>> };
>> ```

>> `uncaughtException`只代表集群中某个工作进程退出

>> `giveup`事件则表示集群中没有任何进程服务了

>> **我们应在`giveup`事件中添加重要日志，并让监控系统监视到这个严重错误，进而报警等。**

### 9.3.3 负载均衡

>> 保证多个处理单元工作量公平的策略叫负载均衡

>> Node默认提供的机制是采用操作系统的抢占式策略。所谓的抢占式就是在一堆工作进程中，闲着的进程对到来的请求进行争抢，谁抢到谁服务。

>> 对于Node而言，需要分清的是它的繁忙是由CPU、I/O两个部分构成的，影响抢占的是CPU的繁忙度。对不同的业务，可能存在I/O繁忙，而CPU较为空闲的情况，这可能造成某个进程能够抢到较多请求，形成负载不均衡的情况

>> Node在v0.11中提供了一种新的策略使得负载均衡更合理，这种新的策略叫Round-Robin，又叫轮叫调度

>> 轮叫调度的工作方式是由主进程接受连接，将其依次分发给工作进程。分发的策略是在N个工作进程中，每次选择第i = (i + 1) mod n个进程来发送连接。在cluster模块中启用它的方式如下：
>> ```JS
>> // 启用Round-Robin
>> cluster.schedulingPolicy = cluster.SCHED_RR
>> // 不启用Round-Robin
>> cluster.schedulingPolicy = cluster.SCHED_NONE
>> ```
>> 或者在环境变量中设置NODE_CLUSTER_SCHED_POLICY的值，如下所示：
>> ```sh
>> export NODE_CLUSTER_SCHED_POLICY=rr
>> export NODE_CLUSTER_SCHED_POLICY=none
>> ```

>> Round-Robin非常简单，可以避免CPU和I/O繁忙差异导致的负载不均衡

>> Round-Robin策略也可以通过代理服务器来实现，但是它会导致服务器上消耗的文件描述符是平常方式的两倍

### 9.3.4 状态共享

>> **解决数据共享最直接、简单的方式就是通过第三方来进行数据存储**

>> 实现状态同步的机制有两种，一种是各个子进程去向第三方进行定时轮询

>> 定时轮询带来的问题是轮询时间不能过密，如果子进程过多，会形成并发处理，如果数据没有发生改变，这些轮询会没有意义，白白增加查询状态的开销。如果轮询时间过长，数据发生改变时，不能及时更新到子进程中，会有一定的延迟。

>> 一种改进的方式是**当数据发生更新时，主动通知子进程**

>> 这个过程仍然不能脱离轮询，但我们可以减少轮询的进程数量

>> 我们将这种用来发送通知和查询状态是否更改的进程叫做通知进程。为了不混合业务逻辑，可以**将这个进程设计为只进行轮询和通知**，不处理任何业务逻辑，示意图如图9-11所示

>> 这种推送机制如果按进程间信号传递，在跨多台服务器时会无效，是故可以考虑**采用TCP或UDP**的方案

>> 进程在启动时从通知服务处除了读取第一次数据外，还将进程信息注册到通知服务处。一旦通过轮询发现有数据更新后，根据注册信息，将更新后的数据发送给工作进程

>> 单一的通知服务轮询带来的压力并不大，所以可以将轮询时间调整得较短，一旦发现更新，就能实时地推送到各个子进程中

## 9.4 Cluster模块

>> 在v0.8版本之前，实现多进程架构必须通过`child_process`来实现

>> 对于本章开头提到的创建Node进程集群，`cluster`实现起来也是很轻松的事情，如下所示：
>> ```JS
>> // cluster.js
>> var cluster = require('cluster');
>> 
>> cluster.setupMaster({
>>     exec: "worker.js"
>> });
>> 
>> var cpus = require('os').cpus();
>> for (var i = 0; i < cpus.length; i++) {
>>     cluster.fork();
>> }
>> ```

>> 在进程中判断是主进程还是工作进程，主要取决于环境变量中是否有`NODE_UNIQUE_ID`，如下所示：
>> ```JS
>> cluster.isWorker = ('NODE_UNIQUE_ID' in process.env);
>> cluster.isMaster = (cluster.isWorker === false);
>> ```

>> 建议用`cluster.setupMaster()`这个API，将主进程和工作进程从代码上完全剥离

### 9.4.1 Cluster工作原理

>> `cluster`模块就是`child_process`和`net`模块的组合应用

>> 如果进程是通过`cluster.fork()`复制出来的，那么它的环境变量里就存在`NODE_UNIQUE_ID`

>> 在`cluster`内部隐式创建TCP服务器的方式对使用者来说十分透明，但也正是这种方式使得它无法如直接使用`child_process`那样灵活

>> 在`cluster`模块应用中，一个主进程只能管理一组工作进程，如图9-12所示

>> 对于自行通过`child_process`来操作时，则可以更灵活地控制工作进程，甚至控制多组工作进程。其原因在于自行通过`child_process`操作子进程时，可以隐式地创建多个TCP服务器，使得子进程可以共享多个的服务器端socket，如图9-13所示

### 9.4.2 Cluster事件

>> 对于健壮性处理，`cluster`模块也暴露了相当多的事件。
>> * `fork`：复制一个工作进程后触发该事件。
>> * `online`：复制好一个工作进程后，工作进程主动发送一条`online`消息给主进程，主进程收到消息后，触发该事件。
>> * `listening`：工作进程中调用`listen()`（共享了服务器端Socket）后，发送一条`listening`消息给主进程，主进程收到消息后，触发该事件。
>> * `disconnect`：主进程和工作进程之间IPC通道断开后会触发该事件。
>> * `exit`：有工作进程退出时触发该事件。
>> * `setup`:`cluster.setupMaster()`执行后触发该事件。

## 9.5 总结

>> 一旦主进程出现问题，所有子进程将会失去管理。在Node的进程管理之外，还需要用监听进程数量或监听日志的方式确保整个系统的稳定性，即使主进程出错退出，也能及时得到监控警报


# 第10章 测试

## 10.1 单元测试

>> **单元测试在软件项目中扮演着举足轻重的角色，是几种软件质量保证的方法中投入产出比最高的一种。**

### 10.1.1 单元测试的意义

>> 对于Node开源社区而言（共有3万多模块），作为一个不知名的开发者，其产出的模块如果连单元测试都没有提供，使用者在挑选模块时，内心也会闪过多个“靠谱吗”的疑问。

>> API升级时，**测试用例可以很好地检查是否向下兼容**

>> 单元测试只是在早期会多花费一定的成本，但这个成本要远远低于后期深陷维护泥潭的投入。

>> 当无法为一段代码写出单元测试时，这段代码必然有坏味道，这会为开发者带来心理压力，这样的代码最需要重构。好代码的单元测试必然是轻量的，重构和写单元测试之间是一个相互促进的步骤

>> 简单而言，编写可测试代码有以下几个原则可以遵循。
>> * **单一职责**。如果一段代码承担的职责越多，为其编写单元测试的时候就要构造更多的输入数据

### 10.1.2 单元测试介绍

#### 1．断言

>> **断言用于检查程序在运行时是否满足期望**

>> JavaScript的断言规范最早来自于CommonJS的单元测试规范（详见[http://wiki.commonjs.org/wiki/Unit_Testing/1.0](http://wiki.commonjs.org/wiki/Unit_Testing/1.0)）, Node实现了规范中的断言部分。

>> 一旦assert.equal()不满足期望，将会抛出 **`AssertionError` 异常**

>> 在断言规范中，我们定义了以下几种检测方法。
>> * `ok()`：判断结果是否**为真**。
>> * `equal()`：判断实际值与期望值是否**相等**。
>> * `notEqual()`：判断实际值与期望值是否**不相等**。
>> * `deepEqual()`：判断实际值与期望值是否**深度相等**（对象或数组的元素是否相等）。
>> * `notDeepEqual()`：判断实际值与期望值是否**不深度相等**。
>> * `strictEqual()`：判断实际值与期望值是否**严格相等**（相当于===）。
>> * `notStrictEqual()`：判断实际值与期望值是否**不严格相等**（相当于！==）。
>> * `throws()`：判断代码块是否**抛出异常**。
>> 
>> 除此之外，Node的assert模块还扩充了如下两个断言方法。
>> * `doesNotThrow()`：判断代码块是否**没有抛出异常**。
>> * `ifError()`：判断实际值是否为一个**假值**（null、undefined、0、''、false），如果实际值为真值，将会抛出异常。

#### 2．测试框架

>> 断言一旦检查失败，将会抛出异常停止整个应用，这对于做大规模断言检查时并不友好。**更通用的做法是，记录下抛出的异常并继续执行，最后生成测试报告。这些任务的承担者就是测试框架**

>> 这里我们要介绍的优秀单元测试框架是**mocha**

>> 调用`mocha --reporters`即可查看所有的报告格式

>> 执行`mocha -R<reporter>`命令即可采用这些报告。**json**报告因为其格式非常通用，多用于将结果传递给其他程序进行处理，而**html-cov**则用于可视化地观察代码覆盖率。

#### 3．测试代码的文件组织

>> **包规范中定义了测试代码存在于test目录中**，而模块代码存在于lib目录下

>> 在包描述文件（package.json）中添加相应模块的依赖关系。**由于mocha只在运行测试时需要，所以添加到`devDependencies`节点即可**

#### 4．测试用例

>> 测试用例最少需要通过正向测试和反向测试来保证测试对功能的覆盖，这是最基本的测试用例。对于Node而言，不仅有这样简单的方法调用，还有**异步代码**和**超时设置**需要关注。

##### ●异步测试

>> 测试用例方法`it()`接受两个参数；**用例标题（title）**和**回调函数（fn）**

>> **如果是异步调用，在执行测试用例时，会将一个函数`done()`注入为实参，测试代码需要主动调用这个函数通知测试框架当前测试用例执行完成**，然后测试框架才进行下一个测试用例的执行

##### ●超时设置

>> 如果代码偶然出错，导致`done()`一直没有执行，将会造成所有的测试用例处于暂停状态，这显然不是框架所期望的

>> **mocha给所有涉及异步的测试用例添加了超时限制**，如果一个用例的执行时间超过了预期时间，将会记录下一个超时错误，然后执行下一个测试用例。

>> mocha的**默认超时时间为2000毫秒**

>> 一般情况下，通过`mocha -t <ms>`设置所有用例的超时时间

>> 可以在测试用例`it`中调用`this.timeout(ms)`实现对单个用例的特殊设置，示例代码如下：
>> ```JS
>> it('should take less than 500ms', function (done) {
>>     this.timeout(500);
>>     setTimeout(done, 300);
>> });
>> ```

>> 也可以在描述`describe`中调用`this.timeout(ms)`设置描述下当前层级的所有用例：
>> ```JS
>> describe('a suite of tests', function(){
>>     this.timeout(500);
>>     it('should take less than 500ms', function (done) {
>>         setTimeout(done, 300);
>>     });
>> 
>>     it('should take less than 500ms as well', function (done) {
>>         setTimeout(done, 200);
>>     });
>> });
>> ```

#### 5．测试覆盖率

>> 若要探知这个测试用例对源代码的覆盖率，需要一种工具来统计每一行代码是否执行，这里要介绍的相关工具是`jscover`模块。通过`npm install jscover -g`的方式可以安装该模块。

>> **调用`jscover lib lib-cov`进行源代码的编译**吧。jscover会将lib目录下的．js文件编译到lib-cov目录下

>> 每一行原始代码的前面都有一些`_$jscoverage`的代码出现，它们将会在执行时统计每一行代码被执行了多少次

>> 为了得到测试覆盖率，必须在运行测试用例时执行编译之后的代码

>> 为了区分这种注入代码和原始代码的区别，我们**在模块的入口文件（通常是包目录下的index.js）中需要做简单的区别**，示例代码如下：
>> ```JS
>> module.exports = process.env.LIB_COV ? require('./lib-cov/index') : require('./lib/index');
>> ```

>> **执行以下命令行**即可得到覆盖率的输出结果：
>> ```sh
>> // 设置当前命令行有效的变量
>> export LIB_COV=1
>> mocha -R html-cov > coverage.html
>> ```

>> 在使用过程中，也可以使用`json-cov`报告，这样结果数据对其余系统较为友好。事实上，`html-cov`报告即是采用`json-cov`的数据与模板渲染而成的。

>> `jscover`模块虽然已经够用，但是还有两个问题。
>>* **它的编译部分是通过Java实现的**，这样环境依赖上就多出了Java。
>>* 它需要编译代码到一个额外的新目录，这个过程相对麻烦。
>> 
>> 而 **`blanket` 模块**解决了这两个问题，它由纯JavaScript实现，编译代码的过程也是隐式的，无须配置额外的目录，对于原模块项目没有额外的侵入。

>> `blanket`将编译的步骤注入在`require`中，而不是去额外编译成文件，执行测试时再去引用编译后的文件

>> 在所有测试用例运行之前**通过`--require`选项引入**它即可：
>> ```sh
>> mocha --require blanket -R html-cov > coverage.html
>> ```

>> **在包描述文件中配置`scripts`节点**。在`scripts`节点中，`pattern`属性用以匹配需要编译的文件：
>> ```JSON
>> "scripts": {
>>     "blanket": {
>>         "pattern": "eventproxy/lib"
>>     }
>> },
>> ```

>> `blanket`则不同，它的原理与第2章中讲到的文件模块编译相同。我们知道，对于．js文件，Node会将它的编译逻辑封装在`require.extensions['.js']`中。`blanket`正是在这个环节中实现了编译，将覆盖率的追踪代码插入到原始代码中，然后再由原始模块处理逻辑进行处理

>> 使用`blanket`之后，就无须配置环境变量了，也无须根据环境去判断引入哪种代码，所以下面这行代码就不再需要了：
>> ```JS
>> module.exports = process.env.LIB_COV ? require('./lib-cov/index') : require('./lib/index');
>> ```

#### 6. mock

>> 开发者常常会遗漏掉一些异常案例，其中相当大一部分原因在于异常的情况较难实现

>> 在测试领域里，模拟异常其实是一个不小的科目，它有着一个特殊的名词：mock。

>> 以下面的代码为例，文件系统的异常是绝对不容易呈现的

>> 为了解决这个问题，我们通过**伪造`fs.readFileSync()`方法抛出错误来触发异常**。同时为了保证该测试用例不影响其余用例，我们需要**在执行完后还原它**。为此，前面提到的 **`before()` 和 `after()` 钩子函数**派上了用场，相关代码如下：
>> ```JS
>> describe("getContent", function () {
>>     var _readFileSync;
>>     before(function () {
>>         _readFileSync = fs.readFileSync;
>>         fs.readFileSync = function (filename, encoding) {
>>             throw new Error("mock readFileSync error"));
>>         };
>>     });
>>     // it();
>>     after(function () {
>>         fs.readFileSync = _readFileSync;
>>     })
>> });
>> ```

>> **如果每个测试用例执行前后都要进行设置和还原，就使用`beforeEach()`和`afterEach()`这两个钩子函数**

>> 由于mock的过程比较烦琐，这里推荐一个模块来解决此事——`muk`，示例代码如下：
>> ```JS
>> var fs = require('fs');
>> var muk = require('muk');
>> before(function () {
>>     muk(fs, 'readFileSync', function(path, encoding) {
>>         throw new Error("mock readFileSync error");
>>     });
>> });
>> 
>> // it();
>> 
>> after(function () {
>>     muk.restore();
>> });
>> ```
>>
>> 当有多个用例时，相关代码如下：
>> ```JS
>> var fs = require('fs');
>> var muk = require('muk');
>> beforeEach(function () {
>>     muk(fs, 'readFileSync', function(path, encoding) {
>>         throw new Error("mock readFileSync error");
>>     });
>> });
>> 
>> // it();
>> // it();
>> 
>> afterEach(function () {
>>     muk.restore();
>> });
>> ```
>>
>> 模拟时无须临时缓存正确引用，用例执行结束后调用`muk.restore()`恢复即可。

>> **对于异步方法的模拟，需要十分小心是否将异步方法模拟为同步**。下面的mock方式可能会引起意外的结果：
>> ```JS
>> fs.readFile = function (filename, encoding, callback) {
>>     callback(new Error("mock readFile error"));
>> };
>> ```
>>
>> 正确的mock方式是尽量让mock后的行为与原始行为保持一致，相关代码如下：
>> ```JS
>> fs.readFile = function (filename, encoding, callback) {
>>     process.nextTick(function () {
>>         callback(new Error("mock readFile error"));
>>     });
>> };
>> ```
>>
>> **模拟异步方法时，我们调用`process.nextTick()`使得回调方法能够异步执行即可。**

#### 7．私有方法的测试

>> `rewire`模块提供了一种巧妙的方式实现对私有方法的访问

>> `rewire`的调用方式与`require`十分类似。对于如下的私有方法，我们获取它并为其执行测试用例非常简单：
>> ```JS
>> var limit = function (num) {
>>     return num < 0 ? 0 : num;
>> };
>> ```
>>
>> 测试用例如下：
>> ```JS
>> it('limit should return success', function () {
>>     var lib = rewire('../lib/index.js');
>>     var litmit = lib.__get__('limit');
>>     litmit(10).should.be.equal(10);
>> });
>> ```

>> 每一个被`rewire`引入的模块都有 **`__set__()` 和 `__get__()` 方法**。它巧妙地利用了闭包的诀窍，在`eval()`执行时，实现了对模块内部局部变量的访问，从而可以将局部变量导出给测试用例调用执行。

### 10.1.3 工程化与自动化

>> Makefile比较小巧灵活，适合用来构建工程

>> 这里需要注意以下两点。
>> * Makefile文件的缩进必须是tab符号，不能用空格。
>> * 记得在包描述文件中配置blanket。

>> 如何持续集成，各个公司都有自己特定的方案，这里介绍一下社区中比较流行的方式——利用travis-ci实现持续集成

>> travis-ci则补足了GitHub在持续集成方面的缺点

>> Git版本控制系统提供了hook机制，用户在push代码后会触发一个hook脚本，而travis-ci即是通过这种方式与GitHub衔接起来的

>> 将你的代码与travis-ci链接起来十分容易，只需如下几步即可完成。
>> 1. 在[https://travis-ci.org/](https://travis-ci.org/)上通过OAuth授权绑定你的GitHub账号。
>> 2. 在GitHub仓库的管理面板（admin）中打开services hook页，在这个页面中可以发现GitHub上提供了很多基于git hook方式的钩子服务。
>> 3. 找到travis服务，点击激活即可

>> 每次将代码push到GitHub的仓库上后，将会触发该钩子服务。

>> 也可以通过travis-ci的管理界面来设置哪些代码仓库开启持续集成服务

>> 它会将项目默认当做Ruby项目

>> 提供一个．travis.yml说明文件，告知travis-ci是哪种类型的项目。Node项目的说明文件如下：
>> ```YAML
>> language: node_js
>> node_js:
>>     - "0.8"
>>     - "0.10"
>> ```

>> travis-ci将会执行`npm test`命令来启动整个测试

>> `mocha -R spec`或`make test`命令应当配置在package.json文件中：
>> ```JSON
>> "scripts": {
>>     "test": "make test"
>> },
>> ```

>> travis-ci提供了一个测试状态的服务。在GitHub上，也会经常看到此类的图标：[插图]或者红色的失败图标。它就是由travis-ci提供的项目状态服务，由如下格式组成：
>> ```URL
>> https://travis-ci.org/<username>/<repo>.png? branch=<branch>
>> ```

>> 还详细记录了每次测试的详细报告和日志，通过这些信息我们可以追踪项目的迭代健康状态

### 10.1.4 小结

>> Express提供了`supertest`辅助库来简化单元测试的编写

>> 如果没有单元测试的覆盖，**依赖方**逻辑发生变化后，很难定位该变动影响的范围。一旦为项目覆盖完善的单元测试，项目的状态将会因为测试报告而了然于心

## 10.2 性能测试

>> 性能测试的范畴比较广泛，包括负载测试、压力测试和基准测试等。由于这部分内容并非Node特有，为了收敛范畴，这里将只会简单介绍下基准测试。

### 10.2.1 基准测试

>> **基准测试要统计的就是在多少时间内执行了多少次某个方法**。为了增强可比性，**一般会以次数作为参照物，然后比较时间**，以此来判别性能的差距。

>> 比较简单直接的方式就是构造相同的输入数据，然后执行相同的次数，最后比较时间。为此我们可以写一个方法来执行这个任务，具体如下所示：
>> ```JS
>> var run = function (name, times, fn, arr, callback) {
>>     var start = (new Date()).getTime();
>>     for (var i = 0; i < times; i++) {
>>         fn(arr, callback);
>>     }
>>     var end = (new Date()).getTime();
>>     console.log('Running %s %d times cost %d ms', name, times, end - start);
>> };
>> ```

>> 为了得到更规范和更好的输出结果，这里介绍`benchmark`这个模块是如何组织基准测试的，相关代码如下：
>> ```JS
>> var Benchmark = require('benchmark');
>> 
>> var suite = new Benchmark.Suite();
>> 
>> var arr = [0, 1, 2, 3, 5, 6];
>> suite.add('nativeMap', function () {
>>     return arr.map(callback);
>> }).add('customMap', function () {
>>     var ret = [];
>>     for (var i = 0; i < arr.length; i++) {
>>         ret.push(callback(arr[i]));
>>     }
>>     return ret;
>> }).on('cycle', function (event) {
>>     console.log(String(event.target));
>> }).on('complete', function() {
>>     console.log('Fastest is ' + this.filter('fastest').pluck('name'));
>> }).run();
>> ```
>> 它通过`suite`来**组织每组测试**，在测试套件中调用`add()`来**添加被测试的代码**。

>> `benchmark`模块并不是简单地统计执行多少次测试代码后对比时间，它对测试有着严密的抽样过程。执行多少次方法取决于采样到的数据能否完成统计。83 runs sampled表示对nativeMap测试的过程中，有83个样本，然后我们根据这些样本，可以推算出标准方差，即±1.99%这部分数据

### 10.2.2 压力测试

>> 对网络接口做压力测试需要考查的几个指标有**吞吐率**、**响应时间**和**并发数**，这些指标反映了服务器的并发处理能力。

>> 最常用的工具是`ab`、`siege`、`http_load`等

>> 下面我们通过`ab`工具来构造压力测试，相关代码如下：
>> ```sh
>> $ ab -c 10-t 3 http://localhost:8001/
>> This is ApacheBench, Version 2.3 <$Revision: 655654 $>
>> Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
>> Licensed to The Apache Software Foundation, http://www.apache.org/
>> 
>> Benchmarking localhost (be patient)
>> Completed 5000 requests
>> Completed 10000 requests
>> Finished 11573 requests
>> 
>> Server Software:
>> Server Hostname:        localhost
>> Server Port:            8001
>> 
>> Document Path:          /
>> Document Length:        10240 bytes
>> 
>> Concurrency Level:      10
>> Time taken for tests:   3.000 seconds
>> Complete requests:      11573
>> Failed requests:        0
>> Write errors:           0
>> Total transferred:      119375495 bytes
>> HTML transferred:       118507520 bytes
>> Requests per second:    3857.60 [#/sec] (mean)
>> Time per request:       2.592 [ms] (mean)
>> Time per request:       0.259 [ms] (mean, across all concurrent requests)
>> Transfer rate:          38858.59 [Kbytes/sec] received
>> 
>> Connection Times (ms)
>>             min  mean[+/-sd] median   max
>> Connect:        0    0   0.3      0      31
>> Processing:     1    2   1.9      2      35
>> Waiting:        0    2   1.9      2      35
>> Total:          1    3   2.0      2      35
>> 
>> Percentage of the requests served within a certain time (ms)
>>     50%      2
>>     66%      3
>>     75%      3
>>     80%      3
>>     90%      3
>>     95%      3
>>     98%      5
>>     99%      6
>>     100%     35 (longest request)
>> ```
>> 上述命令表示10个并发用户持续3秒向服务器端发出请求
>>
>> * Requests per second：这是我们重点关注的一个值，它表示**服务器每秒能处理多少请求**，是重点反映服务器并发能力的指标。这个值又称RPS或QPS。

### 10.2.3 基准测试驱动开发

>> 在“Faster than C”幻灯片中提到了一种他所使用的开发模式，简称也是BDD，全称为Benchmark Driven Development，即基准测试驱动开发

### 10.2.4 测试数据与业务数据的转换

>> 假设某个页面每天的访问量为100万。根据实际业务情况，主要访问量大致集中在10个小时以内，那么换算公式就是：
>>
>> QPS = PV / 10h
>>
>>100万的业务访问量换算为QPS，约等于27.7，即服务器需要每秒处理27.7个请求才能胜任业务量。


# 第11章 产品化

>> 在实际的产品中，需要很多非编码相关的工作以保证项目的进展和产品的正常运行等，这些细节包括工程化、架构、容灾备份、部署和运维等。

## 11.1 项目工程化

>> 所谓的工程化，可以理解为项目的组织能力。体现在文件上，就是文件的组织能力。

>> 目前，在Node的应用中，主流的构建工具还是老牌的make，但它的缺点是只在*nix操作系统下有效。为了实现跨平台，Grunt应运而生。Grunt通过Node写成，借助Node的跨平台能力，实现了很好的平台兼容性。

>> 编码规范的统一一般有几种实现方式，一种是文档式的约定，一种是代码提交时的强制检查。前者靠自觉，后者靠工具。在JSLint和JSHint工具的帮助下，现在已经能够很好地配置规则了。

>> 代码审查需要耗费一定的精力，一些可以自动化完成的工作可以交由工具来自动完成，比如编码规范的检查。但检查后的结果，还需要人工完成确认。

>> 一般还会集成单元测试的执行等环境

## 11.2 部署流程

### 11.2.1 部署环境

>> 对于一些功能而言，它的行为是与具体数据相关的，测试环境中的数据集在种类或者大小上不能够满足测试需求，进而需要在一个**预发布环境**中测试

>> 预发布环境与普通的测试环境的差别在于**它的数据较为接近线上真实的数据**。

>> 我们将普通测试环境称为stage环境，预发布环境称为pre-release环境，实际的生产环境称为product环境

### 11.2.2 部署操作

>> 为了能让进程持续执行，我们可能会用到`nohup`和`&`以不挂断进程的方式执行：
>> ```sh
>> nohup node app.js &
>> ```
>> 启动进程很容易，但是还有两个需求需要考虑——停止进程和重启进程。手工管理的方式会显得烦琐

>> 我们需要一个脚本来实现应用的启动、停止和重启等操作。要完成这样的操作，bash脚本是最精巧又擅长此类需求的

>> bash脚本的内容通过与Web应用以约定的方式来实现

>> 如果没有约定，我们需要找到应用对应的进程，然后调用`kill`命令杀死进程。这通常要调用`ps`来查找，相关代码如下：
>> ```sh
>> $ ps aux | grep node
>> jacksontian     3618   0.0  0.0  2432768    592 s002  R+    3:00PM   0:00.00 grep node
>> jacksontian     3614   0.0  0.4  3054400  32612 s000  S+    2:59PM   0:00.69 /usr/local/bin/node
>> /Users/jacksontian/git/h5/app.js
>> ```

>> 这里所谓的约定是，**主进程在启动时将进程ID写入到一个pid文件中，这个文件可以存放在一个约定的路径下**，如应用的run/app.pid。下面是将pid写入到文件中的示例：
>> ```JS
>> var fs = require('fs');
>> var path = require('path');
>> 
>> var pidfile = path.join(__dirname, 'run/app.pid');
>> fs.writeFileSync(pidfile, process.pid);
>> ```

>> **脚本在停止或重启应用时通过`kill`给进程发送`SIGTERM`信号，而进程收到该信号时删除app.pid文件，同时退出进程**，相关代码如下：
>> ```JS
>> process.on('SIGTERM', function () {
>>     if (fs.existsSync(pidfile)) {
>>         fs.unlinkSync(pidfile);
>>     }
>>     process.exit(0);
>> });
>> ```

>> 下面是一个完整的bash脚本，用于控制应用的启动、停止和重启等操作：
>> ```sh
>> #! /bin/sh
>> DIR=`pwd`
>> NODE=`which node`
>> # get action
>> ACTION=$1
>> 
>> # help
>> usage() {
>>     echo "Usage: ./appctl.sh {start|stop|restart}"
>>     exit 1;
>> }
>> 
>> get_pid() {
>>     if [ -f ./run/app.pid ]; then
>>     echo `cat ./run/app.pid`
>>     fi
>> }
>> 
>> # start app
>> start() {
>>     pid=`get_pid`
>> 
>>     if [ ! -z $pid ]; then
>>     echo 'server is already running'
>>     else
>>     $NODE $DIR/app.js 2>&1 &
>>     echo 'server is running'
>>     fi
>> }
>> 
>> # stop app
>> stop() {
>>     pid=`get_pid`
>>     if [ -z $pid ]; then
>>     echo 'server not running'
>>     else
>>     echo "server is stopping ..."
>>     kill -15 $pid
>>     echo "server stopped ! "
>>     fi
>> }
>> restart() {
>>     stop
>>     sleep 0.5
>>     echo =====
>>     start
>> }
>> 
>> case "$ACTION" in
>>     start)
>>     start
>>     ;;
>>     stop)
>>     stop
>>     ;;
>>     restart)
>>     restart
>>     ;;
>>     *)
>>     usage
>>     ;;
>> esac
>> ```

## 11.3 性能

>> Node产品的性能与许多因素相关，这里我们将范畴缩减到Web应用中来，只评估一些常见的提升性能的方法

>> 对于Web应用而言，最直接有效的莫过于**动静分离**、**多进程架构**、**分布式**

>> 除此之外，**缓存**也能带来很大的性能提升

### 11.3.1 动静分离

>> Node处理静态文件的能力并不算突出。**将图片、脚本、样式表和多媒体等静态文件都引导到专业的静态文件服务器上**，让Node只处理动态请求即可

>> 这个过程可以用**Nginx**或者专业的**CDN**来处理

>> 静态文件请求分离后，对静态请求使用不同的域名或多个域名还能消除掉不必要的Cookie传输和浏览器对下载线程数的限制。

>> 对于静态内容而言无须进行字符串层级的替换，只要保留成Buffer即可。直接进行Buffer传输可以很大程度上提升性能，这在第6章中已演示过。是故能够在动态内容中再将动态内容和静态内容分离，还能进一步提升性能，但这种程度上的控制也许没有普适性，需要较多细节处理

### 11.3.2 启用缓存

>> 如今，**Redis或Memcached**几乎是Web应用的标准配置。如果你的产品需要应对巨大的流量，启用缓存并应用好它，是系统性能瓶颈的关键。

### 11.3.3 多进程架构

>> 需要开发者自己处理多进程的管理。不过好在官方已经有`cluster`模块，在社区也有`pm`、`forever`、`pm2`这样的模块用于进程管理

### 11.3.4 读写分离

>> 进行数据库的读写分离，将数据库进行主从设计，这样读数据操作不再受到写入的影响

## 11.4 日志

### 11.4.1 访问日志

>> **中间件框架Connect在其众多中间件中提供了一个日志中间件**，通过它可以将关键数据按一定格式输出到日志文件中。下面是Connect的一段示例代码：
>> ```JS
>> var app = connect();
>> // 记录访问日志
>> connect.logger.format('home', ':remote-addr :response-time - [:date] ":method :url \
>>     HTTP/:http-version" :status :res[content-length] ":referrer" ":user-agent" :res[content-length]');
>> app.use(connect.logger({
>>     format: 'home',
>>     stream: fs.createWriteStream(__dirname + '/logs/access.log')
>> }));
>> ```

### 11.4.2 异常日志

>> `console`模块在具体实现时，`log`与`info`方法都将信息输出给标准输出`process.stdout`, `warn`与`error`方法则将信息输出到标准错误`process.stderr`

>> `info`和`error`分别是`log`和`warn`的别名

>> **`console`对象上具有一个`Console`属性，它是`console`对象的构造函数。借助这个构造函数，我们可以实现自己的日志对象**，相关代码如下：
>> ```JS
>> var info = fs.createWriteStream(logdir + '/info.log', {flags: 'a', mode: '0666'});
>> var error = fs.createWriteStream(logdir + '/error.log', {flags: 'a', mode: '0666'});
>> 
>> var logger = new console.Console(info, error);
>> ```

>> 对于回调函数中产生的异常，则可以不用过问，交给全局的`uncaughtException`事件去捕获即可。

>> 我的建议是**异常尽量由最上层的调用者捕获记录**，底层调用或中间层调用中出现的异常只要正常传递给上层的调用方即可。底层或中间层调用通常这样写：
>> ```JS
>> exports.find = function (id, callback) {
>>     // 准备SQL
>>     db.query(sql, function (err, rows) {
>>         if (err) {
>>             return callback(err);
>>         }
>>         // 处理结果
>>         var data = rows.sort();
>>         callback(null, data);
>>     });
>> };
>> ```

>> **对于最上层的业务，不能无视下层传递过来的任何异常，需要记录异常**，以便将来排查错误，同时应该对用户给出友好的提示，相关代码如下：
>> ```JS
>> exports.index = function (req, res) {
>>     proxy.find(id, function (err, rows) {
>>         if (err) {
>>             logger.error(err);
>>             res.writeHead(500);
>>             res.end('Error');
>>             return;
>>         }
>>         res.writeHead(200);
>>         res.end(rows);
>>     });
>> };
>> ```

>> **准备一个`format()`方法来封装和格式化异常信息**，该方法的代码如下所示：
>> ```JS
>> var format = function (msg) {
>>     var ret = '';
>>     if (! msg) {
>>         return ret;
>>     }
>> 
>>     var date = moment();
>>     var time = date.format('YYYY-MM-DD HH:mm:ss.SSS');
>>     if (msg instanceof Error) {
>>         var err = {
>>             name: msg.name,
>>             data: msg.data
>>         };
>> 
>>         err.stack = msg.stack;
>>         ret = util.format('%s %s: %s\nHost: %s\nData: %j\n%s\n\n',
>>             time,
>>             err.name,
>>             err.stack,
>>             os.hostname(),
>>             err.data,
>>             time
>>         );
>>         console.log(ret);
>>     } else {
>>         ret = time + ' ' + util.format.apply(util, arguments) + '\n';
>>     }
>>     return ret;
>> };
>> ```

>> 在异常出现时可以**将调用时的数据传递给格式化方法，然后记录下日志**，示例代码如下：
>> ```JS
>> var input = '{error: format}';
>> try {
>>     JSON.parse(input);
>> } catch (ex) {
>>     ex.data = input;
>>     logger.error(format(ex));
>> }
>> ```

>> 对于未捕获的异常，Node提供了机制以免进程直接退出，但是**发生未捕获异常的进程也不能继续在线上进行服务了，因为可能有内存泄漏的风险产生**

>> 退出和重启进程在第9章中已详细描述过，那一章中的示例多是用`console.log()`来记录问题的，但在实际的产品中，需要严格的日志记录

### 11.4.3 日志与数据库

>> 日志文件与数据库写入在性能上处于两个级别，数据库在写入过程中要经历一系列处理，比如锁表、日志等操作。写日志文件则是直接将数据写到磁盘上

>> 如果有大量的访问，可能会存在写入操作大量排队的状况，数据库的消费速度严重低于生产速度，进而导致内存泄漏等

>> 写日志是轻量的方法，将日志分析和日志记录这两个步骤分离开来是较好的选择

>> 日志记录可以在线写，日志分析则可以借助一些工具同步到数据库中，通过离线分析的方式反馈出来。

### 11.4.4 分割日志

>> 设计一个定时器用于**当日期发生更改时，更改日志对象的两个输入流对象**

## 11.5 监控报警

### 11.5.1 监控

#### 1．日志监控

>> 监控**异常日志**文件的变动，将新增的异常按异常类型和数量反映出来

>> 访问日志的监控也能体现出实际的业务QPS值

#### 2．响应时间

>> **响应时间**可以在Nginx一类的反向代理上监控，也可以通过应用自行产生的访问日志来监控

>> 健康的系统响应时间应该是波动较小的、持续均衡的

#### 3．进程监控

>> 监控进程一般是检查操作系统中运行的**应用进程数**

#### 4．磁盘监控

>> 给**磁盘的使用量**设置一个上限

#### 5．内存监控

>> 监控服务器的内存使用状况，可以检查应用中是否存在内存泄漏的状况。

>> 如果突然出现内存异常，也能够追踪到是近期的哪些代码改动导致的问题。

#### 6. CPU占用监控

>> CPU的使用分为用户态、内核态、IOWait等。如果用户态CPU使用率较高，说明服务器上的**应用**需要大量的CPU开销；如果内核态CPU使用率较高，说明服务器花费大量时间进行**进程调度或者系统调用**；IOWait使用率则反应的是CPU**等待磁盘I/O操作**。

>> CPU的使用率中，用户态小于70%、内核态小于35%且整体小于70%时，处于健康状态

>> 监控CPU占用情况，可以帮助分析应用程序在实际业务中的状况。合理设置监控阈值能够很好地预警。

#### 7. CPU load监控

>> CPU load又称CPU平均负载，它用来描述操作系统当前的繁忙程度，可以简单地理解为CPU在单位时间内正在使用和等待使用CPU的平均任务数。

>> **CPU load过高说明进程数量过多**，这在Node中可能体现在用子进程模块反复启动新的进程。监控该值可以防止意外产生。

#### 8. I/O负载

>> 不管Node进程是否与数据库或其他I/O密集的应用共处相同的服务器，我们都应监控该值以防万一。

#### 9．网络监控

>> 虽然网络流量监控的优先级没有上述项目那么高，但还是需要对流量进行监控并设置上限值。

>> 对于正常增长，应当评估是否该增加硬件设备来为更多用户提供服务。

#### 10．应用状态监控

>> **应用还应当提供一种机制来反馈其自身的状态信息，外部监控将会持续性地调用应用的反馈接口来检查它的健康状态**

>> 给监控响应一个**时间戳**，监控方检查时间戳是否正常

>> 将应用的**依赖项**的状态打印出来，如数据库连接是否正常、缓存是否正常等

### 11. DNS监控

>> 对于产品的稳定性，域名DNS状态也需要加入监控。

### 11.5.2 报警的实现

>> * **邮件报警**。如果报警系统由Node编写，可以调用`nodemailer`模块来实现邮件的发送。

>> * **短信或电话报警**。一些短信服务平台提供短信接入服务

### 11.5.3 监控系统的稳定性

>> 监控系统自身的稳定性对应用非常重要

## 11.6 稳定性

>> 部署多台机器也需要考虑如何将请求均匀地分配给各个机器，这需要在机房的级别上架设负载均衡，可能是硬件设备来实现，也可能是软件来实现，比如反向代理。图11-5为负载均衡的示意图。对于状态共享和数据一致性，它们与多进程的问题是一致的，具体可参见第9章

>> 多机房部署是比多机器部署更高层次的部署，目的是为了解决地理位置给用户访问带来的延迟等问题。在容灾方面，机房与机房之间可以互为备份。由于机房与机房之间的网络复杂度再度提升，负载均衡方面需要进一步去统筹规划

>> 在多服务器部署中，要尽量避免多个服务器在相同的实体机上。因为一旦实体机出现故障，导致多台服务器一起停止服务。

>> 还要考虑的是应用依赖的服务的容灾和备份，如依赖的数据库、缓存等服务。

# 附录B 调试Node

>> 执行上述代码时，在命令行中加入`debug`。添加`debug`在命令中后，Node会开启调试功能，内建的客户端会与V8建立连接。下面的输出为执行结果：
>> ```sh
>> $ node debug examples/B/myscript.js
>> < debugger listening on port 5858
>> connecting... ok
>> break in examples/B/myscript.js:2
>> 1 // myscript.js
>>     2 x = 5;
>>     3 setTimeout(function () {
>>     4   debugger;
>> debug>
>> ```

>> Node的调试客户端并没有支持V8的所有命令，只有简单的步进和检查的命令。
>>
>> 其中步进指令主要有如下几个。
>> * cont或c。继续执行。
>> * next或n。执行到下一个断点。
>> * step或s。步进到函数内部。
>> * out或o。从函数内部跳出。
>> * pause。暂停执行。

>> V8提供了如下几种设置断点和清除断点的方法。
>> * setBreakpoint()或sb()。在当前行设置断点
>> * setBreakpoint(line)或sb(line)。在指定的行设置断点。
>> * setBreakpoint('fn()')或sb(...)。在函数体的第一个声明处设置断点。
>> * setBreakpoint('script.js', 1)或sb(...)。在脚本文件的第1行设置断点。
>> * clearBreakpoint或cb(...)。清除断点。

>> 进行调试时，还可以查看一些信息。这些信息指令如下所示。
>> * backtrace或bt。打印当前执行情况下的堆栈信息。
>> * list(5)。列出当前上下文前后5行的源代码。
>> * watch(expr)。添加表达式到观察列表，进行观察。
>> * unwatch(expr)。从观察列表中移除对表达式的观察。
>> * watchers。列出所有观察的表达式和值。
>> * repl。打开调试的交互，用于执行调试脚本的上下文。

>> Node Inspector工具是基于Debugger和Blink开发者工具创建的调试界面。

>> 调试只适合于开发阶段，并且由于过程略麻烦，不宜在开发中过于依赖。**更好的方式是编写良好的单元测试和做合理的日志记录**，这对于程序开发来说更轻量，信赖度也更高。

# 附录C Node编码规范

## C.1 根源

>> 在编码规范上，一个重要的人物是Douglas Crockford，他是JavaScript开发社区最知名的权威，是JSON、JSLint、JSMin和ADSafe之父，其中JSLint现在仍然是最重要的JavaScript质量检测工具。他出版的JavaScript:The Good Parts一书对于JavaScript社区影响深远。

>> Douglas Crockford的JSLint和JavaScript:The Good Parts对JavaScript的贡献在于，他让我们能够甄别语言中的精华和糟粕，写出更好的代码。

>> 利用JSLint能够解决大部分问题，但是随着Node的流行，带来了一些新的变化，这些需要引起我们注意。

## C.2 编码规范

### C.2.1 空格与格式

#### 1．缩进

>> 2个空格会让代码看起来更紧凑、明快。

#### 2．变量声明

>> 每行声明都应该带上`var`，而不是只有一个`var`

#### 7．分号

>> 尽管JavaScript编译器会自动给行尾添加分号，但还是会带来一些误解，示例如下：
>> ```JS
>> function add() {
>>     var a = 1, b = 2
>>     return
>>         a + b
>> }
>> ```
>>
>> 将会得到`undefined`的返回值。因为自动加入分号后会变成如下的样子：
>> ```JS
>> function add() {
>>     var a = 1, b = 2;
>>     return;
>>         a + b;
>> }
>> ```

>> 而如下的代码：
>> ```JS
>> x = y
>> (function () {
>> }())
>> ```
>>
>> 执行时会得到：
>> ```JS
>> x = y(function () {}())
>> ```

>> **自动添加分号可能带来未预期的结果，所以添加上分号有助于避免误会。**

### C.2.2 命名规范

#### 6．包名

>> 在包名中，尽量不要包含js或node的字样，它是重复的。

### C.2.4 字面量

>> 请尽量使用`{}`、`[]`代替`new Object()`、`new Array()`，不要使用`string`、`bool`、`number`对象类型，即不要调用`new String`、`new Boolean`和`new Number`。

### C.2.5 作用域

>> 在JavaScript中，需要注意一个关键字和一个方法，它们是`with`和`eval()`，容易引起作用域混乱。

>> 慎用`eval()`的原因与`with`相同。如果不影响作用域上已存在的变量，用它是安全的。

>> 在大多数情况下，基本上轮不到`eval()`来完成特殊使命

### C.2.7 异步

>> 异步回调函数的第一个参数应该是错误指示

>> 异步方法中一旦有回调函数传入，就一定要执行它，且不能多次执行。如果不执行，可能造成调用一直等待不结束，多次执行也可能会造成未期望的结果。

### C.2.8 类与模块

>> 关于如何在JavaScript中实现继承，有各种各样的方式，但在Node中我们只推荐一种，那就是类继承的方式。

>> 一般情况下，我们采用Node推荐的类继承方式，示例代码如下：
>> ```JS
>> function Socket(options) {
>>     // ...
>>     stream.Stream.call(this);
>>     // ...
>> }
>> util.inherits(Socket, stream.Stream);
>> ```

>> 一般情况下，我们会对每个方法编写注释，这里采用dox的推荐注释，示例如下：
>> ```JS
>> /**
>>  * Queries some records
>>  * Examples:
>>  * ```
>>  * query('SELECT * FROM table', function (err, data) {
>>  * // some code
>>  * });
>>  * ```
>>  * @param {String} sql Queries
>>  * @param {Function} callback Callback
>>  */
>> exports.query = function (sql, callback) {
>>     // ...
>> };
>> ```

## C.3 最佳实践

>> 无论SVN还是Git，都有precommit这样的**钩子脚本**，通过在提交时实现代码质量的检查。如果质量不达标，将停止提交。

>> 持续集成包含两个方面：一方面仍是代码质量的扫描，可以选择定时扫描，或是触发式扫描；另一方面可以通过集中的平台统计代码质量的好坏变化趋势。

# 附录D 搭建局域NPM仓库

>> NPM仓库的源代码托管在GitHub上，地址是：[http://github.com/isaacs/npmjs.org](http://github.com/isaacs/npmjs.org)。

>> NPM仓库的设计基于CouchDB实现。CouchDB是一款NoSQL数据库，基于文档设计，它的文档带有版本性质，同时暴露的HTTP RESTful接口十分好用，这与Node的模块具有较为相似的特性。Isaac Z. Schlueter正是在这个基础上考虑用它实现模块的托管。

>> NPM仓库主要由两部分组成，体现在源代码中分别是www和registry。www是NPM站点的界面，registry则是利用CouchDB存储模块包文件和提供JSON API，面向NPM站点和NPM命令行工具服务。

>> 由于在CouchDB中构建Web应用较为复杂，后来Isaac Z. Schlueter重新构建了一个新的NPM的Web应用，用来替代CouchDB提供的Web应用服务，让CouchDB做纯粹的数据托管并提供HTTP RESTful服务。这个新的NPM Web应用就是图D-1中的new www应用，其源代码在[https://github.com/isaacs/npm-www](https://github.com/isaacs/npm-www)中。


---

***`//End of Article`***

---


![公众号二维码](https://neptliang.github.io/img/Article/WeChatBlog.png)