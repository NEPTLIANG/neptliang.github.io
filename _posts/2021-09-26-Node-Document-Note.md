---
layout:       post
title:        "Node 文档机翻整理"
subtitle:     "undefined"
date:         2021-09-26 19:59:01
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - FrontEnd
---


# 入门

## Node.js 简介

Node.js 是一个开源、跨平台的 JavaScript 运行时环境。它是几乎任何类型的项目的流行工具！

Node.js 在浏览器之外运行 Google Chrome 的核心—— V8 JavaScript 引擎。这使得 Node.js 非常高效。

Node.js 应用程序在单个进程中运行，无需为每个请求创建新线程。Node.js 在其标准库中提供了一组异步 I/O 原语，可防止 JavaScript 代码阻塞，通常，Node.js 中的库是使用非阻塞范式
编写的，使阻塞行为成为例外而不是常态。

当 Node.js 执行 I/O 操作时——例如从网络读取、访问数据库或文件系统——Node.js 不会阻塞线程并浪费 CPU 周期等待，而是会在响应返回时恢复操作。

这允许 Node.js 处理与单个服务器的数千个并发连接，而​​不会引入管理线程并发的负担，这可能是错误的重要来源。

Node.js 有一个独特的优势，因为数百万为浏览器编写 JavaScript 的前端开发人员现在能够编写除了客户端代码之外的服务器端代码，而无需学习完全不同的语言。

在 Node.js 中，可以毫无问题地使用新的 ECMAScript 标准，因为您不必等待所有用户更新他们的浏览器 - 您可以通过更改 Node.js 版本来决定使用哪个 ECMAScript 版本，您还可以通过运行带有标志的 Node.js 来启用特定的实验功能。

### 大量的库

npm 以其简单的结构帮助 Node.js 的生态系统蓬勃发展，现在 npm 注册表托管了超过 1,000,000 个开源包，您可以自由使用。

### 一个示例 Node.js 应用程序

Node.js 最常见的 Hello World 示例是 Web 服务器：

```js
const http = require('http')

const hostname = '127.0.0.1'
const port = 3000

const server = http.createServer((req, res) => {
  res.statusCode = 200
  res.setHeader('Content-Type', 'text/plain')
  res.end('Hello World\n')
})

server.listen(port, hostname, () => {
  console.log(`Server running at http://${hostname}:${port}/`)
})
```

这段代码首先导入了 Node.js 的 [`http` 模块](https://nodejs.org/api/http.html)。

Node.js 有一个很棒的[标准库](https://nodejs.org/api/)，包括一流的网络支持。

`http`的`createServer()`方法创建一个新的 HTTP 服务器并返回它。

服务器设置为侦听指定的端口和主机名。当服务器准备好时，回调函数被调用，在这种情况下通知我们服务器正在运行。

每当接收到新请求时，[`request`事件](https://nodejs.org/api/http.html#http_event_request)都会被调用，提供两个对象：请求（[`http.IncomingMessage`](https://nodejs.org/api/http.html#http_class_http_incomingmessage)对象）和响应（[`http.ServerResponse`](https://nodejs.org/api/http.html#http_class_http_serverresponse)对象）。

这两个对象对于处理 HTTP 调用至关重要。

第一个提供请求详细信息。在这个简单的示例中，没有使用它，但您可以访问请求头和请求数据。

第二个用于将数据返回给调用者。

在这种情况下：

```JS
res.statusCode = 200;
```

我们将 statusCode 属性设置为 200，表示一个成功的响应。

我们设置 Content-Type 标头：

```JS
res.setHeader('Content-Type', 'text/plain');
```

然后我们关闭响应，将响应内容作为参数添加到`end()`：

```JS
res.end('Hello World\n');
```

### Node.js 框架和工具

Node.js 是一个底层平台。为了使开发人员的工作变得简单且令人兴奋，社区在 Node.js 上构建了数千个库。

随着时间的推移，其中许多被确立为受欢迎的选择。以下是值得学习的不全面列表：（编者注：下方列表保留机翻结果）

* AdonisJS：一个基于 TypeScript 的全功能框架，高度关注开发人员的人体工程学、稳定性和信心。Adonis 是最快的 Node.js Web 框架之一。

* Egg.js：一个使用 Node.js 和 Koa 构建更好的企业框架和应用程序的框架。

* Express：它提供了一种创建 Web 服务器的最简单但功能强大的方法。其极简主义的方法，没有意见，专注于服务器的核心功能，是其成功的关键。

* Fastify：一个高度专注于以最少的开销和强大的插件架构提供最佳开发人员体验的 Web 框架。Fastify 是最快的 Node.js Web 框架之一。

* FeatherJS：Feathers 是一个轻量级的 Web 框架，用于使用 JavaScript 或 TypeScript 创建实时应用程序和 REST API。在几分钟内构建原型，在几天内构建生产就绪的应用程序。

* Gatsby：基于React、 GraphQL支持的静态站点生成器，具有非常丰富的插件和启动器生态系统。

* hapi：用于构建应用程序和服务的丰富框架，使开发人员能够专注于编写可重用的应用程序逻辑，而不是花时间构建基础设施。

* koa：它由 Express 背后的同一团队构建，旨在更简单、更小，建立在多年的知识之上。新项目的诞生是为了在不破坏现有社区的情况下创建不兼容的更改。

* Loopback.io：使构建需要复杂集成的现代应用程序变得容易。

* Meteor：一个非常强大的全栈框架，为您提供同构的方法来使用 JavaScript 构建应用程序，在客户端和服务器上共享代码。曾经是提供一切的现成工具，现在与前端库React、 Vue和Angular集成。也可用于创建移动应用程序。

* Micro：它提供了一个非常轻量级的服务器来创建异步 HTTP 微服务。

* NestJS：一个基于 TypeScript 的渐进式 Node.js 框架，用于构建企业级高效、可靠和可扩展的服务器端应用程序。

* Next.js： React框架，可为您提供最佳的开发人员体验，并提供生产所需的所有功能：混合静态和服务器渲染、TypeScript 支持、智能捆绑、路由预取等。

* Nx：使用 NestJS、Express、 React、 Angular等进行全栈 monorepo 开发的工具包！Nx 有助于将您的开发从一个团队构建一个应用程序扩展到多个团队协作开发多个应用程序！

* Remix：Remix 是一个全栈 Web 框架，用于为 Web 构建出色的用户体验。它开箱即用，包含构建现代 Web 应用程序（前端和后端）并将它们部署到任何基于 JavaScript 的运行时环境（包括 Node.js）所需的一切。

* Sapper：Sapper 是一个用于构建各种规模的 Web 应用程序的框架，具有出色的开发体验和灵活的基于文件系统的路由。提供 SSR 等等！

* Socket.io：构建网络应用程序的实时通信引擎。

* Strapi：Strapi 是一种灵活的开源无头 CMS，它让开发人员可以自由选择自己喜欢的工具和框架，同时还允许编辑人员轻松管理和分发他们的内容。通过插件系统使管理面板和 API 可扩展，Strapi 使世界上最大的公司能够加速内容交付，同时构建美妙的数字体验。


# 异步工作

## 异步流控制

这篇文章中的材料深受 [Mixu 的 Node.js Book](http://book.mixu.net/node/ch7.html) 的启发。

在其核心，JavaScript 被设计为在“主”线程上是非阻塞的，这是渲染视图的地方。你可以想象这在浏览器中的重要性。当主线程被阻塞时，它会导致终端用户害怕的臭名昭著的“冻结”，并且无法调度其他事件，从而导致数据采集丢失。

这会产生一些独特的约束，只有函数式编程才能解决这些约束。这就是回调出现的地方。

但是，在更复杂的过程中处理回调可能会变得具有挑战性。这通常会导致“回调地狱”，其中带有回调的多重嵌套函数会使代码在阅读、调试、组织等方面更具挑战性。

```JS
async1(function (input, result1) {
  async2(function (result2) {
    async3(function (result3) {
      async4(function (result4) {
        async5(function (output) {
          // do something with output
        });
      });
    });
  });
});
```

当然，在现实生活中很可能会有额外的代码行来处理 `result1`、`result2` 等，因此，这个问题的长度和复杂性通常会导致代码看起来比上面的示例更加混乱。

这就是*函数*派上用场的地方。更复杂的操作由许多函数组成：

* 发起人风格/输入
* 中间件
* 终结者

“发起人风格/输入”是序列中的第一个函数。此函数将接受操作的原始输入（如果有的话）。该操作是一系列可执行的函数，原始输入主要是：

1. 全局环境中的变量
2. 带或不带参数的直接调用
3. 通过文件系统或网络请求获得的值

网络请求可以是由外部网络发起、由同一网络上的另一个应用程序发起、或由同一网络或外部网络上的应用程序本身发起的传入请求。

中间件函数将返回另一个函数，终止函数将调用回调。下面说明了网络或文件系统请求的流程。这里延迟为 0，因为所有这些值都在内存中可用。

```JS
function final(someInput, callback) {
  callback(`${someInput} and terminated by executing callback `);
}

function middleware(someInput, callback) {
  return final(`${someInput} touched by middleware `, callback);
}

function initiate() {
  const someInput = 'hello this is a function ';
  middleware(someInput, function (result) {
    console.log(result);
    // requires callback to `return` result
  });
}

