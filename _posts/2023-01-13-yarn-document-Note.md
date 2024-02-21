---
layout:       post
title:        "Yarn 文档节选机翻整理"
subtitle:     "undefined"
date:         2023-01-13 10:33:45
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - FrontEnd
---


# 入门

## 安装

### 安装核心包

管理 Yarn 的首选方法是通过[Corepack](https://nodejs.org/dist/latest/docs/api/corepack.html)，这是从 16.10 开始随所有 Node.js 版本一起提供的新二进制文件。它充当你和 Yarn 之间的中介，让你可以在多个项目中使用不同的包管理器版本，而无需再签入 Yarn 二进制文件。

#### Node.js >=16.10

Corepack 默认包含在所有 Node.js 安装中，但目前是可选的。要启用它，请运行以下命令：

```sh
corepack enable
```

#### Node.js <16.10

在 16.10 之前的版本中，Corepack 不包含在 Node.js 中；要解决这个问题，请运行：

```sh
npm i -g corepack
```

### 更新全局 Yarn 版本

#### Node.js ^16.17 或 >=18.6

```sh
corepack prepare yarn@stable --activate
```

#### Node.js <16.17 或 <18.6

查看[最新的 Yarn 版本](https://github.com/yarnpkg/berry/releases/latest)，记下版本号，然后运行：

```sh
corepack prepare yarn@<version> --activate
```

### 初始化你的项目

只需运行以下命令。它会在你的当前目录中生成一些文件；将它们全部添加到您的下一次提交中，您就完成了！

```sh
yarn init -2
```

> **注意**：默认情况下，`yarn init -2` 会将您的项目设置为与[Zero-Installs](https://yarnpkg.com/features/zero-installs)兼容，这需要在存储库中签入缓存；如果你想禁用它，请检查你的 [`.gitignore`](https://yarnpkg.com/getting-started/qa#which-files-should-be-gitignored)。

> **注意**：如果您从 Yarn 1.x 迁移并遇到障碍，您可能需要查看我们的[迁移指南](https://yarnpkg.com/getting-started/migration)。它并不总是需要的，但是关于如何解决过渡中可能出现的问题的相当全面的资源。

### 更新到最新版本

任何时候你想将 Yarn 更新到最新版本，只需运行：

```sh
yarn set version stable
```

然后 Yarn 将配置您的项目以使用最新的稳定二进制文件。在提交结果之前，不要忘记运行新安装来更新您的工件！

### 从 master 安装最新版本

有时即使是最新的版本也不够，然后你会想尝试最新的 master 分支来检查错误是否已修复。这变得非常简单！只需运行以下命令：

```sh
yarn set version from sources
```

同样，可以使用`--branch`标志安装特定的 PR：

```sh
yarn set version from sources --branch 1211
```

## 用法

现在你已经[安装](https://yarnpkg.com/getting-started/install)了 Yarn ，你可以开始使用它了！以下是您需要的一些最常用的命令。

> **从 Yarn 1 迁移**
> 
> 在下列[迁移指南](https://yarnpkg.com/getting-started/migration)中从 Yarn 1 移植时，我们一直在编写有用的建议。如果您看到尚未涵盖的内容，请看一看并为它做出贡献！确保查阅 [PnP 兼容性表](https://yarnpkg.com/features/pnp#compatibility-table)并在需要时[启用node-modules插件](https://yarnpkg.com/getting-started/migration#if-required-enable-the-node-modules-plugin)！

### 访问命令列表

```sh
yarn help
```

### 开始一个新项目

```sh
yarn init
```

### 安装所有依赖项

```sh
yarn
yarn install
```

### 添加依赖项

```sh
yarn add [package]
yarn add [package]@[version]
yarn add [package]@[tag]
```

### 为不同类别的依赖项添加依赖项

```sh
yarn add [package] --dev  # dev dependencies
yarn add [package] --peer # peer dependencies
```

### 升级依赖

```sh
yarn up [package]
yarn up [package]@[version]
yarn up [package]@[tag]
```

### 删除依赖项

```sh
yarn remove [package]
```

### 升级 Yarn 本身

```sh
yarn set version latest
yarn set version from sources
```


# 命令行界面

<!-- ## yarn init

创建一个新包。

### 用法

```sh
$> yarn init
```

### 例子

在本地目录中创建一个新包：

```sh
yarn init
```

在本地目录中创建一个新的私有包：

```sh
yarn init -p
```

创建一个新包并将 Yarn 版本存储在其中：

```sh
yarn init -i=latest
```

创建一个新的私有包并将其定义为工作区根目录：

```sh
yarn init -w
```

### 选项

定义 | 描述
----|-----
`-p,--private` | 初始化私有包
`-w,--workspace` | 使用`packages/`目录初始化工作区根目录
`-i,--install` | 使用将锁定在项目中的特定包初始化包

### 细节

此命令将在您的本地目录中设置一个新包。

如果设置了`-p,--private`或`-w,--workspace`选项，包将默认为私有。

如果设置了 `-w,--workspace` 选项，包将被配置为接受 `packages/` 目录中的一组工作空间。

如果 `-i,--install` 选项被赋予一个值，Yarn 将首先使用 `yarn set version` 下载它，然后才将 init 调用转发到新下载的包。如果没有参数，下载的包将是 `latest`。

可以使用 `initScope` 和 `initFields` 配置值更改清单的初始设置。此外，Yarn 将生成一个 EditorConfig 文件，其规则可以通过 `initEditorConfig` 更改，并将在当前目录中初始化一个 Git 存储库。 -->

## `yarn run`

运行 package.json 中定义的脚本。

### 用法

```sh
$> yarn run <scriptName> ...
```

### 例子

从本地工作区运行测试：

```sh
yarn run test
```

同样的事情，但没有“run”关键字：

```sh
yarn test
```

在运行时检查 Webpack：

```sh
yarn run --inspect-brk webpack
```

### 选项

定义 | 描述
----|-----
`--inspect` | 执行二进制文件时转发到底层 Node 进程
`--inspect-brk` | 执行二进制文件时转发到底层 Node 进程
`-T,--top-level` | 检查脚本和/或二进制文件的根工作区而不是当前的
`-B,--binaries-only` | 忽略任何用户定义的脚本，只检查二进制文件

### 细节

此命令将运行一个工具。将被执行的确切工具将取决于工作区的当前状态：

* 如果本地 package.json 中的`scripts`字段包含匹配的脚本名称，则将执行其定义。

* 否则，如果本地工作区的依赖项之一暴露了具有匹配名称的二进制文件，则该二进制文件将被执行。

* 否则，如果指定的名称包含冒号字符，并且项目中的一个工作区恰好包含一个具有匹配名称的脚本，则将执行该脚本。

无论发生什么，衍生进程的 cwd 将是声明脚本的工作区（这使得使用第三种语法跨工作区调用命令成为可能）。


---

***`//End of Article`***

---


![公众号二维码](https://neptliang.github.io/img/Article/WeChatBlog.png)