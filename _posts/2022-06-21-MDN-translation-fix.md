---
layout:       post
title:        "MDN 中文内容问题翻译部分机翻整理"
subtitle:     "undefined"
date:         2022-06-21 17:54:32
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - FrontEnd
---


# 前言

MDN 有部分中文内容明显翻译得有问题，且到了无法理解的程度，所以对照着原文机翻整理一下。


# [`Object.freeze()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze)

## [例子](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze#examples)

### [冻结数组](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze#%E5%86%BB%E7%BB%93%E6%95%B0%E7%BB%84)

```js
let a = [0];
Object.freeze(a); // 现在数组不能被修改了。

a[0]=1; // fails silently
a.push(2); // fails silently

// In strict mode such attempts will throw TypeErrors
function fail() {
  "use strict"
  a[0] = 1;
  a.push(2);
}

fail();
```

被冻结的对象是不可变的。但也不总是这样。下例展示了冻结对象不是常量对象（浅冻结）。

```js
obj1 = {
  internal: {}
};

Object.freeze(obj1);
obj1.internal.a = 'aValue';

obj1.internal.a // 'aValue'
```

对于一个常量对象，整个引用图（对其他对象的直接和间接引用）只能引用不可变的冻结对象。冻结的对象被认为是不可变的，因为整个对象中的全部对象*状态*（值和对其他对象的引用）是固定的。注意，字符串，数字和布尔总是不可变的，而函数和数组是对象。

#### 什么是“浅冻结”？

调用`Object.freeze(object)`的结果仅适用于`object`自身的直接属性，并且将*仅*在`object`上阻止未来的属性添加、删除或值重新分配操作。如果这些属性的值是对象本身，则这些对象不会被冻结，并且可能是属性添加、删除或值重新分配操作的目标。

```js
const employee = {
  name: "Mayank",
  designation: "Developer",
  address: {
    street: "Rohini",
    city: "Delhi"
  }
};

Object.freeze(employee);

employee.name = "Dummy"; // fails silently in non-strict mode
employee.address.city = "Noida"; // attributes of child object can be modified

console.log(employee.address.city) // Output: "Noida"
```