initiate();
```

### 状态管理

函数可能会也可能不会依赖于状态。当函数的输入或其他变量依赖于外部函数时，就会出现状态依赖。

这样，状态管理有两种主要策略：

1. 将变量直接传递给函数，以及
2. 从缓存、会话、文件、数据库、网络或其他外部来源获取变量值。

注意，我没有提到全局变量。使用全局变量管理状态通常是一种草率的反模式，这使得保障状态变得困难或不可能。应尽可能避免复杂程序中的全局变量。

### 控制流

如果一个对象在内存中可用，则可以进行迭代，并且控制流不会发生变化：

```JS
function getSong() {
  let _song = '';
  let i = 100;
  for (i; i > 0; i -= 1) {
    _song += `${i} beers on the wall, you take one down and pass it around, ${
      i - 1
    } bottles of beer on the wall\n`;
    if (i === 1) {
      _song += "Hey let's get some more beer";
    }
  }

  return _song;
}

function singSong(_song) {
  if (!_song) throw new Error("song is '' empty, FEED ME A SONG!");
  console.log(_song);
}

const song = getSong();
// this will work
singSong(song);
```

但是，如果数据存在于内存之外，则迭代将不再起作用：

```JS
function getSong() {
  let _song = '';
  let i = 100;
  for (i; i > 0; i -= 1) {
    /* eslint-disable no-loop-func */
    setTimeout(function () {
      _song += `${i} beers on the wall, you take one down and pass it around, ${
        i - 1
      } bottles of beer on the wall\n`;
      if (i === 1) {
        _song += "Hey let's get some more beer";
      }
    }, 0);
    /* eslint-enable no-loop-func */
  }

  return _song;
}

function singSong(_song) {
  if (!_song) throw new Error("song is '' empty, FEED ME A SONG!");
  console.log(_song);
}

const song = getSong('beer');
// this will not work
singSong(song);
// Uncaught Error: song is '' empty, FEED ME A SONG!
```

为什么会这样？`setTimeout`指示 CPU 将指令存储在总线上的其他位置，并指示数据被安排在稍后的时间提取。在该函数在 0 毫秒标记处再次命中之前经过数千个 CPU 周期，CPU 从总线获取指令并执行它们。唯一的问题是song（''）在数千个周期之前返回。

在处理文件系统和网络请求时也会出现同样的情况。主线程根本不能被阻塞一段不确定的时间——因此，我们使用回调以一种受控的方式及时调度代码的执行。

您将能够使用以下 3 种模式执行几乎所有操作：

1. *串联*：函数将按照严格的顺序执行，这与`for`循环最相似。

    ```JS
    // operations defined elsewhere and ready to execute
    const operations = [
      { func: function1, args: args1 },
      { func: function2, args: args2 },
      { func: function3, args: args3 },
    ];

    function executeFunctionWithArgs(operation, callback) {
      // executes function
      const { args, func } = operation;
      func(args, callback);
    }

    function serialProcedure(operation) {
      if (!operation) process.exit(0); // finished
      executeFunctionWithArgs(operation, function (result) {
        // continue AFTER callback
        serialProcedure(operations.shift());
      });
    }

    serialProcedure(operations.shift());
    ```

2. *完全并行*：当排序不是问题时，例如通过电子邮件发送 1,000,000 个电子邮件收件人的列表。

    ```JS
    let count = 0;
    let success = 0;
    const failed = [];
    const recipients = [
      { name: 'Bart', email: 'bart@tld' },
      { name: 'Marge', email: 'marge@tld' },
      { name: 'Homer', email: 'homer@tld' },
      { name: 'Lisa', email: 'lisa@tld' },
      { name: 'Maggie', email: 'maggie@tld' },
    ];

    function dispatch(recipient, callback) {
      // `sendEmail` is a hypothetical SMTP client
      sendMail(
        {
          subject: 'Dinner tonight',
          message: 'We have lots of cabbage on the plate. You coming?',
          smtp: recipient.email,
        },
        callback
      );
    }

    function final(result) {
      console.log(`Result: ${result.count} attempts \
          & ${result.success} succeeded emails`);
      if (result.failed.length)
        console.log(`Failed to send to: \
            \n${result.failed.join('\n')}\n`);
    }

    recipients.forEach(function (recipient) {
      dispatch(recipient, function (err) {
        if (!err) {
          success += 1;
        } else {
          failed.push(recipient.name);
        }
        count += 1;

        if (count === recipients.length) {
          final({
            count,
            success,
            failed,
          });
        }
      });
    });
    ```

3. *有限并行*：有限制并行，例如从 10E7 个用户列表中成功向 1,000,000 名收件人发送电子邮件。

    ```JS
    let successCount = 0;

    function final() {
      console.log(`dispatched ${successCount} emails`);
      console.log('finished');
    }

    function dispatch(recipient, callback) {
      // `sendEmail` is a hypothetical SMTP client
      sendMail(
        {
          subject: 'Dinner tonight',
          message: 'We have lots of cabbage on the plate. You coming?',
          smtp: recipient.email,
        },
        callback
      );
    }

    function sendOneMillionEmailsOnly() {
      getListOfTenMillionGreatEmails(function (err, bigList) {
        if (err) throw err;

        function serial(recipient) {
          if (!recipient || successCount >= 1000000) return final();
          dispatch(recipient, function (_err) {
            if (!_err) successCount += 1;
            serial(bigList.shift());
          });
        }

        serial(bigList.shift());
      });
    }

    sendOneMillionEmailsOnly();
    ```

每个都有自己的用例、好处和问题，您可以更详细地体验和阅读。最重要的是，记住模块化你的操作并使用回调！如果您有任何疑问，请将所有内容视为中间件！

## Node.Js 事件循环

### 介绍

**事件循环**是了解 Node.Js 的最重要方面之一。

为什么这个这么重要？因为它解释了 Node.Js 如何实现异步并且具有非阻塞 I/O，所以它基本上解释了 Node.Js 的“杀手级特性”，它是让它如此成功的东西。

Node.Js JavaScript 代码在单个线程上运行。一次只发生一件事。

这是一个实际上非常有用的限制，因为它大大简化了您的编程方式，而无需担心并发问题。

您只需要注意如何编写代码并避免任何可能阻塞线程的事情，例如同步网络调用或无限循环。

一般来说，在大多数浏览器中，每个浏览器选项卡都有一个事件循环，以使每个进程隔离，并避免一个具有无限循环或繁重处理的网页阻塞整个浏览器。

该环境管理多个并发事件循环，例如处理 API 调用。Web Worker 也在它们自己的事件循环中运行。

您主要需要关心*您的代码*将在单个事件循环上运行，并在编写代码时牢记这一点以避免阻塞它。

### 阻塞事件循环

任何需要很长时间才能将控制权返回给事件循环的 JavaScript 代码都会阻塞页面中任何 JavaScript 代码的执行，甚至会阻塞 UI 线程，并且用户无法四处点击、滚动页面等等。

JavaScript 中几乎所有的 I/O 原语都是非阻塞的。网络请求、文件系统操作等。阻塞是个例外，这就是为什么 JavaScript 如此依赖回调，以及最近基于 Promise 和 Async/Await 的原因。

### 调用栈

调用栈是 LIFO（后进先出）栈。

事件循环不断检查**调用栈**以查看是否有任何函数需要运行。

这样做时，它会添加在调用栈中找到的任何函数调用，并按顺序执行每个函数。

您知道调试器或浏览器控制台中您可能熟悉的错误栈跟踪吗？浏览器在调用栈中查找函数名称，以告知您哪个函数发起了当前调用：

[![Exception call stack](https://nodejs.dev/static/e4594b6135efd353b44770f748fdccd5/fcda8/exception-call-stack.png)](https://nodejs.dev/static/e4594b6135efd353b44770f748fdccd5/fcda8/exception-call-stack.png)

### 一个简单的事件循环解释

让我们举个例子：

```JS
const bar = () => console.log('bar')

const baz = () => console.log('baz')

const foo = () => {
  console.log('foo')
  bar()
  baz()
}

foo()
```

当此代码运行时，首先`Foo()`被调用。在`Foo()`里面我们先调用`Bar()`，然后调用`Baz()`。

此时调用栈如下所示：

[![Call stack first example](https://nodejs.dev/static/270ebeb6dbfa7d613152b71257c72a9e/fcda8/call-stack-first-example.png)](https://nodejs.dev/static/270ebeb6dbfa7d613152b71257c72a9e/fcda8/call-stack-first-example.png)

每次迭代的事件循环都会查看调用栈中是否有东西，并执行它：

[![Execution order first example](https://nodejs.dev/static/ca404c319c6fc595497d5dc097d469ff/fc1a1/execution-order-first-example.png)](https://nodejs.dev/static/ca404c319c6fc595497d5dc097d469ff/fc1a1/execution-order-first-example.png)

直到调用栈为空。

### 队列函数执行

上面的例子看起来很正常，并没有什么特别之处：JavaScript 找到要执行的东西，按顺序运行它们。

让我们看看如何推迟一个函数直到栈被清除。

`SetTimeout(() => {}, 0)`的用例是调用一个函数，但在代码中的每个其他函数执行后执行它。

举个例子：

```JS
const bar = () => console.log('bar')

const baz = () => console.log('baz')

const foo = () => {
  console.log('foo')
  setTimeout(bar, 0)
  baz()
}

