---
layout:       post
title:        "npm笔记"
subtitle:     "部分文档机翻"
date:         2020-12-04 22:24:00
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: true
tags:
    - FrontEnd
---


# 前言

发现npm没有中文文档，故机翻一手备查

---


# 关于公共npm registry

公共npm registry是JavaScript程序包的数据库，每个程序包均包含软件和metadata。开源开发人员和公司的开发人员使用npm registry为整个社区或其组织成员提供包，并下载包以在自己的项目中使用

---


# 关于包和模块

npm registry包含包，其中很多也是Node模块，或者包含Node模块

## 关于包

**包**是package.json文件描述的一个文件或目录。软件包必须包含package.json文件才能发布到npm registry

包对于用户或组织可以是unscoped（无范围）的或scoped（范围）的，并且scoped包可以是私有的也可以是公共的

### 关于包装格式

软件包是以下任意一种：

* a) 包含package.json文件描述的程序的文件夹
* b) 包含(a)的压缩包
* c) 解析到(b)的URL
* d) 用(c)发布在registry上的`<name>@<version>`
* e) 指向(d)的`<name>@<tag>`
* F) 具有latest标签满足(E)的`<name>`
* g) clone后得到(a)的gitURL

### npm包的git URL格式

用于npm包的Git URL可以通过以下方式格式化：

```
git://github.com/user/project.git#commit-ish
git+ssh://user@hostname:project.git#commit-ish
git+http://user@hostname/project/blah.git#commit-ish
git+https://user@hostname/project/blah.git#commit-ish
```

`commit-ish`可以是可以作为参数被提供给git checkout的任何标签、sha，或分支。默认commit-ish值为`master`

## 关于模块

模块是在node_modules目录中可以被Node.js的`require()`函数加载的任何文件或目录

要由Node.js `require()`函数加载，模块必须是以下之一：

* 具有package.json包含"`main`"字段的文件的文件夹
* 一个JavaScript文件

注意：由于不需要模块具有package.json文件，不是所有模块都是软件包。仅具有package.json文件也是包

在Node程序的上下文中，module也是从文件加载的内容。例如，在以下程序中：

```js
var req = require（'request'）
```

我们可能会说“变量`req`引用`request`模块”

---


# 本地下载和安装包

如果你想让自己的模块依赖于一个包，您可以本地安装这个包，使用像Node.js的`require`的东西。这是`npm install`的默认行为

## 安装unscoped（无作用域的？）包

Unscoped包始终是公共的，这意味着任何人都可以搜索、下载和安装它们。要安装公共包，请在命令行上运行

```sh
npm install <package_name>
```

这将在您当前的目录中创建node_modules目录（如果尚不存在），并将包下载到该目录

**注意**：如果本地目录中没有package.json文件，则安装包的最新版本
如果有package.json文件，npm将安装package.json中声明的满足semver规则（semantic versioning rule，语义化版本控制规则）的最新版本

## 安装scoped（有作用域的？）公共包

只要在安装过程中引用了scope（作用域？）名称，任何人都可以下载并安装Scoped公共包：

```sh
npm install @scope/package-name
```

## 安装私有包

私有包只能由被授予对该包读取权限的人员下载和安装。由于私有包始终是scoped的（作用域的？），因此在安装过程中必须引用scope名称：

```sh
npm install @scope/private-package-name
```

## 测试包安装

为确认npm install工作正常，请在您的模块目录中，检查node_modules目录是否存在，并且其中包含您安装的包的目录：

```sh
ls node_modules
```

## 安装的包的版本

如果运行npm install的目录中有一个package.json文件，则npm安装package.json中满足语义化版本控制规则声明的包的最新版本

如果没有package.json文件，则安装该包的最新版本

## 安装带有dist-tags的包

与npm publish一样，默认情况下`npm install <package_name>`会使用`latest`标记

要覆盖此行为，请使用`npm install <package_name>@<tag>`。例如，要安装标记为`beta`的版本的example-package，您可以运行以下命令：

```sh
npm install example-package@beta
```

# 全局下载和安装包

**提示**：如果您使用的是npm 5.2或更高版本，建议使用npx全局运行包

全局安装包可让您将包中的代码用作本地计算机上的一组工具

要全局下载并安装包，请在命令行上运行以下命令：

```sh
npm install -g <package_name>
```

如果收到EACCES权限错误，则可能需要使用版本管理器重新安装npm或手动更改npm的默认目录


*//End of Article*

**参考文献**：

* [seceditor@MottoIN《WAF攻防研究之四个层次Bypass WAF》（mottoin.com/detail/359.html）](http://www.mottoin.com/detail/359.html)

![公众号二维码](https://neptliang.github.io/img/Article/WeChatBlog.png)