要使对象不可变，需要递归冻结每个类型为对象的属性（深冻结）。当你知道对象在引用图中不包含任何[环](https://en.wikipedia.org/wiki/Cycle_(graph_theory)) (循环引用) 时，根据你的设计对逐个对象使用该模式，否则将触发无限循环。一个增强功能`deepFreeze()`是拥有一个接收路径（例如数组）参数的内部函数，这样您就可以在对象变成不可变的过程中抑制递归调用`deepFreeze()`。你仍然有冻结不应冻结的对象的风险，例如 [window]。

```js
// 深冻结函数。
function deepFreeze(obj) {

  // 取回定义在 obj 上的属性名
  var propNames = Object.getOwnPropertyNames(obj);

  // 在冻结自身之前冻结属性
  propNames.forEach(function(name) {
    var prop = obj[name];

    // 如果 prop 是个对象，冻结它
    if (typeof prop == 'object' && prop !== null)
      deepFreeze(prop);
  });

  // 冻结自身 (no-op if already frozen)
  return Object.freeze(obj);
}

obj2 = {
  internal: {}
};

deepFreeze(obj2);
obj2.internal.a = 'anotherValue';
obj2.internal.a; // undefined
```

<!-- ---


# [对象模型的细节](https://developer.mozilla.org/zh-CN/docs/conflicting/Web/JavaScript/Inheritance_and_the_prototype_chain)

![](https://developer.mozilla.org/en-US/docs/web/javascript/guide/using_classes/figure8.1.png) -->

---


# [this](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/this)

## [例子](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/this#%E7%A4%BA%E4%BE%8B)

### [箭头函数](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/this#%E7%AE%AD%E5%A4%B4%E5%87%BD%E6%95%B0)

在[箭头函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)中，`this` 保留了封闭词法上下文的 `this` 值。在全局代码中，它将被设置为全局对象：

```JS
const globalObject = this;
const foo = (() => this);
console.log(foo() === globalObject); // true
```

> 注意：如果在调用箭头函数时将 `this` 参数传递给`call`、`bind`或`apply`，它将被忽略。您仍然可以在调用前添加参数，但第一个参数 (`thisArg`) 应设置为`null`。

```JS
// Call as a method of an object
const obj = { func: foo };
console.log(obj.func() === globalObject); // true

// 尝试使用call设置this
console.log(foo.call(obj) === globalObject); // true

// 尝试使用bind设置this
const boundFoo = foo.bind(obj);
console.log(boundFoo() === globalObject); // true
```

无论如何，`foo` 的 `this` 都被设置为它被创建时的状态（在上面的示例中，全局对象）。这同样适用于在其他函数内部创建的箭头函数：它们的 `this` 仍然是封闭的词汇上下文。

```JS
// 创建带有方法bar的obj，方法bar返回一个
// 返回this的函数。返回的函数被创建为
// 一个箭头函数，因此它的this被永久绑定到
// 它的外围函数的this上。bar的值可以
// 在调用时设置，而调用又设置返回函数的值。
// 注意:在这个上下文中`bar()`语法等价于`bar: function ()`
const obj = {
  bar() {
    const x = (() => this);
    return x;
  }
};

// 作为obj的一个方法调用bar，设置它的this为obj
// 将返回函数的引用赋给fn
const fn = obj.bar();

// 在没有设置this的情况下调用fn，通常默认
// 为全局对象或在严格模式下为undefined
console.log(fn() === obj); // true

// 但是如果你引用了obj的方法而没有调用它，请注意
const fn2 = obj.bar;
// 在bar方法中调用箭头函数的this将返回window，因为它遵循了fn2中的this。
console.log(fn2()() === window); // true
```

在上面，分配给`obj.bar`的函数（称为匿名函数 A） 返回另一个作为箭头函数创建的函数（称为匿名函数 B）。结果，函数 B 的 `this` 在调用时被永久设置为 `obj.bar`（函数 A）的 `this`。当调用返回的函数（函数 B）时，它的 `this` 将始终是它最初设置的值。在上面的代码示例中，函数 B 的 `this` 被设置为函数 A 的 `this`，它是 `obj`，因此即使以通常将其 `this` 设置为 `undefined` 或全局对象的方式（或任何其他方法，如上例中的全局执行上下文）调用它，它仍保持设置为 `obj`。

---


# [传输层安全协议](https://developer.mozilla.org/zh-CN/docs/Web/Security/Transport_Layer_Security)

## 历史

HTTPS 被引入时，它是基于安全套接字层 (SSL) 2.0的，这是 Netscape 引入的一种技术。不久之后它更新到 SSL 3.0，随着其使用范围的扩大，很明显需要指定一种通用的标准加密技术，以确保所有 Web 浏览器和服务器之间的互通性。[互联网工程专案组](https://www.ietf.org/)（IETF）于 1999 年 1 月在 [RFC 2246](https://datatracker.ietf.org/doc/html/rfc2246) 中指定了 TLS 1.0。TLS 的当前版本是 1.3（[RFC 8446](https://datatracker.ietf.org/doc/html/rfc8446)）。

尽管网络现在使用 TLS 进行加密，但出于习惯，许多人仍然将其称为“SSL”。

尽管 TLS 可以在任何低层传输协议之上使用，但该协议的最初目标是加密 HTTP 流量。使用 TLS 加密的 HTTP 通常称为[HTTPS](https://developer.mozilla.org/en-US/docs/Glossary/https)。按照惯例，TLS 加密的 Web 流量默认在端口 443 上进行交换，而未加密的 HTTP 默认使用端口 80。HTTPS 仍然是 TLS 的重要用例。

## 基于 TLS 的 HTTP

TLS 提供三项主要服务，有助于确保用它交换的数据的安全性：

* 验证

  身份验证允许通信的每一方验证对方是他们声称的身份。

* 加密
  
  数据在用户代理和服务器之间传输时被加密，以防止未经授权的各方读取和解释数据。

* 完整性

  TLS 确保在对数据进行加密、传输和解密之间没有信息被丢失、损坏、篡改或伪造。

TLS 连接从握手阶段开始，其中客户端和服务器就共享密钥和重要参数（如密码套件）达成一致。一旦参数和数据交换模式，应用程序数据，如 HTTP，被交换。

### 密码套件

TLS 握手协商的主要参数是[密码套件](https://en.wikipedia.org/wiki/Cipher_suite)。

在 TLS 1.2 和更早版本中，协商密码套件包括一组加密算法，它们共同提供共享密钥的协商、服务器验证的方式以及将用于加密数据的方法。

TLS 1.3 中的密码套件主要管理数据的加密，单独的协商方法用于密钥协商和验证。

不同的软件可能对相同的密码套件使用不同的名称。例如，OpenSSL 和 GnuTLS 中使用的名称与 TLS 标准中的名称不同。Mozilla OpSec 团队关于 TLS 配置的文章中的[密码名称对应表](https://wiki.mozilla.org/Security/Server_Side_TLS#Cipher_names_correspondence_table)列出了这些名称以及有关兼容性和安全级别的信息。

### 配置您的服务器

正确配置服务器至关重要。通常，您应该尝试将密码支持限制为可能与您希望能够连接到您的站点的浏览器兼容的最新密码。[Mozilla OpSec TLS 配置指南](https://wiki.mozilla.org/Security/Server_Side_TLS)提供了有关推荐配置的更多信息。

为了帮助您配置站点，Mozilla 提供了一个有用的 [TLS 配置生成器](https://ssl-config.mozilla.org/)，它将为以下 Web 服务器生成配置文件：

* Apache
* Nginx
* Lighttpd
* HAProxy
* Amazon Web Services CloudFormation 弹性负载均衡器

推荐使用[配置器](https://ssl-config.mozilla.org/)来创建配置以满足您的需求；然后将其复制并粘贴到服务器上的相应文件中，然后重新启动服务器以获取更改。配置文件可能需要一些调整以包含自定义设置，因此请务必在使用前查看生成的配置；在不确保对域名等的任何引用正确的情况下安装配置文件将导致服务器无法正常工作。

## TLS 1.3

[RFC 8446：TLS 1.3](https://datatracker.ietf.org/doc/html/rfc8446) 是一次对 TLS 的重大修订。TLS 1.3 包括许多提高安全性和性能的更改。TLS 1.3 的目标是：

* 删除 TLS 1.2 的未使用和不安全的功能。
* 在设计中包含强大的安全分析。
* 通过加密更多协议来改善隐私。
* 减少完成握手所需的时间。

TLS 1.3 改变了协议的许多基础，但几乎保留了以前的 TLS 版本的所有基本功能。对于 Web，可以启用 TLS 1.3 而不会影响兼容性，除了一些罕见的例外（见下文）。

TLS 1.3 的主要变化是：

* 在大多数情况下，TLS 1.3 握手在一次往返中完成，从而减少了握手延迟。
* 服务器可以启用 0-RTT（零往返时间）握手。重新连接到服务器的客户端可以立即发送请求，完全消除 TLS 握手的延迟。尽管 0-RTT 带来的性能提升可能非常显着，但它们也存在重放攻击的风险，因此在启用此功能之前需要小心谨慎。
* TLS 1.3 仅支持前向安全模式，除非连接恢复或使用预共享密钥。
* TLS 1.3 定义了一组新的 TLS 1.3 独有的密码套件。这些密码套件都使用现代的带有关联数据的认证加密（AEAD）算法。
* TLS 1.3 握手是加密的，但建立共享密钥所需的消息除外。特别是，这意味着服务器和客户端证书是加密的。但是请注意，客户端发送到服务器的服务器身份（server_name 或 SNI 扩展）未加密。
* 许多机制已被禁用：重新协商、通用数据压缩、[数字签名算法](https://en.wikipedia.org/wiki/Digital_Signature_Algorithm)（DSA）证书、静态 RSA 密钥交换以及与自定义 Diffie-Hellman (DH) 组的密钥交换。

TLS 1.3 草案版本的实现可用。某些浏览器启用了 TLS 1.3，包括 0-RTT 模式。启用 TLS 1.3 的 Web 服务器可能需要调整配置以允许 TLS 1.3 成功运行。

TLS 1.3 只增加了一个重要的新用例。0-RTT 握手可以为延迟敏感的应用程序（如 Web）提供显著的性能提升。启用 0-RTT 需要额外的步骤，以确保成功部署和管理重放攻击的风险。

在 TLS 1.3 中删除重新协商可能会影响一些依赖于使用证书进行客户端身份验证的 Web 服务器。一些 Web 服务器使用重新协商来确保客户端证书被加密，或者仅在请求某些资源时才请求客户端证书。为了客户端证书的隐私，TLS 1.3 握手的加密确保客户端证书被加密；但是，这可能需要一些软件更改。TLS 1.3 支持使用证书的反应式客户端身份验证，但未广泛实施。替代机制正在开发中，也将支持 HTTP/2。

## 淘汰旧的 TLS 版本

为了帮助打造更现代、更安全的网络，所有主要浏览器都在 2020 年初开始取消对 TLS 1.0 和 1.1 的支持。您需要确保您的网络服务器支持 TLS 1.2 或 1.3。

从版本 74 开始，Firefox 将在使用旧 TLS 版本连接到服务器时返回[安全连接失败](https://support.mozilla.org/en-US/kb/secure-connection-failed-firefox-did-not-connect)错误（[错误 1606734](https://bugzilla.mozilla.org/show_bug.cgi?id=1606734)）。

## TLS 握手超时值

如果 TLS 握手由于某些原因开始变慢或无响应，则用户体验可能会受到很大影响。为了缓解这个问题，现代浏览器已经实现了握手超时：

* 从版本 58 开始，Firefox 实现了 TLS 握手超时，默认值为 30 秒。可以通过在 about:config 中编辑 network.http.tls-handshake-timeout 首选项来改变超时值。

## 也可以看看

* [Mozilla SSL 配置生成器](https://ssl-config.mozilla.org/)和[Cipherlist.eu](https://cipherlist.eu/)可以帮助您为服务器生成配置文件以保护您的站点。
* Mozilla 运营安全 (OpSec) 团队维护一个带有[参考 TLS 配置](https://wiki.mozilla.org/Security/Server_Side_TLS)的 wiki 页面。
* [Mozilla Observatory](https://observatory.mozilla.org/)、[SSL Labs](https://www.ssllabs.com/ssltest/)和[Cipherscan](https://github.com/mozilla/cipherscan)可以帮助您测试站点以了解其 TLS 配置的安全性。
* [安全上下文](https://developer.mozilla.org/en-US/docs/Web/Security/Secure_Contexts)
* [Strict-Transport-Security](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Strict-Transport-Security) HTTP 标头


# [HTTP缓存](https://developer.mozilla.org/en-US/docs/Web/HTTP/Caching)

## 概述

<!-- HTTP 缓存存储与请求关联的响应，并将存储的响应重用于后续请求。

可重用性有几个优点。首先，由于不需要将请求传递到源服务器，因此客户端和缓存越近，响应速度就越快。最典型的例子是浏览器本身为浏览器请求存储缓存。

此外，当响应可重用时，源服务器不需要处理请求——因此它不需要解析和路由请求、根据 cookie 恢复会话、查询数据库以获取结果或渲染模板引擎。这减少了服务器上的负载。

缓存的正确操作对系统的健康至关重要。 -->

## 缓存类型

<!-- 在 [HTTP 缓存](https://httpwg.org/specs/rfc9111.html)规范中，有两种主要的缓存类型：私有缓存和共享缓存。

### 私有缓存

私有缓存是绑定到特定客户端的缓存——通常是浏览器缓存。由于存储的响应不与其他客户端共享，因此私有缓存可以存储该用户的个性化响应。

另一方面，如果个性化内容存储在私有缓存以外的缓存中，那么其他用户可能能够检索到这些内容——这可能会导致无意的信息泄露。

如果响应包含个性化内容并且您只想将响应存储在私有缓存中，则必须指定`private`指令。

```HTTP
Cache-Control: private
```

个性化内容通常由 cookie 控制，但 cookie 的存在并不总是表明它是私有的，因此单独的 cookie 不会使响应成为私有的。

请注意，如果响应具有`Authorization`标头，则不能将其存储在私有缓存（或共享缓存，除非`public`被指定）中。 -->

### 共享缓存

<!-- 共享缓存位于客户端和服务器之间，可以存储可以在用户之间共享的响应。共享缓存可以进一步细分为**代理缓存**和**托管缓存**。 -->

#### 代理缓存

<!-- 除了访问控制的功能外，一些代理还实现了缓存以减少出网的流量。这通常不由服务开发人员管理，因此必须由适当的 HTTP 标头等控制。然而，在过去，过时的代理缓存实现——例如没有正确理解 HTTP 缓存标准的实现——经常给开发人员带来问题。

像下面这样的 **Kitchen-sink 标头**用于尝试解决不理解当前 HTTP 缓存规范指令（如 `no-store`）的“旧且未更新的代理缓存”实现。

```HTTP
Cache-Control: no-store, no-cache, max-age=0, must-revalidate, proxy-revalidate
```

然而，近年来，随着 HTTPS 变得越来越普遍，客户端/服务器通信变得加密，在许多情况下，路径中的代理缓存只能隧道响应而不能充当缓存。因此，在这种情况下，无需担心过时的代理缓存实现甚至看不到响应。 -->

另一方面，如果[TLS](https://developer.mozilla.org/en-US/docs/Glossary/TLS)桥接代理通过在 PC 上安装来自组织管理的[CA（证书颁发机构）](https://developer.mozilla.org/en-US/docs/Glossary/Certificate_authority)的证书，以中间人方式解密所有通信，并执行访问控制等——可以查看响应的内容并将其缓存。但是，由于[CT（证书透明度）](https://developer.mozilla.org/en-US/docs/Web/Security/Certificate_Transparency)近年来已经普及，并且一些浏览器只允许带有 SCT（签名证书时间戳）颁发的证书，因此这种方法需要企业政策的应用。在这样的受控环境中，无需担心代理缓存“已过时且未更新”。

#### 托管缓存

<!-- 托管缓存由服务开发人员明确部署，以卸载源服务器并有效地交付内容。例子包括反向代理、CDN 和 service worker 与缓存 API 的组合。

托管缓存的特性因部署的产品而异。在大多数情况下，您可以通过`Cache-Control`标头和您自己的配置文件或仪表板来控制缓存的行为。 -->

例如，HTTP缓存规范本质上没有定义显式删除缓存的方法——但是使用托管缓存，可以通过仪表板操作、API 调用、重新启动等随时删除存储的响应。这允许更主动的缓存策略。

<!-- 也可以忽略标准 HTTP 缓存规范协议以支持显式操作。例如，可以指定以下内容以选择退出私有缓存或代理缓存，同时使用您自己的策略仅在托管缓存中进行缓存。

```HTTP
Cache-Control: no-store
```

例如，Varnish Cache 使用 VCL（Varnish Configuration Language，一种[DSL](https://developer.mozilla.org/en-US/docs/Glossary/DSL/Domain_specific_language)）逻辑来处理缓存存储，而 service workers 结合 Cache API 允许您在 JavaScript 中创建该逻辑。

这意味着如果托管缓存故意忽略`no-store`指令，则无需将其视为“不符合”标准。您应该做的是，避免使用 kitchen-sink 标头，但仔细阅读您正在使用的任何托管缓存机制的文档，并确保您以您选择使用的机制提供的方式正确控制缓存。 -->

请注意，某些 CDN 提供自己的标头，这些标头仅对该 CDN 有效（例如，`Surrogate-Control`）。目前，定义一个`CDN-Cache-Control`标头来标准化这些标头的工作正在进行中。

<!-- ![缓存类型](https://developer.mozilla.org/en-US/docs/Web/HTTP/Caching/type-of-cache.png) -->

## 启发式缓存

<!-- HTTP 旨在尽可能多地缓存，因此即使没有给定 `Cache-Control`，如果满足某些条件，响应也会被存储和重用。这叫作**启发式缓存**。 -->

例如，采取以下响应。此响应最后一次更新是在 1 年前。

<!-- ```HTTP
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 1024
Date: Tue, 22 Feb 2022 22:22:22 GMT
Last-Modified: Tue, 22 Feb 2021 22:22:22 GMT

<!doctype html>
…
```

启发式地知道，整整一年没有更新的内容在那之后的一段时间内不会更新。因此，客户端存储此响应（尽管缺少`max-age`）并重用它一段时间。复用多长时间取决于实现，但规范建议存储后大约 10%（在本例中为 0.1 年）的时间。

启发式缓存是在`Cache-Control`支持被广泛采用之前出现的一种解决方法，基本上所有响应都应明确指定`Cache-Control`标头。 -->

## 基于age的新鲜和陈旧

<!-- 已存储的 HTTP 响应有两种状态：**新鲜**和**陈旧**。*新鲜*状态通常表示响应仍然有效并且可以重复使用，而*陈旧*状态表示缓存的响应已经过期。

确定响应何时是新鲜的和何时是陈旧的标准是 **age**。在 HTTP 中，age 是自响应生成以来经过的时间。这类似于其他缓存机制中的[TTL](https://developer.mozilla.org/en-US/docs/Glossary/TTL)。

以下面的示例响应为例（604800 秒是一周）：

```HTTP
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 1024
Date: Tue, 22 Feb 2022 22:22:22 GMT
Cache-Control: max-age=604800

<!doctype html>
…
```

存储示例响应的缓存计算响应生成后经过的时间，并将结果用作响应的*age*。

对于示例响应，`max-age` 的含义如下：

* 如果响应的age*小于*一周，则响应是*新鲜*的。
* 如果响应的age*超过*一周，则响应是*陈旧*的。

只要存储的响应保持新鲜，它将用于满足客户端请求。

当响应存储在共享缓存中时，有必要通知客户端响应的age。继续举例，如果共享缓存将响应存储了一天，则共享缓存将向后续客户端请求发送以下响应。

```HTTP
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 1024
Date: Tue, 22 Feb 2022 22:22:22 GMT
Cache-Control: max-age=604800
Age: 86400

<!doctype html>
…
``` -->

收到该响应的客户端会发现它在剩余的 518400 秒内是新鲜的，即响应的 `max-age` 和 `Age` 之间的差。

## Expires 或 max-age

<!-- 在 HTTP/1.0 中，新鲜度过去由`Expires`标头指定。

`Expires`标头使用明确的时间而不是通过指定经过的时间来指定缓存的寿命。

```HTTP
Expires: Tue, 28 Feb 2022 22:22:22 GMT
``` -->

但是时间格式难以解析，很多实现bug被发现，并且有可能通过故意偏移系统时钟来诱发问题；因此，`max-age`——用于指定经过的时间——在 HTTP/1.1 中被用于 `Cache-Control`。

<!-- 如果 `Expires` 和 `Cache-Control: max-age` 都可用，则 `max-age` 被定义为首选。所以现在 HTTP/1.1 被广泛使用，没有必要提供 `Expires`。 -->

## Vary

<!-- 区分响应的方式本质上是基于它们的 URL：

![用 url 键控](https://developer.mozilla.org/en-US/docs/Web/HTTP/Caching/keyed-with-url.png)

但是响应的内容并不总是相同的，即使它们具有相同的 URL。特别是在进行内容协商时，来自服务器的响应可能取决于`Accept`、`Accept-Language`和`Accept-Encoding`请求标头的值。 -->

例如，对于带有`Accept-Language: en`标头返回并缓存的英文内容，接着为具有 `Accept-Language: ja` 请求标头的请求重用该缓存响应是不可取的。在这种情况下，您可以通过将“`Accept-Language`”添加到`Vary`标头的值来根据语言分别缓存响应。

<!-- ```HTTP
Vary: Accept-Language
```

这会导致缓存基于响应 URL 和 `Accept-Language` 请求标头的组合进行键控，而不是仅基于响应 URL。

![用 url 和语言进行键控](https://developer.mozilla.org/en-US/docs/Web/HTTP/Caching/keyed-with-url-and-language.png) -->

此外，如果您正在基于 user agent 提供内容优化（例如，响应式设计），您可能会想在 `Vary` 标头的值中包含“`User-Agent`”。但是，`User-Agent`请求头通常有非常多的变体，这大大降低了缓存被重用的机会。因此，如果可能，请考虑一种基于特征检测而不是基于`User-Agent`请求标头来改变行为的方式。

<!-- 对于使用 cookie 来防止其他人重复使用缓存的个性化内容的应用程序，您应该指定 `Cache-Control: private` 而不是为 `Vary` 指定 cookie。 -->

## 验证

<!-- 过时的响应不会立即被丢弃。HTTP 有一种机制，可以通过询问源服务器将陈旧的响应转换为新的响应。这称为**验证**，有时称为**重新验证**。

验证是通过使用包含`If-Modified-Since`或`If-None-Match`请求标头的**条件请求**来完成的。 -->

### If-Modified-Since

以下响应在 22:22:22 生成，`max-age`为 1 小时，因此您知道它在 23:22:22 之前是新鲜的。

```HTTP
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 1024
Date: Tue, 22 Feb 2022 22:22:22 GMT
Last-Modified: Tue, 22 Feb 2022 22:00:00 GMT
Cache-Control: max-age=3600

<!doctype html>
…
```

在 23:22:22，响应变得陈旧，缓存不能被重用。因此，下面的请求显示了客户端发送带有`If-Modified-Since`请求标头的请求，以询问服务器自指定时间以来是否有任何改变。

```HTTP
GET /index.html HTTP/1.1
Host: example.com
Accept: text/html
If-Modified-Since: Tue, 22 Feb 2022 22:00:00 GMT
```

如果内容自指定时间以来没有更改，服务器将响应`304 Not Modified`。

<!-- 由于此响应仅表示“没有变化”，因此没有响应体——只有一个状态码——因此传输大小非常小。

```HTTP
HTTP/1.1 304 Not Modified
Content-Type: text/html
Date: Tue, 22 Feb 2022 23:22:22 GMT
Last-Modified: Tue, 22 Feb 2022 22:00:00 GMT
Cache-Control: max-age=3600
```

收到该响应后，客户端将存储的陈旧响应恢复为新鲜的，并可以在剩余的 1 小时内重用它。

服务器可以从操作系统文件系统中获取修改时间，这对于提供静态文件的情况来说是比较容易做到的。但是，也存在一些问题；例如，时间格式复杂且难以解析，分布式服务器难以同步文件更新时间。

为了解决这些问题，`ETag`响应标头被标准化作为替代方案。 -->

### ETag/If-None-Match

<!-- `ETag`响应头的值是服务器生成的任意值。服务器必须如何生成值没有任何限制，因此服务器可以根据他们选择的任何方式自由设置值——例如响应体内容的哈希或版本号。

举个例子，如果`ETag`头使用哈希值，`index.html`资源的哈希值为`33a64df5`，则响应如下：

```HTTP
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 1024
Date: Tue, 22 Feb 2022 22:22:22 GMT
ETag: "33a64df5"
Cache-Control: max-age=3600

<!doctype html>
…
```

如果该响应是陈旧的，则客户端获取缓存响应的`ETag`响应标头的值，并将其放入`If-None-Match`请求标头中，以询问服务器资源是否已被修改：

```HTTP
GET /index.html HTTP/1.1
Host: example.com
Accept: text/html
If-None-Match: "33a64df5"
```

如果服务器为请求的资源确定的`ETag`标头的值与请求中的`If-None-Match`值相同，则服务器将返回`304 Not Modified`。 -->

但是，如果服务器确定请求的资源现在应该具有不同的 `ETag` 值，则服务器将改为以 `200 OK` 和资源的最新版本进行响应。

<!-- > **注意**：在评估如何使用 `ETag` 和 `Last-Modified` 时，请考虑以下事项： 在缓存重新验证期间，如果 `ETag` 和 `Last-Modified` 都存在，则 `ETag` 优先。因此，如果您只考虑缓存，您可能会认为 `Last-Modified` 是不必要的。然而，`Last-Modified` 不仅仅对缓存有用；相反，它是一个标准的 HTTP 标头，内容管理（CMS）系统也使用它来显示上次修改时间，由爬虫调整爬取频率，以及用于其他各种目的。因此，考虑到整个 HTTP 生态系统，最好同时提供 `ETag` 和 `Last-Modified`。

### 强制重新验证

如果您不希望重用响应，而是希望始终从服务器获取最新内容，则可以使用 `no-cache` 指令强制验证。

通过在响应中添加 `Cache-Control: no-cache` 以及 `Last-Modified` 和 `ETag`（如下所示），如果请求的资源已更新，客户端将收到 `200 OK` 响应，如果请求的资源尚未更新，则会收到 `304 Not Modified` 响应。

```HTTP
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 1024
Date: Tue, 22 Feb 2022 22:22:22 GMT
Last-Modified: Tue, 22 Feb 2022 22:00:00 GMT
ETag: deadbeef
Cache-Control: no-cache

<!doctype html>
…
```

人们常说 `max-age=0` 和 `must-revalidate` 的组合与 `no-cache` 具有相同的含义。

```HTTP
Cache-Control: max-age=0, must-revalidate
```

`max-age=0`意味着响应立即过时，`must-revalidate`意味着一旦响应过时就不得在没有重新验证的情况下重用它——因此，结合起来，语义似乎与 `no-cache` 相同。

然而，`max-age=0` 的使用是 HTTP/1.1 之前的许多实现无法处理 `no-cache` 指令这一事实的残余——因此为了解决这个限制，`max-age=0` 被用作一种解决方法。

但是现在符合 HTTP/1.1 的服务器已经广泛部署，没有理由使用 `max-age=0` 和 `must-revalidate` 组合——您应该只使用 `no-cache`。

## 不要缓存

`no-cache`指令不阻止响应的存储，而是阻止在没有重新验证的情况下重用响应。

如果您不希望将一个响应存储在任何缓存中，请使用 `no-store`。

```HTTP
Cache-Control: no-store
```

但是，一般来说，实践中的“不缓存”要求相当于以下一组情况：

* 出于隐私原因，不希望特定客户端以外的任何人存储响应。
* 希望始终提供最新信息。
* 不知道在过时的实现中会发生什么。

在这种情况下，`no-store`并不总是最合适的指令。

以下部分更详细地介绍了这些情况。

### 不要与他人共享

如果具有个性化内容的响应意外地对缓存的其他用户可见，那将是有问题的。

在这种情况下，使用 `private` 指令将导致个性化响应仅与特定客户端一起存储，而不会被泄露给缓存的任何其他用户。

```HTTP
Cache-Control: private
```

在这种情况下，即使给定了 `no-store`，也必须给定 `private`。

### 每次都提供最新的内容

`no-store` 指令阻止存储响应，但不会删除同一 URL 的任何已存储响应。

换句话说，如果已经为特定 URL 存储了旧响应，则返回`no-store`不会阻止旧响应被重用。

但是，`no-cache`指令将强制客户端在重用任何存储的响应之前发送验证请求。

```HTTP
Cache-Control: no-cache
```

如果服务器不支持条件请求，你可以强制客户端每次都访问服务器，并且总是得到最新的 `200 OK` 响应。

### 处理过时的实现

作为忽略 `no-store` 的过时实现的解决方法，您可能会看到使用了诸如以下内容的 kitchen-sink 标头。

```HTTP
Cache-Control: no-store, no-cache, max-age=0, must-revalidate, proxy-revalidate
```

[建议](https://docs.microsoft.com/troubleshoot/developer/browsers/connectivity-navigation/how-to-prevent-caching)使用`no-cache`作为处理这种过时实现的替代方案，如果从一开始就给定`no-cache`，它是没有问题的，因为服务器总是会收到请求。

如果您关心的是共享缓存，您可以通过添加`private`来确保防止意外缓存：

```HTTP
Cache-Control: no-cache, private
```

### `no-store`会失去什么

您可能认为添加`no-store`是选择退出缓存的正确方法。

但是，不建议随意授予`no-store`，因为您失去了 HTTP 和浏览器所拥有的许多优势，包括浏览器的后退/前进缓存。

因此，要获得 Web 平台的全部功能集的优势，更倾向于将 `no-cache` 与 `private` 结合使用。 -->

## 重新加载和强制重新加载

<!-- 可以对请求和响应执行验证。

**重新加载**和**强制重新加载**操作是从浏览器端执行验证的常见示例。 -->

### 重新加载

<!-- 为了从窗口损坏中恢复或更新到最新版本的资源，浏览器为用户提供了重新加载功能。

在浏览器重新加载期间发送的 HTTP 请求的简化视图如下所示：

```HTTP
GET / HTTP/1.1
Host: example.com
Cache-Control: max-age=0
If-None-Match: "deadbeef"
If-Modified-Since: Tue, 22 Feb 2022 20:20:20 GMT
```

（来自 Chrome、Edge 和 Firefox 的请求看起来很像上面；来自 Safari 的请求看起来会有点不同。）

请求中的`max-age=0`指令指定“重用 age 小于等于 0 的响应”——因此，实际上，中间存储的响应不会被重用。

因此，请求由`If-None-Match`和`If-Modified-Since`验证。 -->

该行为也在[Fetch](https://fetch.spec.whatwg.org/#http-network-or-cache-fetch)规范中定义，并且可以通过在缓存模式设置为`no-cache`的情况下调用`fetch()`来在 JavaScript 中重现（请注意，对于这种情况，`reload`不是正确的模式）：

<!-- ```JS
// Note: "reload" is not the right mode for a normal reload; "no-cache" is
fetch("/", { cache: "no-cache" });
``` -->

### 强制重新加载

出于向后兼容的原因，浏览器在重新加载期间使用 `max-age=0` ——因为 HTTP/1.1 之前的许多过时实现不理解 `no-cache`。但是在这个用例中`no-cache`现在很好，**强制重新加载**是绕过缓存响应的另一种方法。

<!-- 浏览器**强制重新加载**期间的 HTTP 请求如下所示：

```HTTP
GET / HTTP/1.1
Host: example.com
Pragma: no-cache
Cache-Control: no-cache
```

（来自 Chrome、Edge 和 Firefox 的请求看起来很像上面的；来自 Safari 的请求看起来会有点不同。）

由于这不是带有 `no-cache` 的条件请求，因此您可以确定您会从原始服务器获得 `200 OK`。 -->

该行为在[Fetch](https://fetch.spec.whatwg.org/#http-network-or-cache-fetch)规范中也有定义，并且可以通过在将缓存模式设置为 `reload` 的情况下调用 `fetch()` 来在 JavaScript 中重现（注意缓存模式不是 `force-reload`）：

<!-- ```JS
// Note: "reload" — rather than "no-cache" — is the right mode for a "force reload"
fetch("/", { cache: "reload" });
``` -->

### 避免重新验证

<!-- 永远不会改变的内容应该通过使用缓存破坏（即在请求 URL 中包含版本号、哈希值等）来获得较长的 `max-age`。

但是，当用户重新加载时，即使服务器知道内容是不可变的，也会发送重新验证请求。

为了阻止这种情况出现，`immutable`指令可用于明确指示不需要重新验证，因为内容永远不会改变。

```HTTP
Cache-Control: max-age=31536000, immutable
```

这可以防止在重新加载期间进行不必要的重新验证。 -->

请注意，Chrome 没有实现该指令，而是[更改了其实现](https://blog.chromium.org/2017/01/reload-reloaded-faster-and-leaner-page_26.html)，以便在重新加载子资源期间不执行重新验证。

## 删除存储的响应

<!-- 基本上没有办法删除已经以较长的 `max-age` 存储的响应。

假设存储了以下来自 `https://example.com/` 的响应。

```HTTP
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 1024
Cache-Control: max-age=31536000

<!doctype html>
…
``` -->

一旦响应在服务器上过期，您可能希望覆盖该响应，但是一旦响应被储存，服务器就无法执行任何操作——由于缓存，不再有请求到达服务器。

<!-- 规范中提到的方法之一是使用不安全的方法（例如 `POST`）发送对同一 URL 的请求，但对于许多客户端而言，通常很难故意这样做。

还有一个`Clear-Site-Data: cache`标头和值的规范，但[并非所有浏览器都支持它](https://groups.google.com/a/mozilla.org/g/dev-platform/c/I939w1yrTp4)——即使使用它，它也只影响浏览器缓存，对中间缓存没有影响。 -->

因此，应该假设任何存储的响应都将在其`max-age`内保留，除非用户手动执行重新加载、强制重新加载或清除历史记录操作。

<!-- 缓存减少了对服务器的访问，这意味着服务器失去了对该 URL 的控制。如果服务器不想失去对 URL 的控制——例如，在资源被频繁更新的情况下——你应该添加`no-cache`以便服务器将始终接收请求并发送预期的响应。

## 请求折叠

共享缓存主要位于源服务器之前，旨在减少到源服务器的流量。

因此，如果多个相同的请求同时到达共享缓存，中间缓存将代表自己将单个请求转发到源，然后可以将结果重用于所有客户端。这称为***请求折叠***。

当请求同时到达时会发生请求折叠，因此即使在响应中给定`max-age=0`或`no-cache`，它也会被重用。

如果响应是针对特定用户个性化的，并且您不希望它在折叠中共享，则应添加`private`指令：

![请求折叠](https://developer.mozilla.org/en-US/docs/Web/HTTP/Caching/request-collapse.png) -->

## 常见的缓存模式

<!-- `Cache-Control`规范中有很多指令，可能很难全部理解。但是大多数网站都可以被几种模式的组合所覆盖。

本节介绍设计缓存的常见模式。

### 默认设置

如上所述，缓存的默认行为（即对于没有 `Cache-Control` 的响应）不仅仅是“不缓存”，而是根据所谓的“启发式缓存”进行隐式缓存。

为了避免这种启发式缓存，最好显式地为所有响应提供一个默认的 `Cache-Control` 标头。

为确保默认情况下始终传输最新版本的资源，通常的做法是使默认`Cache-Control`值包括`no-cache`：

```HTTP
Cache-Control: no-cache
```

此外，如果服务实现了 cookie 或其他登录方式，并且内容是针对每个用户个性化的，那么也必须提供 `private`，以防止与其他用户共享：

```HTTP
Cache-Control: no-cache, private
``` -->

### 缓存破坏

<!-- 最适合缓存的资源是静态不可变文件，其内容永远不会改变。而对于确实发生变化的资源，通常的最佳做法是在每次内容发生变化时更改 URL，以便 URL 单元可以缓存更长的时间。

例如，考虑以下 HTML：

```HTML
<script src="bundle.js"></script>
<link rel="stylesheet" href="build.css" />
<body>
  hello
</body>
```

在现代 Web 开发中，JavaScript 和 CSS 资源会随着开发的进展而频繁更新。此外，如果客户端使用的 JavaScript 和 CSS 资源的版本不同步，则显示将中断。 -->

所以上面的 HTML 使得使用 `max-age` 缓存 `bundle.js` 和 `build.css` 变得很困难。

因此，您可以使用包含一个根据版本号或哈希值变化的部分的 URL 来提供 JavaScript 和 CSS。一些做到这一点的方法如下所示。

<!-- ```sh
# version in filename
bundle.v123.js

# version in query
bundle.js?v=123

# hash in filename
bundle.YsAIAAAA-QG4G6kCMAMBAAAAAAAoK.js

# hash in query
bundle.js?v=YsAIAAAA-QG4G6kCMAMBAAAAAAAoK
```

由于缓存根据它们的 URL 来区分资源，因此如果在更新资源时 URL 发生变化，缓存将不会再次被重用。

```HTML
<script src="bundle.v123.js"></script>
<link rel="stylesheet" href="build.v123.css" />
<body>
  hello
</body>
```

通过这种设计，JavaScript 和 CSS 资源都可以被缓存很长时间。那么`max-age`应该设置多长时间呢？QPACK 规范提供了该问题的答案。

[QPACK](https://datatracker.ietf.org/doc/html/rfc9204)是一种用于压缩 HTTP 标头字段的标准，其中定义了常用字段值的表。

一些常用的缓存头值如下所示。

```
36 cache-control max-age=0
37 cache-control max-age=604800
38 cache-control max-age=2592000
39 cache-control no-cache
40 cache-control no-store
41 cache-control public, max-age=31536000
``` -->

如果您选择其中一个编号的选项，则可以在通过 HTTP3 传输时将值压缩为 1 个字节。

<!-- 数字`37`、`38`和`41`分别代表一周、一个月和一年的时间段。 -->

因为缓存会在保存新条目时删除旧条目，所以存储的响应一周后仍然存在的可能性并不高——即使`max-age`设置为 1 周。因此，在实践中，您选择哪一种并没有太大的区别。

请注意，`41` 号具有最长的 `max-age`（1 年），但具有 `public`。

即使`Authorization`标头存在，`public`值也具有使响应可存储的效果。

> **注意**：只有在设置了 `Authorization` 标头时需要存储响应，才应使用 `public` 指令。否则不需要，因为只要给定 `max-age`，响应就会存储在共享缓存中。

<!-- 因此，如果响应是使用基本身份验证进行个性化的，则`public`可能会导致问题。如果您对此感到担忧，您可以选择第二长的值`38`（1 个月）。

```HTTP
# response for bundle.v123.js

# If you never personalize responses via Authorization
Cache-Control: public, max-age=31536000

# If you can't be certain
Cache-Control: max-age=2592000
``` -->

### 验证

<!-- 不要忘记设置`Last-Modified`和`ETag`标头，这样在重新加载时就不必重新传输资源。为预构建的静态文件生成这些标头很容易。

这里的`ETag`值可能是文件的哈希值。

```HTTP
# response for bundle.v123.js
Last-Modified: Tue, 22 Feb 2022 20:20:20 GMT
ETag: YsAIAAAA-QG4G6kCMAMBAAAAAAAoK
```

此外，可以添加`immutable`以防止重新加载时验证。

综合结果如下所示。

```HTTP
# bundle.v123.js
HTTP/1.1 200 OK
Content-Type: application/javascript
Content-Length: 1024
Cache-Control: public, max-age=31536000, immutable
Last-Modified: Tue, 22 Feb 2022 20:20:20 GMT
ETag: YsAIAAAA-QG4G6kCMAMBAAAAAAAoK
```

**缓存破坏**是一种通过在内容更改时更改 URL 来使响应在很长一段时间内可缓存的技术。该技术可以应用于所有子资源，例如图像。

> **注意**：在评估`immutable`和 QPACK 的使用时：如果您担心`immutable`会更改 QPACK 提供的预定义值，请考虑在这种情况下，可以通过将`Cache-Control`值分成两行来单独编码`immutable`部分——尽管这取决于特定 QPACK 实现使用的编码算法。

```HTTP
Cache-Control: public, max-age=31536000
Cache-Control: immutable
``` -->

### 主资源

<!-- 与子资源不同，主资源不能被缓存破坏，因为它们的 URL 不能像子资源 URL 一样被修饰。 -->

如果存储了以下 HTML 本身，即使内容在服务器端更新了，也无法显示最新版本。

<!-- ```HTML
<script src="bundle.v123.js"></script>
<link rel="stylesheet" href="build.v123.css" />
<body>
  hello
</body>
```

对于这种情况，`no-cache` 将是合适的——而不是 `no-store` ——因为我们不想存储 HTML，而只是希望它始终是最新的。

此外，添加 `Last-Modified` 和 `ETag` 将允许客户端发送条件请求，如果 HTML 没有更新，则可以返回 `304 Not Modified`：

```HTTP
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 1024
Cache-Control: no-cache
Last-Modified: Tue, 22 Feb 2022 20:20:20 GMT
ETag: AAPuIbAOdvAGEETbgAAAAAAABAAE
```

该设置适用于非个性化 HTML，但对于使用 cookie 进行个性化的响应（例如，在登录后），不要忘记同时指定`private`：

```HTTP  
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 1024
Cache-Control: no-cache, private
Last-Modified: Tue, 22 Feb 2022 20:20:20 GMT
ETag: AAPuIbAOdvAGEETbgAAAAAAABAAE
Set-Cookie: __Host-SID=AHNtAyt3fvJrUL5g5tnGwER; Secure; Path=/; HttpOnly
```

这同样可用于`favicon.ico`、`manifest.json`、`.well-known`和 API 端点，它们的 URL 不能使用缓存破坏来更改。

大多数 Web 内容都可以通过上述两种模式的组合来覆盖。 -->

### 有关托管缓存的更多信息

<!-- 使用前面章节描述的方法，子资源可以通过使用缓存破坏来缓存很长时间，但主资源（通常是HTML文档）不能。 -->

缓存主资源很困难，因为仅使用 HTTP 缓存规范中的标准指令，服务器更新内容时无法主动删除缓存内容。

<!-- 但是，可以通过部署托管缓存（例如 CDN 或 service worker）来实现。 -->

例如，允许通过 API 或仪表板操作清除缓存的 CDN 将通过存储主资源并仅在服务器上发生更新时显式清除相关缓存来实现更积极的缓存策略。

<!-- 如果 service worker 可以在服务器上发生更新时删除缓存 API 中的内容，它也可以这样做。

有关详细信息，请参阅您的 CDN 文档，并查阅 [service worker 文档](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API)。

## 也可以看看

* [RFC 9111：超文本传输​​协议 (HTTP/1.1)：缓存](https://datatracker.ietf.org/doc/html/RFC9111)
* [缓存教程 - Mark Nottingham](https://www.mnot.net/cache_docs/) -->


# dns-prefetch

## 最佳实践

一些资源（如字体）以匿名模式加载。在这种情况下，应使用预连接提示设置 [crossorigin](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Attributes/crossorigin) 属性。如果您省略它，则浏览器将仅执行 DNS 查找。


# Element.getClientRects()

## 语法

### 返回值

即使对于具有空 border-box 的 CSS 盒子，也会返回矩形。`left`、`top`、`right` 和 `bottom` 坐标仍然有意义。


# Element.getBoundingClientRect()

## 语法

### 返回值

空 border-box 被完全忽略。如果所有元素的 border-box 都是空的，则返回一个 `width` 和 `height` 为零的矩形，其中 `top` 和 `left` 是元素的第一个 CSS 盒子（按内容顺序）的 border-box 的左上角。

在计算边界矩形时，会考虑视口区域（或任何其他可滚动元素）的滚动量。这意味着每次滚动位置更改时 ，矩形的边界边缘（`top`、`right`、`bottom`、`left`）都会更改它们的值（因为它们的值是相对于 viewport 的而不是绝对的）。

如果您需要相对于 document 左上角的边界矩形，只需将当前滚动位置加到 `top` 和 `left` 属性（可以使用 [`window.scrollY`](https://developer.mozilla.org/en-US/docs/Web/API/Window/scrollY) 和 [`window.scrollX`](https://developer.mozilla.org/en-US/docs/Web/API/Window/scrollX) 获得）以获得与当前滚动位置无关的边界矩形。


# Object.defineProperty()

## 描述

### 描述符可拥有的键值

请记住，这些属性不一定是描述符自己的属性。继承的属性也将被考虑。为了确保保留这些默认值，您可以预先冻结描述符对象原型链中的现有对象，明确指定所有选项，或使用 `Object.create(null)` 指向 `null`。


# Object.freeze()

`Object.freeze()` 方法*冻结*一个对象。冻结对象可[防止扩展](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/preventExtensions)并使现有属性不可写和不可配置。无法再更改冻结的对象：无法添加新属性，无法删除现有属性，无法更改它们的可枚举性、可配置性、可写性或值，并且无法重新分配对象的原型。`freeze()`返回传入的同一个对象。


# 深入：微任务与 Javascript 运行时环境

## JavaScript 运行时

为了运行 JavaScript 代码，运行时引擎维护一组用于执行 JavaScript 代码的**代理**。每个代理由一组执行上下文、执行上下文栈、一个主线程、一组用于处理 worker 的任何附加线程、一个任务队列和一个微任务队列组成。除了一些浏览器在多个代理之间共享的主线程之外，一个代理的每个组件对于该代理都是唯一的。

### 事件循环（Event loops）

每个代理都由一个事件循环驱动，事件循环收集任何用户和其他事件，将任务排入队列以处理每个回调。然后它运行任何挂起的 JavaScript 任务，然后是任何挂起的微任务，然后执行任何需要的渲染和绘制，然后再次循环以检查挂起的任务。

您的网站或应用程序的代码在同一个[**线程**](https://developer.mozilla.org/zh-CN/docs/Glossary/Thread)中运行，共享同一个**事件循环**，如同 Web 浏览器本身的用户界面。这是[**主线程**](https://developer.mozilla.org/en-US/docs/Glossary/Main_thread)，除了运行站点的主要代码体之外，它还处理接收和调度用户和其他事件、渲染和绘制 Web 内容等。

* 窗口事件循环
  * 窗口事件循环是驱动所有共享相似来源的窗口的事件循环（尽管对此有进一步的限制，如下所述）。


---

***`//End of Article`***

---


![公众号二维码](https://neptliang.github.io/img/Article/WeChatBlog.png)