foo()
```

这段代码打印出来，也许令人惊讶：

```BASH
foo
baz
bar
```

当此代码运行时，首先调用 Foo()。在 Foo() 内部，我们首先调用 SetTimeout，`Bar`作为参数被传递，我们指示它尽快运行，传递 0 作为定时器。然后我们调用 Baz()。

此时调用栈如下所示：

[![Call stack second example](https://nodejs.dev/static/be55515b9343074d00b43de88c495331/966a0/call-stack-second-example.png)](https://nodejs.dev/static/be55515b9343074d00b43de88c495331/966a0/call-stack-second-example.png)

以下是我们程序中所有函数的执行顺序：

[![Execution order second example](https://nodejs.dev/static/585ff3207d814911a7e44d55fbde483b/f96db/execution-order-second-example.png)](https://nodejs.dev/static/585ff3207d814911a7e44d55fbde483b/f96db/execution-order-second-example.png)

为什么会这样？

### 消息队列

调用 SetTimeout() 时，浏览器或 Node.Js 会启动计时器。一旦计时器到期，在这种情况下，我们立即将 0 作为超时，回调函数被放入消息队列中。

消息队列也是用户发起的事件（如单击或键盘事件）或获取响应在您的代码有机会对其做出反应之前排队的地方。或者还有 DOM 事件，例如Onload.

循环优先考虑调用堆栈，它首先处理它在调用堆栈中找到的所有内容，一旦那里没有任何内容，它就会去获取消息队列中的内容。

我们不必等待诸如SetTimeout、 Fetch 或其他功能来完成自己的工作，因为它们是由浏览器提供的，并且它们存在于自己的线程上。例如，如果将SetTimeout超时设置为 2 秒，则不必等待 2 秒 - 等待发生在其他地方。

ES6 作业队列
ECMAScript 2015 引入了作业队列的概念，Promises 使用了它（在 ES6/ES2015 中也引入了）。这是一种尽快执行异步函数结果的方法，而不是放在调用堆栈的末尾。

在当前函数结束之前解析的 Promise 将在当前函数之后立即执行。

类似于游乐园的过山车：消息队列将您排在队列的最后，排在所有其他人之后，您必须在那里等待轮到您，而工作队列是让您乘坐的快速通行证完成上一个后立即进行另一次骑行。

例子：


这是 Promises（以及基于 Promise 构建的 Async/Await）与通过SetTimeout()或其他平台 API 的普通旧异步函数之间的一个很大区别。

最后，这是上面示例的调用堆栈的样子：

调用栈第三个例子


# 操作文件

## 在 Node.js 中使用文件描述符

在您能够与位于文件系统中的文件进行交互之前，您必须获得一个文件描述符。

文件描述符是对打开的文件的引用，是使用`fs`模块提供的`open()`方法打开文件时返回的数字 (即fd)。此数字 (即`fd`) 唯一地标识操作系统中打开的文件：

```JS
const fs = require('fs');

fs.open('/Users/joe/test.txt', 'r', (err, fd) => {
  // fd is our file descriptor
});
```

注意我们用作`fs.open()`调用的第二个参数`r`。

该标志意味着我们为了读取而打开文件。

您通常使用的其他标志是：


* `r+` 打开文件进行读写，如果文件不存在，则不会创建。

* `w+` 打开文件进行读写，将流定位在文件的开头。如果文件不存在，则创建该文件。

* `a` 打开文件进行写入，将流定位在文件末尾。如果文件不存在，则创建该文件。

* `a+` 打开文件进行读写，将流定位在文件末尾。如果文件不存在，则创建该文件。

您还可以使用 `fs.openSync` 方法打开文件，该方法返回文件描述符，而不是在回调中提供它：

```JS
const fs = require('fs');

try {
  const fd = fs.openSync('/Users/joe/test.txt', 'r');
} catch (err) {
  console.error(err);
}
```

获得文件描述符后，无论选择何种方式，您都可以执行所有需要它的操作，例如调用`fs.close()`和与文件系统交互的许多其他操作。

您还可以使用`fs/promises`模块提供的基于 Promise 的`fsPromises.open`方法打开文件。

`fs/promises` 模块仅从 Node.js v14 开始可用。在 v14 之前、v10 之后，您可以使用 `require('fs').promises` 代替。在 v10 之前、v8 之后，您可以使用 `util.promisify` 将 `fs` 方法转换为基于 Promise 的方法。

```JS
const fs = require('fs/promises');
// Or const fs = require('fs').promises before v14.
async function example() {
  let filehandle;
  try {
    filehandle = await fs.open('/Users/joe/test.txt', 'r');
    console.log(filehandle.fd);
    console.log(await filehandle.readFile({ encoding: 'utf8' }));
  } finally {
    await filehandle.close();
  }
}
example();
```

下面是一个 `util.promisify` 的例子：

```JS
const fs = require('fs');
const util = require('util');

async function example() {
  const open = util.promisify(fs.open);
  const fd = await open('/Users/joe/test.txt', 'r');
}
example();
```

要查看有关 `fs/promises` 模块的更多详细信息，请查看 [fs/promises API](https://nodejs.org/docs/latest-v17.x/api/fs.html#promises-api)。

## Node.js 文件统计信息

每个文件都带有一套细节，我们可以使用 Node.js 进行检查。

特别是使用`fs`模块提供的`stat()`方法。

您传入文件路径调用它，一旦 Node.js 获取文件详细信息，它将调用您传递的回调函数，该函数带有 2 个参数——错误消息和文件统计信息：

```JS
const fs = require('fs');

fs.stat('/Users/joe/test.txt', (err, stats) => {
  if (err) {
    console.error(err);
  }
  // we have access to the file stats in `stats`
});
```

Node.js 还提供了一个同步方法，它会阻塞线程，直到文件统计信息准备好：

```JS
const fs = require('fs');

try {
  const stats = fs.statSync('/Users/joe/test.txt');
} catch (err) {
  console.error(err);
}
```

文件信息被包含在 stats 变量中。我们可以使用统计数据提取什么样的信息？

很多，包括：

* 文件是否为目录或文件，使用 `stats.isFile()` 和 `stats.isDirectory()`判断
* 文件是否为符号链接，使用`stats.isSymbolicLink()`判断
* 文件大小（以字节为单位），使用`stats.size`获取。

还有其他高级方法，但您将在日常开发中使用的大部分内容就是这些。

```JS
const fs = require('fs');

fs.stat('/Users/joe/test.txt', (err, stats) => {
  if (err) {
    console.error(err);
    return;
  }

  stats.isFile(); // true
  stats.isDirectory(); // false
  stats.isSymbolicLink(); // false
  stats.size; // 1024000 //= 1MB
});
```

如果您愿意，还可以使用`fs/promises`模块提供的基于 Promise 的`fsPromises.stat()`方法：

```JS
const fs = require('fs/promises');

async function example() {
  try {
    const stats = await fs.stat('/Users/joe/test.txt');
    stats.isFile(); // true
    stats.isDirectory(); // false
    stats.isSymbolicLink(); // false
    stats.size; // 1024000 //= 1MB
  } catch (err) {
    console.log(err);
  }
}
example();
```

## Node.js 文件路径

系统中的每个文件都有一个路径。

在 Linux 和 macOS 上，路径可能如下所示：

`/users/joe/file.txt`

而 Windows 计算机则不同，其结构如下：

`C:\users\joe\file.txt`

在应用程序中使用路径时需要注意，因为必须考虑到这种差异。

你在你的文件中包含这个模块，使用

```JS
const path = require('path');
```

你可以开始使用它的方法了。

### 从路径中获取信息

给定路径，您可以使用以下方法从中提取信息：

* `dirname`: 获取文件的父文件夹
* `basename`: 获取文件名部分
* `extname`: 获取文件扩展名

例子：

```JS
const notes = '/users/joe/notes.txt';

path.dirname(notes); // /users/joe
path.basename(notes); // notes.txt
path.extname(notes); // .txt
```

您可以通过指定 `basename` 的第二个参数来获取不带扩展名的文件名：

```JS
path.basename(notes, path.extname(notes)); // notes
```

### 使用paths

您可以使用 `path.join()` 连接路径的两个或多个部分：

```JS
const name = 'joe';
path.join('/', 'users', name, 'notes.txt'); // '/users/joe/notes.txt'
```

您可以使用`path.resolve()`获得相对路径的绝对路径计算：

```JS
path.resolve('joe.txt'); // '/Users/joe/joe.txt' if run from my home folder
```

在这种情况下，Node.js 将简单地追加`/joe.txt`到当前工作目录。如果您指定第二个参数文件夹，`resolve`将使用第一个作为第二个的基础：

```JS
path.resolve('tmp', 'joe.txt'); // '/Users/joe/tmp/joe.txt' if run from my home folder
```

如果第一个参数以斜杠开头，则表示它是绝对路径：

```JS
path.resolve('/etc', 'joe.txt'); // '/etc/joe.txt'
```

`path.normalize()`是另一个有用的函数，在包含相对说明符（如`.`或`..`或双斜杠）时，它将尝试计算实际路径：

```JS
path.normalize('/users/joe/..//test.txt'); // '/users/test.txt'
```

**resolve 和 normalize 都不会检查路径是否存在**。他们只是根据获得的信息计算出一条路径。

## 使用 Node.js 读取文件

在 Node.js 中读取文件的最简单方法是使用`fs.readfile()`方法，将文件路径、编码和将使用文件数据（和错误）调用的回调函数传递给它：

```JS
const fs = require('fs');

fs.readFile('/Users/joe/test.txt', 'utf8', (err, data) => {
  if (err) {
    console.error(err);
    return;
  }
  console.log(data);
});
```

或者，您可以使用同步版本`fs.readFileSync()`：

```JS
const fs = require('fs');

try {
  const data = fs.readFileSync('/Users/joe/test.txt', 'utf8');
  console.log(data);
} catch (err) {
  console.error(err);
}
```

您还可以使用`fs/promises`模块提供的基于 Promise 的`fsPromises.readFile()`方法：

```JS
const fs = require('fs/promises');

async function example() {
  try {
    const data = await fs.readFile('/Users/joe/test.txt', { encoding: 'utf8' });
    console.log(data);
  } catch (err) {
    console.log(err);
  }
}
example();
```

`fs.readFile()`、`fs.readFileSync()` 和 `fsPromises.readFile()` 这三个函数都会在返回数据之前读取内存中文件的全部内容。

这意味着大文件将对您的内存消耗和程序执行速度产生重大影响。

在这种情况下，更好的选择是使用流读取文件内容。

## 使用 Node.js 写入文件

在 Node.js 中写入文件的最简单方法是使用`fs.writeFile()`API。

例子：

```JS
const fs = require('fs');

const content = 'Some content!';

fs.writeFile('/Users/joe/test.txt', content, err => {
  if (err) {
    console.error(err);
  }
  // file written successfully
});
```

或者，您可以使用同步版本`fs.writeFileSync()`：

```JS
const fs = require('fs');

const content = 'Some content!';

try {
  fs.writeFileSync('/Users/joe/test.txt', content);
  // file written successfully
} catch (err) {
  console.error(err);
}
```

您还可以使用`fs/promises`模块提供的基于 Promise 的`fsPromises.writeFile()`方法：

```JS
const fs = require('fs/promises');

async function example() {
  try {
    const content = 'Some content!';
    await fs.writeFile('/Users/joe/test.txt', content);
  } catch (err) {
    console.log(err);
  }
}
example();
```

默认情况下，如果文件已经存在，此 API 将**替换文件的内容**。

您可以通过指定标志来修改默认值：

```JS
fs.writeFile('/Users/joe/test.txt', content, { flag: 'a+' }, err => {});
```

您可能会使用的标志是

* `r+` 打开文件进行读写
* `w+` 打开文件进行读写，将流定位在文件的开头。如果文件不存在，则创建该文件
* `a` 打开文件进行写入，将流定位在文件末尾。如果文件不存在，则创建该文件
* `a+` 打开文件进行读写，将流定位在文件末尾。如果文件不存在，则创建该文件

（您可以在[https://nodejs.org/api/fs.html#fs_file_system_flags](https://nodejs.org/api/fs.html#fs_file_system_flags)找到更多标志）

### 追加到文件

将内容追加到文件末尾的一种方便方法是`fs.appendFile()`（及其`fs.appendFileSync()`对应项）：

```JS
const content = 'Some content!';

fs.appendFile('file.log', content, err => {
  if (err) {
    console.error(err);
  }
  // done!
});
```

这是一个 `fsPromises.appendFile()` 示例：

```JS
const fs = require('fs/promises');

async function example() {
  try {
    const content = 'Some content!';
    await fs.appendFile('/Users/joe/test.txt', content);
  } catch (err) {
    console.log(err);
  }
}
example();
```

### 使用流

所有这些方法在将控制权返回给您的程序之前将全部内容写入文件（在异步版本中，这意味着执行回调）

在这种情况下，更好的选择是使用流写入文件内容。

## 在 Node.js 中处理文件夹

Node.js `fs` 核心模块提供了许多可用于处理文件夹的便捷方法。

### 检查文件夹是否存在

使用`fs.access()`（及其基于 promise 的 `fsPromises.access()` 对应项）检查文件夹是否存在，并且 Node.js 可以使用其权限访问它。

### 新建一个文件夹

使用 `fs.mkdir()`，或使用 `fs.mkdirSync()`，或使用 `fsPromises.mkdir()` 创建一个新文件夹。

```JS
const fs = require('fs');

const folderName = '/Users/joe/test';

try {
  if (!fs.existsSync(folderName)) {
    fs.mkdirSync(folderName);
  }
} catch (err) {
  console.error(err);
}
```

### 读取目录的内容

使用`fs.readdir()`，或使用`fs.readdirSync()`，或使用`fsPromises.readdir()`读取目录的内容。

这段代码读取文件夹的内容，包括文件和子文件夹，并返回它们的相对路径：

```JS
const fs = require('fs');

const folderPath = '/Users/joe';

fs.readdirSync(folderPath);
```

您可以获得完整路径：

```JS
fs.readdirSync(folderPath).map(fileName => {
  return path.join(folderPath, fileName);
});
```

您还可以过滤结果以仅返回文件，并排除文件夹：

```JS
const isFile = fileName => {
  return fs.lstatSync(fileName).isFile();
};

fs.readdirSync(folderPath)
  .map(fileName => {
    return path.join(folderPath, fileName);
  })
  .filter(isFile);
```

### 重命名文件夹

使用`fs.rename()`，或使用`fs.renameSync()`，或使用`fsPromises.rename()`重命名文件夹。第一个参数是当前路径，第二个是新路径：

```JS
const fs = require('fs');

fs.rename('/Users/joe', '/Users/roger', err => {
  if (err) {
    console.error(err);
  }
  // done
});
```

`fs.renameSync()`是同步版本：

```JS
const fs = require('fs');

try {
  fs.renameSync('/Users/joe', '/Users/roger');
} catch (err) {
  console.error(err);
}
```

`fsPromises.rename()`是基于promise的版本：

```JS
const fs = require('fs/promises');

async function example() {
  try {
    await fs.rename('/Users/joe', '/Users/roger');
  } catch (err) {
    console.log(err);
  }
}
example();
```

### 删除文件夹

使用 `fs.rmdir()`，或使用 `fs.rmdirSync()`，或使用 `fsPromises.rmdir()` 删除文件夹。

删除包含内容的文件夹可能比您需要的更复杂。您可以传递选项 `{ recursive: true }` 以递归删除内容。

```JS
const fs = require('fs');

fs.rmdir(dir, { recursive: true }, err => {
  if (err) {
    throw err;
  }

  console.log(`${dir} is deleted!`);
});
```

> **注意**：在 Node `v16.x` 中，回调 API `fs.rmdir` 的 `recursive` 选项**已弃用**，改为使用 `fs.rm` 删除包含内容的文件夹：

```JS
const fs = require('fs');

fs.rm(dir, { recursive: true, force: true }, err => {
  if (err) {
    throw err;
  }

  console.log(`${dir} is deleted!`);
});
```

或者您可以安装并使用非常流行且维护良好的 [`fs-extra`](https://www.npmjs.com/package/fs-extra) 模块。它是 `fs` 模块的直接替代品，在其之上提供了更多功能。

在这种情况下，`remove()` 方法就是您想要的。

使用以下命令安装它

```BASH
npm install fs-extra 
```

并像这样使用它：

```JS
const fs = require('fs-extra');

const folder = '/Users/joe';

fs.remove(folder, err => {
  console.error(err);
});
```

它也可以与 Promise 一起使用：

```JS
fs.remove(folder)
  .then(() => {
    // done
  })
  .catch(err => {
    console.error(err);
  });
```

或使用async/await：

```JS
async function removeFolder(folder) {
  try {
    await fs.remove(folder);
    // done
  } catch (err) {
    console.error(err);
  }
}

const folder = '/Users/joe';
removeFolder(folder);
```


# Node.js Web 服务器

## 构建 HTTP 服务器

这是一个示例 Hello World HTTP Web 服务器：

```JS
const http = require('http')

const port = process.env.PORT || 3000

const server = http.createServer((req, res) => {
  res.statusCode = 200
  res.setHeader('Content-Type', 'text/html')
  res.end('<h1>Hello, World!</h1>')
})

server.listen(port, () => {
  console.log(`Server running at port ${port}`)
})
```

让我们简要分析一下。我们包括[`http`模块](https://nodejs.org/api/http.html)。

我们使用该模块来创建一个 HTTP 服务器。

服务器设置为侦听指定端口`3000`。当服务器准备好时，`listen`回调函数被调用。

我们传递的回调函数将在每个请求进入时执行。每当收到新请求时，都会调用[`request`事件](https://nodejs.org/api/http.html#http_event_request)，提供两个对象：一个请求（一个 [`http.IncomingMessage`](https://nodejs.org/api/http.html#http_class_http_incomingmessage) 对象）和一个响应（一个 [`http.ServerResponse`](https://nodejs.org/api/http.html#http_class_http_serverresponse) 对象）。

`request`提供请求详细信息。通过它，我们访问请求头和请求数据。

`response`用于填充我们要返回给客户端的数据。

在这种情况下

```JS
res.statusCode = 200;
```

我们将 statusCode 属性设置为 200，表示响应成功。

我们还设置了 Content-Type 头：

```JS
res.setHeader('Content-Type', 'text/html');
```

然后我们结束关闭响应，将内容作为参数添加到 `end()`：

```JS
res.end('<h1>Hello, World!</h1>');
```

## 使用 Node.js 发出 HTTP 请求

### 执行 GET 请求

在 Node.js 中执行 HTTP GET 请求的方法有很多种，具体取决于您要使用的抽象级别。

使用 Node.js 执行 HTTP 请求的最简单方法是使用 [Axios 库](https://github.com/axios/axios)：

```JS
const axios = require('axios');

axios
  .get('https://example.com/todos')
  .then(res => {
    console.log(`statusCode: ${res.status}`);
    console.log(res);
  })
  .catch(error => {
    console.error(error);
  });
```

但是，Axios 需要使用第三方库。

仅使用 Node.js 标准模块就可以进行 GET 请求，尽管它比上面的选项更冗长：

```JS
const https = require('https');

const options = {
  hostname: 'example.com',
  port: 443,
  path: '/todos',
  method: 'GET',
};

const req = https.request(options, res => {
  console.log(`statusCode: ${res.statusCode}`);

  res.on('data', d => {
    process.stdout.write(d);
  });
});

req.on('error', error => {
  console.error(error);
});

req.end();
```

### 执行 POST 请求

与发出 HTTP GET 请求类似，您可以使用 [Axios 库](https://github.com/axios/axios)执行 POST 请求：

```JS
const axios = require('axios');

axios
  .post('https://whatever.com/todos', {
    todo: 'Buy the milk',
  })
  .then(res => {
    console.log(`statusCode: ${res.status}`);
    console.log(res);
  })
  .catch(error => {
    console.error(error);
  });
```

或者，使用 Node.js 标准模块：

```JS
const https = require('https');

const data = JSON.stringify({
  todo: 'Buy the milk',
});

const options = {
  hostname: 'whatever.com',
  port: 443,
  path: '/todos',
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'Content-Length': data.length,
  },
};

const req = https.request(options, res => {
  console.log(`statusCode: ${res.statusCode}`);

  res.on('data', d => {
    process.stdout.write(d);
  });
});

req.on('error', error => {
  console.error(error);
});

req.write(data);
req.end();
```

### PUT和DELETE

PUT 和 DELETE 请求使用相同的 POST 请求格式——您只需将`options.method`值更改为适当的方法。

## 使用 Node.js 获取 HTTP 请求体数据

以下是如何提取请求体中作为 JSON 发送的数据的方法。

如果您使用 Express，那非常简单：使用 Express v4.16.0 及更高版本中可用的 `express.json()` 中间件。

例如，要获取此请求的请求体：

```JS
const axios = require('axios');

axios.post('https://whatever.com/todos', {
  todo: 'Buy the milk',
});
```

这是匹配的服务器端代码：

```JS
const express = require('express');

const app = express();

app.use(
  express.urlencoded({
    extended: true,
  })
);

app.use(express.json());

app.post('/todos', (req, res) => {
  console.log(req.body.todo);
});
```

如果您不使用 Express，并且想在普通 Node.js 中执行此操作，那么您当然需要做更多的工作，因为 Express 为您抽象了很多这些内容。

要理解的关键是，当您使用 `http.createServer()` 初始化 HTTP 服务器时，当服务器获取所有 HTTP 头而不是请求体时会调用回调。

连接回调中传入的`request`对象是一个流。

所以，我们必须监听要处理的请求体内容，并且是分块处理的。

我们首先通过监听流的`data`事件来获取数据，当数据结束时，流的`end`事件会被调用，一次：

```JS
const server = http.createServer((req, res) => {
  // we can access HTTP headers
  req.on('data', chunk => {
    console.log(`Data chunk available: ${chunk}`);
  });
  req.on('end', () => {
    // end of data
  });
});
```

所以要访问数据，假设我们期望接收一个字符串，我们必须在监听流`data`时将块连接成一个字符串，当流`end`时，我们将字符串解析为 JSON：

```JS
const server = http.createServer((req, res) => {
  let data = '';
  req.on('data', chunk => {
    data += chunk;
  });
  req.on('end', () => {
    console.log(JSON.parse(data).todo); // 'Buy the milk'
    res.end();
  });
});
```

从 Node.js v10 开始，一种 `for await .. of` 语法可供使用。它简化了上面的例子，使它看起来更线性：

```JS
const server = http.createServer(async (req, res) => {
  const buffers = [];

  for await (const chunk of req) {
    buffers.push(chunk);
  }

  const data = Buffer.concat(buffers).toString();

  console.log(JSON.parse(data).todo); // 'Buy the milk'
  res.end();
});
```


# Node.js 模块

## Node.js fs 模块

`fs` 模块提供了许多非常有用的功能来访问文件系统并与之交互。

无需安装它。作为 Node.js 核心的一部分，只需require它即可使用它：

```JS
const fs = require('fs');
```

一旦你这样做，你就可以访问它的所有方法，其中包括：

* `fs.access()`: 检查文件是否存在并且 Node.js 可以使用它的权限访问它
* `fs.appendFile()`: 将数据追加到文件中。如果文件不存在，则创建
* `fs.chmod()`: 更改由传递的文件名指定的文件的权限。相关的有：`fs.lchmod()`、`fs.fchmod()`
* `fs.chown()`: 更改由传递的文件名指定的文件的所有者和组。相关的有：`fs.fchown()`、`fs.lchown()`
* `fs.close()`: 关闭一个文件描述符
* `fs.copyFile()`: 复制一个文件
* `fs.createReadStream()`: 创建一个可读的文件流
* `fs.createWriteStream()`: 创建一个可写的文件流
* `fs.link()`: 创建指向一个文件的一个新的硬链接
* `fs.mkdir()`:  新建一个文件夹
* `fs.mkdtemp()`: 创建一个临时目录
* `fs.open()`: 打开文件并返回文件描述符以允许文件操作
* `fs.readdir()`: 读取目录的内容
* `fs.readFile()`: 读取文件的内容。有关的有：`fs.read()`
* `fs.readlink()`: 读取符号链接的值
* `fs.realpath()`: 将相对文件路径指针 (`.`、`..`) 解析为完整路径
* `fs.rename()`: 重命名文件或文件夹
* `fs.rmdir()`: 删除文件夹
* `fs.stat()`: 返回由传递的文件名标识的文件的状态。相关的有：`fs.fstat()`、`fs.lstat()`
* `fs.symlink()`: 创建指向一个文件的一个新的符号链接
* `fs.truncate()`: 将传递的文件名标识的文件截断到指定长度。有关的有：`fs.ftruncate()`
* `fs.unlink()`: 删除文件或符号链接
* `fs.unwatchFile()`: 停止监视文件的变化
* `fs.utimes()`: 更改由传递的文件名标识的文件的时间戳。有关的有：`fs.futimes()`
* `fs.watchFile()`: 开始观察文件的变化。有关的有：`fs.watch()`
* `fs.writeFile()`: 将数据写入文件。有关的有：`fs.write()`

`fs` 模块的一个特殊之处在于所有方法默认情况下都是异步的，但它们也可以通过附加 `Sync` 来同步工作。

例如：

* `fs.rename()`
* `fs.renameSync()`
* `fs.write()`
* `fs.writeSync()`

这会对您的应用程序流程产生巨大影响。

> Node.js 10 包括对基于 Promise 的 API 的[实验性支持](https://nodejs.org/api/fs.html#fs_fs_promises_api)

例如，让我们检验一下`fs.rename()`方法。异步 API 以回调使用：

```JS
const fs = require('fs');

fs.rename('before.json', 'after.json', err => {
  if (err) {
    return console.error(err);
  }

  // done
});
```

可以像这样使用同步 API，使用 try/catch 块来处理错误：

```JS
const fs = require('fs');

try {
  fs.renameSync('before.json', 'after.json');
  // done
} catch (err) {
  console.error(err);
}
```

这里的关键区别在于，在第二个示例中，脚本的执行将阻塞，直到文件操作成功。

您可以使用`fs/promises`模块提供的基于promise的API来避免使用基于回调的API，这可能会导致[回调地狱](http://callbackhell.com/)。这是一个例子：

```JS
// Example: Read a file and change its content and read
// it again using callback-based API.
const fs = require('fs');

const fileName = '/Users/joe/test.txt';
fs.readFile(fileName, 'utf8', (err, data) => {
  if (err) {
    console.log(err);
    return;
  }
  console.log(data);
  const content = 'Some content!';
  fs.writeFile(fileName, content, err2 => {
    if (err2) {
      console.log(err2);
      return;
    }
    console.log('Wrote some content!');
    fs.readFile(fileName, 'utf8', (err3, data3) => {
      if (err3) {
        console.log(err3);
        return;
      }
      console.log(data3);
    });
  });
});
```

当嵌套回调太多时，基于回调的 API 可能会引发回调地狱。我们可以简单地使用基于 Promise 的 API 来避免它：

```JS
// Example: Read a file and change its content and read
// it again using promise-based API.
const fs = require('fs/promises');

async function example() {
  const fileName = '/Users/joe/test.txt';
  try {
    const data = await fs.readFile(fileName, 'utf8');
    console.log(data);
    const content = 'Some content!';
    await fs.writeFile(fileName, content);
    console.log('Wrote some content!');
    const newData = await fs.readFile(fileName, 'utf8');
    console.log(newData);
  } catch (err) {
    console.log(err);
  }
}
example();
```

## Node.js path 模块

`path` 模块提供了许多非常有用的功能来访问文件系统并与之交互。

无需安装它。作为 Node.js 核心的一部分，只需require它即可使用它：

```JS
const path = require('path');
```

该模块提供了`path.sep`来提供路径段分隔符（Windows 上的 `\` 和 Linux / macOS 上的 `/`），以及`path.delimiter`提供路径分隔符（Windows 上的 `;` 和 Linux / macOS 上的 `:`）。

这些是`path`方法：

### `path.basename()`

返回路径的最后一部分。第二个参数可以过滤掉文件扩展名：

```JS
require('path').basename('/test/something'); // something
require('path').basename('/test/something.txt'); // something.txt
require('path').basename('/test/something.txt', '.txt'); // something
```

### `path.dirname()`

返回路径的目录部分：

```JS
require('path').dirname('/test/something'); // /test
require('path').dirname('/test/something/file.txt'); // /test/something
```

### `path.extname()`

返回路径的扩展名部分

```JS
require('path').extname('/test/something'); // ''
require('path').extname('/test/something/file.txt'); // '.txt'
```

### `path.format()`

从对象返回路径字符串，这与 `path.parse` 相反

`path.format` 接受具有以下键的对象作为参数：

* `root`:  根目录
* `dir`: 从根目录开始的文件夹路径
* `base`: 文件名+扩展名
* `name`: 文件名
* `ext`: 文件扩展名

如果提供了 `dir`，则忽略 `root`

如果 `base` 存在，则忽略 `ext` 和 `name`

```JS
// POSIX
require('path').format({ dir: '/Users/joe', base: 'test.txt' }); //  '/Users/joe/test.txt'

require('path').format({ root: '/Users/joe', name: 'test', ext: '.txt' }); //  '/Users/joe/test.txt'

// WINDOWS
require('path').format({ dir: 'C:\\Users\\joe', base: 'test.txt' }); //  'C:\\Users\\joe\\test.txt'
```

### `path.isAbsolute()`

如果是绝对路径，则返回 true

```JS
require('path').isAbsolute('/test/something'); // true
require('path').isAbsolute('./test/something'); // false
```

### `path.join()`

连接路径的两个或多个部分：

```JS
const name = 'joe';
require('path').join('/', 'users', name, 'notes.txt'); // '/users/joe/notes.txt'
```

### `path.normalize()`

当它包含相对说明符时尝试计算实际路径，如 `.` 或 `..`，或双斜杠：

```JS
require('path').normalize('/users/joe/..//test.txt'); // '/users/test.txt'
```

### `path.parse()`

把路径解析成一个由以下段组成的对象：

* `root`: 根目录
* `dir`: 从根目录开始的文件夹路径
* `base`: 文件名+扩展名
* `name`: 文件名
* `ext`: 文件扩展名

例子：

```JS
require('path').parse('/users/test.txt');
```

结果是

```JS
{
  root: '/',
  dir: '/users',
  base: 'test.txt',
  ext: '.txt',
  name: 'test'
}
```

### `path.relative()`

接受 2 个路径作为参数。根据当前工作目录返回从第一个路径到第二个路径的相对路径。

例子：

```JS
require('path').relative('/Users/joe', '/Users/joe/test.txt'); // 'test.txt'
require('path').relative('/Users/joe', '/Users/joe/something/test.txt'); // 'something/test.txt'
```

### `path.resolve()`

您可以使用 `path.resolve()` 获得相对路径的绝对路径计算：

```JS
require('path').resolve('joe.txt'); // '/Users/joe/joe.txt' if run from my home folder
```

通过指定第二个参数，`resolve`将使用第一个作为第二个的基础：

```JS
require('path').resolve('tmp', 'joe.txt'); // '/Users/joe/tmp/joe.txt' if run from my home folder
```

如果第一个参数以斜杠开头，则表示它是绝对路径：

```JS
require('path').resolve('/etc', 'joe.txt'); // '/etc/joe.txt'
```

## Node.js os 模块

该模块提供了许多功能，您可以使用这些功能从底层操作系统和程序运行的计算机中检索信息，并与之交互。

```JS
const os = require('os');
```

有一些有用的属性可以告诉我们一些与处理文件相关的关键信息：

`os.EOL` 给出了行分隔符序列。它在 Linux 和 macOS 上为 `\n`，在 Windows 上为 `\r\n`。

`os.constants.signals`告诉我们所有与处理进程信号相关的常量，如 SIGHUP、SIGKILL 等。

`os.constants.errno`设置错误报告的常量，如 EADDRINUSE、EOVERFLOW 等。

您可以在[https://nodejs.org/api/os.html#os_signal_constants](https://nodejs.org/api/os.html#os_signal_constants)上阅读所有内容。

现在让我们看看`os`提供的主要方法：

### `os.arch()`

返回标识底层架构的字符串，如`arm`、`x64`、`arm64`。

### `os.cpus()`

返回有关系统上可用 CPU 的信息。

例子：

```JS
[
  {
    model: 'Intel(R) Core(TM)2 Duo CPU     P8600  @ 2.40GHz',
    speed: 2400,
    times: {
      user: 281685380,
      nice: 0,
      sys: 187986530,
      idle: 685833750,
      irq: 0,
    },
  },
  {
    model: 'Intel(R) Core(TM)2 Duo CPU     P8600  @ 2.40GHz',
    speed: 2400,
    times: {
      user: 282348700,
      nice: 0,
      sys: 161800480,
      idle: 703509470,
      irq: 0,
    },
  },
];
```

### `os.endianness()`

返回 `BE` 或 `LE`，具体取决于 Node.js 是使用 [Big Endian 还是 Little Endian](https://zh.wikipedia.org/wiki/%E5%AD%97%E8%8A%82%E5%BA%8F) 编译的。

### `os.freemem()`

返回表示系统中可用内存的字节数。

### `os.homedir()`

返回当前用户主目录的路径。

例子：

```JS
'/Users/joe';
```

### `os.hostname()`

返回主机名。

### `os.loadavg()`

返回操作系统对平均负载的计算。

它只在 Linux 和 macOS 上返回一个有意义的值。

例子：

```JS
[3.68798828125, 4.00244140625, 11.1181640625];
```

### `os.networkInterfaces()`

返回系统上可用网络接口的详细信息。

例子：

```JS
{ lo0:
   [ { address: '127.0.0.1',
       netmask: '255.0.0.0',
       family: 'IPv4',
       mac: 'fe:82:00:00:00:00',
       internal: true },
     { address: '::1',
       netmask: 'ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff',
       family: 'IPv6',
       mac: 'fe:82:00:00:00:00',
       scopeid: 0,
       internal: true },
     { address: 'fe80::1',
       netmask: 'ffff:ffff:ffff:ffff::',
       family: 'IPv6',
       mac: 'fe:82:00:00:00:00',
       scopeid: 1,
       internal: true } ],
  en1:
   [ { address: 'fe82::9b:8282:d7e6:496e',
       netmask: 'ffff:ffff:ffff:ffff::',
       family: 'IPv6',
       mac: '06:00:00:02:0e:00',
       scopeid: 5,
       internal: false },
     { address: '192.168.1.38',
       netmask: '255.255.255.0',
       family: 'IPv4',
       mac: '06:00:00:02:0e:00',
       internal: false } ],
  utun0:
   [ { address: 'fe80::2513:72bc:f405:61d0',
       netmask: 'ffff:ffff:ffff:ffff::',
       family: 'IPv6',
       mac: 'fe:80:00:20:00:00',
       scopeid: 8,
       internal: false } ] }
```

### `os.platform()`

返回编译 Node.js 的平台：

* `darwin`
* `freebsd`
* `linux`
* `openbsd`
* `win32`
* ...更多

### `os.release()`

返回一个标识操作系统版本号的字符串

### `os.tmpdir()`

返回分配的临时文件夹的路径。

### `os.totalmem()`

返回表示系统中可用总内存的字节数。

### `os.type()`

识别操作系统：

* `Linux`
* `Darwin`——在 macOS 上
* `Windows_NT`——在 Windows 上

### `os.uptime()`

返回计算机自上次重新启动以来已运行的秒数。

### `os.userInfo()`

返回一个包含当前`username`、`uid`、`gid`、`shell` 和 `homedir` 的对象

## Node.js 事件模块

`events` 模块为我们提供了 EventEmitter 类，这是在 Node.js 中处理事件的关键。

```JS
const EventEmitter = require('events');

const door = new EventEmitter();
```

事件侦听器具有以下内置事件：

* `newListener`——添加监听器时
* `removeListener`——当监听器被移除时

以下是最有用的方法的详细说明：

### `emitter.addListener()`

`emitter.on()` 的别名。

### `emitter.emit()`

发出一个事件。它按照注册的顺序同步调用每个事件监听器。

```JS
door.emit('slam'); // emitting the event "slam"
```

### `emitter.eventNames()`

返回一个字符串数组，表示在当前`EventEmitter`对象上注册的事件：

```JS
door.eventNames();
```

### `emitter.getMaxListeners()`

获取可以添加到 `EventEmitter` 对象的最大侦听器数量，默认为 10，但可以使用 `setMaxListeners()` 增加或减少

```JS
door.getMaxListeners();
```

### `emitter.listenerCount()`

获取作为参数传递的事件的侦听器计数：

```JS
door.listenerCount('open');
```

### `emitter.listeners()`

获取作为参数传递的事件的侦听器数组：

```JS
door.listeners('open');
```

### `emitter.off()`

`emitter.removeListener()`在 Node.js 10中添加的别名

### `emitter.on()`

添加在发出事件时调用的回调函数。

用法：

```JS
door.on('open', () => {
  console.log('Door was opened');
});
```

### `emitter.once()`

添加一个回调函数，在注册后一个事件第一次发出时调用该函数。这个回调只会被调用一次，永远不会再被调用。

```JS
const EventEmitter = require('events');

const ee = new EventEmitter();

ee.once('my-event', () => {
  // call callback function once
});
```

### `emitter.prependListener()`

当您使用 `on` 或 `addListener` 添加侦听器时，它会在侦听器队列最后添加，并最后调用。使用 `prependListener` 在其他侦听器之前添加和调用它。

### `emitter.prependOnceListener()`

当您使用 `once` 添加侦听器时，它会在侦听器队列最后添加，并最后调用。使用 `prependOnceListener` 在其他侦听器之前添加和调用它。

### `emitter.removeAllListeners()`

移除 EventEmitter 对象的所有监听特定事件的监听器：

```JS
door.removeAllListeners('open');
```

### `emitter.removeListener()`

删除特定的侦听器。您可以在添加时将回调函数保存到变量中，以便稍后引用它：

```JS
const doSomething = () => {};
door.on('open', doSomething);
door.removeListener('open', doSomething);
```

### `emitter.setMaxListeners()`

设置可以添加到`EventEmitter`对象的最大侦听器数量，默认为 10，但可以增加或减少。

```JS
door.setMaxListeners(50);
```

## Node.js http 模块

HTTP 核心模块是 Node.js 网络的关键模块。

它可以使用以下方式包含进来

```JS
const http = require('http');
```

该模块提供了一些属性和方法，以及一些类。

### 属性

#### `http.METHODS`

此属性列出了所有支持的 HTTP 方法：

```JS
> require('http').METHODS
[ 'ACL',
  'BIND',
  'CHECKOUT',
  'CONNECT',
  'COPY',
  'DELETE',
  'GET',
  'HEAD',
  'LINK',
  'LOCK',
  'M-SEARCH',
  'MERGE',
  'MKACTIVITY',
  'MKCALENDAR',
  'MKCOL',
  'MOVE',
  'NOTIFY',
  'OPTIONS',
  'PATCH',
  'POST',
  'PROPFIND',
  'PROPPATCH',
  'PURGE',
  'PUT',
  'REBIND',
  'REPORT',
  'SEARCH',
  'SUBSCRIBE',
  'TRACE',
  'UNBIND',
  'UNLINK',
  'UNLOCK',
  'UNSUBSCRIBE' ]
```

#### `http.STATUS_CODES`

此属性列出所有 HTTP 状态代码及其描述：

```JS
> require('http').STATUS_CODES
{ '100': 'Continue',
  '101': 'Switching Protocols',
  '102': 'Processing',
  '200': 'OK',
  '201': 'Created',
  '202': 'Accepted',
  '203': 'Non-Authoritative Information',
  '204': 'No Content',
  '205': 'Reset Content',
  '206': 'Partial Content',
  '207': 'Multi-Status',
  '208': 'Already Reported',
  '226': 'IM Used',
  '300': 'Multiple Choices',
  '301': 'Moved Permanently',
  '302': 'Found',
  '303': 'See Other',
  '304': 'Not Modified',
  '305': 'Use Proxy',
  '307': 'Temporary Redirect',
  '308': 'Permanent Redirect',
  '400': 'Bad Request',
  '401': 'Unauthorized',
  '402': 'Payment Required',
  '403': 'Forbidden',
  '404': 'Not Found',
  '405': 'Method Not Allowed',
  '406': 'Not Acceptable',
  '407': 'Proxy Authentication Required',
  '408': 'Request Timeout',
  '409': 'Conflict',
  '410': 'Gone',
  '411': 'Length Required',
  '412': 'Precondition Failed',
  '413': 'Payload Too Large',
  '414': 'URI Too Long',
  '415': 'Unsupported Media Type',
  '416': 'Range Not Satisfiable',
  '417': 'Expectation Failed',
  '418': 'I\'m a teapot',
  '421': 'Misdirected Request',
  '422': 'Unprocessable Entity',
  '423': 'Locked',
  '424': 'Failed Dependency',
  '425': 'Unordered Collection',
  '426': 'Upgrade Required',
  '428': 'Precondition Required',
  '429': 'Too Many Requests',
  '431': 'Request Header Fields Too Large',
  '451': 'Unavailable For Legal Reasons',
  '500': 'Internal Server Error',
  '501': 'Not Implemented',
  '502': 'Bad Gateway',
  '503': 'Service Unavailable',
  '504': 'Gateway Timeout',
  '505': 'HTTP Version Not Supported',
  '506': 'Variant Also Negotiates',
  '507': 'Insufficient Storage',
  '508': 'Loop Detected',
  '509': 'Bandwidth Limit Exceeded',
  '510': 'Not Extended',
  '511': 'Network Authentication Required' }
```

#### `http.globalAgent`

指向代理对象的全局实例，它是`http.Agent`类的一个实例。

它用于管理 HTTP 客户端的连接持久性和重用，它是 Node.js HTTP 网络的关键组件。

稍后在`http.Agent`类描述中进行更多说明。

### 方法

#### `http.createServer()`

返回 `http.Server` 类的新实例。

用法：

```JS
const server = http.createServer((req, res) => {
  // handle every single request with this callback
});
```

#### `http.request()`

向服务器发出 HTTP 请求，创建`http.ClientRequest`类的实例。

#### `http.get()`

与`http.request()`类似，但自动将 HTTP 方法设置为 GET，并自动调用`req.end()`。

### 类

HTTP 模块提供了 5 个类：

* `http.Agent`
* `http.ClientRequest`
* `http.Server`
* `http.ServerResponse`
* `http.IncomingMessage`

#### `http.Agent`

Node.js 创建 `http.Agent` 类的全局实例来管理 HTTP 客户端的连接持久性和重用，这是 Node.js HTTP 网络的关键组件。

该对象确保向服务器发出的每个请求都排队并重用单个套接字。

它还维护一个套接字池。这是性能原因的关键。

#### `http.ClientRequest`

`http.ClientRequest`对象是在调用`http.request()`或调用`http.get()`时创建的。

接收到响应时，将使用响应调用`response`事件，并使用`http.IncomingMessage`实例作为参数。

可以通过两种方式读取响应的返回数据：

* 你可以调用`response.read()`方法
* 在`response`事件处理程序中，您可以为`data`事件设置事件侦听器，以便您可以侦听流入的数据。

#### `http.Server`

此类通常在使用`http.createServer()`创建新服务器时实例化并返回。

一旦你有了一个服务器对象，你就可以访问它的方法：

* `close()`阻止服务器接受新连接
* `listen()`启动 HTTP 服务器并监听连接

#### `http.ServerResponse`

由 `http.Server` 创建并作为第二个参数传递给它触发的`request`事件。

众所周知并在代码中用作 `res`：

```JS
const server = http.createServer((req, res) => {
  // res is an http.ServerResponse object
});
```

您将始终在handler中调用的方法是`end()`，它关闭响应，消息完成并且服务器可以将其发送给客户端。它必须在每个响应上被调用。

这些方法用于与 HTTP 头交互：

* `getHeaderNames()`获取已设置的 HTTP header名的列表
* `getHeaders()`获取已设置的 HTTP 头的副本
* `setHeader('headername', value)`设置一个 HTTP 头的值
* `getHeader('headername')`获取一个已设置的 HTTP 头
* `removeHeader('headername')`删除一个已设置的 HTTP 头
* `hasHeader('headername')`如果响应设置了该header，则返回 true
* `headersSent()`如果header已经发送到客户端，则返回 true

处理完header后，您可以通过调用`response.writeHead()`将它们发送到客户端，它接受 statusCode 作为第一个参数、可选的状态消息和 headers 对象。

要在响应体中向客户端发送数据，请使用 `write()`。它将缓冲的数据发送到 HTTP 响应流。

如果尚未使用`response.writeHead()`发送header，它将首先发送header，以及在请求中设置的状态码和message，您可以通过设置`statusCode`和`statusMessage`属性值来编辑它们：

```JS
response.statusCode = 500;
response.statusMessage = 'Internal Server Error';
```

#### `http.IncomingMessage`

`http.IncomingMessage`对象由以下方式创建：

* `http.Server`——监听`request`事件时
* `http.ClientRequest`——监听`response`事件时

它可用于访问响应的：

* 状态——使用其`statusCode`和`statusMessage`方法
* header——使用其`headers`方法或`rawHeaders`
* HTTP method——使用它的`method`方法
* HTTP版本——使用`httpVersion`方法
* URL——使用`url`方法
* 底层socket——使用`socket`方法

使用流访问数据，因为`http.IncomingMessage`实现了 Readable Stream 接口。

## Node.js 缓冲区

### 什么是缓冲区？

缓冲区是一块内存区域。与使用每天直接与内存交互的系统编程语言（如 C、C++ 或 Go）的开发者相比，大多数 JavaScript 开发人员不太熟悉这个概念。

它表示在 V8 JavaScript 引擎之外分配的固定大小的内存块（无法调整大小）。

你可以把缓冲区想象成一个整数数组，每个整数代表一个字节的数据。

它由 Node.js [Buffer 类](https://nodejs.org/api/buffer.html)实现。

### 为什么我们需要缓冲区？

引入缓冲区是为了帮助开发人员在传统上只处理字符串而不是二进制文件的生态系统中处理二进制数据。

Node.js 中的缓冲区与缓冲数据的概念无关。当一个流的处理器接收数据的速度超过其消化速度时，就会发生这种情况。

### 如何创建缓冲区

缓冲区是使用 [`Buffer.from()`](https://nodejs.org/api/buffer.html#buffer_buffer_from_buffer_alloc_and_buffer_allocunsafe)、[`Buffer.alloc()`](https://nodejs.org/api/buffer.html#buffer_class_method_buffer_alloc_size_fill_encoding) 和 [`Buffer.allocUnsafe()`](https://nodejs.org/api/buffer.html#buffer_class_method_buffer_allocunsafe_size) 方法创建的。

```JS
const buf = Buffer.from('Hey!');
```

* [`Buffer.from(array)`](https://nodejs.org/api/buffer.html#buffer_class_method_buffer_from_array)
* [`Buffer.from(arrayBuffer[, byteOffset[, length]])`](https://nodejs.org/api/buffer.html#buffer_class_method_buffer_from_arraybuffer_byteoffset_length)
* [`Buffer.from(buffer)`](https://nodejs.org/api/buffer.html#buffer_class_method_buffer_from_buffer)
* [`Buffer.from(string[, encoding])`](https://nodejs.org/api/buffer.html#buffer_class_method_buffer_from_string_encoding)

您也可以只传递大小初始化缓冲区。这将创建一个 1KB 的缓冲区：

```JS
const buf = Buffer.alloc(1024);
```

或者

```JS
const buf = Buffer.allocUnsafe(1024);
```

虽然 `alloc` 和 `allocUnsafe` 都分配了一个以字节为单位的指定大小的 `Buffer`，但由 `alloc` 创建的 `Buffer` 将被*初始化*为零。这意味着虽然`allocUnsafe`与`alloc`相比会相当快，但分配的内存段可能包含可能敏感的旧数据。

较旧的数据（如果存在于内存中）可以在读取 `Buffer` 内存时被访问或泄漏。这是使 `allocUnsafe` unsafe的真正原因，使用它时必须格外小心。

### 使用缓冲区

#### 访问一个缓冲区的内容

缓冲区是一个字节数组，可以像数组一样访问：

```JS
const buf = Buffer.from('Hey!');
console.log(buf[0]); // 72
console.log(buf[1]); // 101
console.log(buf[2]); // 121
```

这些数字是标识缓冲区中字符的 UTF-8 字节（`H`→`72`，`e`→`101`，`y`→`121`）。发生这种情况是因为`Buffer.from()`默认使用 UTF-8。请记住，某些字符可能会在缓冲区中占用超过一个字节（`é` → `195 169`）。

您可以使用 `toString()` 方法打印缓冲区的全部内容：

```JS
console.log(buf.toString());
```

`buf.toString()`默认情况下也使用 UTF-8。

> 请注意，如果您使用设置其大小的数字初始化缓冲区，您将可以访问包含随机数据的预初始化内存，而不是空缓冲区！

#### 获取一个缓冲区的长度

使用`length`属性：

```JS
const buf = Buffer.from('Hey!');
console.log(buf.length);
```

#### 遍历一个缓冲区的内容

```JS
const buf = Buffer.from('Hey!');
for (const item of buf) {
  console.log(item); // 72 101 121 33
}
```

#### 更改一个缓冲区的内容

您可以使用 `write()` 方法将整个数据字符串写入一个缓冲区：

```JS
const buf = Buffer.alloc(4);
buf.write('Hey!');
```

就像您可以使用数组语法访问缓冲区一样，您也可以以相同的方式设置缓冲区的内容：

```JS
const buf = Buffer.from('Hey!');
buf[1] = 111; // o in UTF-8
console.log(buf.toString()); // Hoy!
```

#### 切片缓冲区

如果要创建缓冲区的部分可视化，可以创建切片。切片不是副本：原始缓冲区仍然是事实的来源。如果它发生变化，您的切片就会发生变化。

使用`subarray()`方法来创建它。第一个参数是起始位置，您可以使用结束位置指定可选的第二个参数：

```JS
const buf = Buffer.from('Hey!');
buf.subarray(0).toString(); // Hey!
const slice = buf.subarray(0, 2);
console.log(slice.toString()); // He
buf[1] = 111; // o
console.log(slice.toString()); // Ho
```

#### 复制缓冲区

使用 `set()` 方法可以复制缓冲区：

```JS
const buf = Buffer.from('Hey!');
const bufcopy = Buffer.alloc(4); // allocate 4 bytes
bufcopy.set(buf);
```

默认情况下，您复制整个缓冲区。如果您只想复制缓冲区的一部分，则可以使用`.subarray()`和指定要写入的偏移量的`offset`参数：

```JS
const buf = Buffer.from('Hey?');
const bufcopy = Buffer.from('Moo!');
bufcopy.set(buf.subarray(1, 3), 1);
console.log(bufcopy.toString()); // 'Mey!'
```

## Node.js 流

### 什么是流

流是支持 Node.js 应用程序的基本概念之一。

它们是一种以有效方式处理读取/写入文件、网络通信或任何类型的端到端信息交换的方法。

流不是 Node.js 独有的概念。它们是几十年前在 Unix 操作系统中引入的，程序可以通过管道运算符（`|`）相互交互。

例如，在传统方式中，当您告诉程序读取文件时，文件会从头到尾读入内存，然后您对其进行处理。

使用流，您可以逐段读取它，处理其内容而不将其全部保存在内存中。

Node.js [`stream` 模块](https://nodejs.org/api/stream.html)提供了构建所有流 API 的基础。所有流都是[EventEmitter](https://nodejs.org/api/events.html#events_class_eventemitter)的实例

### 为什么选择流

与使用其他数据处理方法相比，流基本上提供了两个主要优势：

* **内存效率**：您无需在内存中加载大量数据就可以处理它
* **时间效率**：开始处理数据所需的时间更少，因为您可以在拥有数据后立即开始处理，而不是等到整个数据有效载荷可用

### 流的示例

一个典型的例子是从磁盘读取文件。

使用 Node.js `fs` 模块，您可以读取一个文件，并在与您的 HTTP 服务器建立新连接时通过 HTTP 提供它：

```JS
const http = require('http');
const fs = require('fs');

const server = http.createServer(function (req, res) {
  fs.readFile(`${__dirname}/data.txt`, (err, data) => {
    res.end(data);
  });
});
server.listen(3000);
```

`readFile()` 读取文件的全部内容，并在完成后调用回调函数。

回调中的 `res.end(data)` 会将文件内容返回给 HTTP 客户端。

如果文件很大，操作将花费相当多的时间。这是使用流编写的相同内容：

```JS
const http = require('http');
const fs = require('fs');

const server = http.createServer((req, res) => {
  const stream = fs.createReadStream(`${__dirname}/data.txt`);
  stream.pipe(res);
});
server.listen(3000);
```

我们不是等到文件被完全读取，而是在准备好要发送的数据块后立即开始将其流式传输到 HTTP 客户端。

### pipe()

上面的例子使用了 `stream.pipe(res)` 行：在文件流上调用 `pipe()` 方法。

这段代码有什么作用？它获取源，并将其通过管道传输到目标。

您在源流上调用它，因此在这种情况下，文件流通过管道传输到 HTTP 响应。

`pipe()` 方法的返回值是目标流，这是一件非常方便的事情，可以让我们链接多个 `pipe()` 调用，如下所示：

```JS
src.pipe(dest1).pipe(dest2);
```

这个构造和以下做的一样

```JS
src.pipe(dest1);
dest1.pipe(dest2);
```

### 流驱动的 Node.js API

由于它们的优势，许多 Node.js 核心模块都提供了原生的流处理能力，最值得注意的是：

* `process.stdin`返回连接到标准输入的流
* `process.stdout`返回连接到标准输出的流
* `process.stderr`返回连接到 stderr 的流
* `fs.createReadStream()`创建文件的可读流
* `fs.createWriteStream()`创建文件的可写流
* `net.connect()`启动基于流的连接
* `http.request()`返回 http.ClientRequest 类的一个实例，它是一个可写流
* `zlib.createGzip()`使用 gzip（一种压缩算法）将数据压缩成流
* `zlib.createGunzip()`解压缩 gzip 流。
* `zlib.createDeflate()`使用 deflate（一种压缩算法）将数据压缩成流
* `zlib.createInflate()`解压deflate流

### 不同类型的流

有四类流：

* `Readable`：可用于从中读取数据的流。换句话说，换句话说，它是`readonly`的。
* `Writable`：可用于向其写入数据的流。它是`writeonly`的。
* `Duplex`：可以读写数据的流，基本上是`Readable`和`Writable`流的组合。
* `Transform`：读取数据、转换数据，然后以所需格式写入转换后的数据的`Duplex`流。

### 如何创建可读流

我们从[`stream`模块](https://nodejs.org/api/stream.html)中获取Readable流，对其进行初始化并实现 `readable._read()` 方法。

首先创建一个流对象：

```JS
const Stream = require('stream');

const readableStream = new Stream.Readable();
```

然后实现`_read`：

```JS
readableStream._read = () => {};
```

您还可以使用 `read` 选项实现 `_read`：

```JS
const readableStream = new Stream.Readable({
  read() {},
});
```

现在流已经初始化，我们可以向它发送数据：

```JS
readableStream.push('hi!');
readableStream.push('ho!');
```

### 如何创建可写流

为了创建一个可写流，我们扩展基本的 `Writable` 对象，并实现它的 `_write()` 方法

首先创建一个流对象：

```JS
const Stream = require('stream');

const writableStream = new Stream.Writable();
```

然后实现`_write`：

```JS
writableStream._write = (chunk, encoding, next) => {
  console.log(chunk.toString());
  next();
};
```

现在可以通过管道传输可读流：

```JS
process.stdin.pipe(writableStream);
```

### 如何从可读流中获取数据

我们如何从可读流中读取数据？使用可写流：

```JS
const Stream = require('stream');

const readableStream = new Stream.Readable({
  read() {},
});
const writableStream = new Stream.Writable();

writableStream._write = (chunk, encoding, next) => {
  console.log(chunk.toString());
  next();
};

readableStream.pipe(writableStream);

readableStream.push('hi!');
readableStream.push('ho!');
```

您还可以使用`readable`事件直接使用可读流：

```JS
readableStream.on('readable', () => {
  console.log(readableStream.read());
});
```

### 如何将数据发送到可写流

使用流`write()`方法：

```JS
writableStream.write('hey!\n');
```

### 发信号通知您结束写入的可写流

使用`end()`方法：

```JS
const Stream = require('stream');

const readableStream = new Stream.Readable({
  read() {},
});
const writableStream = new Stream.Writable();

writableStream._write = (chunk, encoding, next) => {
  console.log(chunk.toString());
  next();
};

readableStream.pipe(writableStream);

readableStream.push('hi!');
readableStream.push('ho!');

readableStream.on('close', () => writableStream.end());
writableStream.on('close', () => console.log('ended'));

readableStream.destroy();
```

在上面的示例中，在可读流 `close` 事件的侦听器中调用 `end()` 以确保在所有写入事件都已经通过了管道之前不会调用它，因为这样做会导致发出`error`事件。在可读流上调用 `destroy()` 会导致发出`close`事件。可写流`close`事件的侦听器演示了该过程的完成，因为它是在调用 `end()` 之后发出的。

### 如何创建转换流

我们从[`stream`模块](https://nodejs.org/api/stream.html)中获取 Transform 流，对其进行初始化并实现 `transform._transform()` 方法。

首先创建一个转换流对象：

```JS
const { Transform } = require('stream');

const transformStream = new Transform();
```

然后实现`_transform`：

```JS
transformStream._transform = (chunk, encoding, callback) => {
  transformStream.push(chunk.toString().toUpperCase());
  callback();
};
```

通过管道传输可读流：

```JS
process.stdin.pipe(transformStream).pipe(process.stdout);
```


---

***`//End of Article`***

---


![公众号二维码](https://neptliang.github.io/img/Article/WeChatBlog.png)