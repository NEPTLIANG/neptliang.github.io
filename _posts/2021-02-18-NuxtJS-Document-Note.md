---
layout:       post
title:        "NUXT.JS文档机翻整理"
subtitle:     "null"
date:         2021-02-18 15:46:54
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - FrontEnd
---


# 开始使用

## 安装

### 先决条件

* node——至少为`v10.13`，*建议您安装最新版本的LTS*。
* 文本编辑器，我们建议使用带有Vetur扩展的VS Code或WebStorm
* 终端，我们建议使用VS Code的集成终端或WebStorm终端。

### 使用`create-nuxt-app`

要快速入门，可以使用`create-nuxt-app`。

确保已安装`npx`（自npm 5.2.0起默认安装`npx`）或npm v6.1或yarn。

```sh
yarn create nuxt-app <project-name>
```

它将询问您一些问题（名称，Nuxt选项，UI框架，TypeScript，linter，测试框架等）。要了解有关所有选项的更多信息，请参阅[Create Nuxt应用程序](https://github.com/nuxt/create-nuxt-app/blob/master/README.md)。

回答完所有问题后，它将安装所有依赖项。下一步是导航到项目文件夹并启动它：

```bash
cd <project-name>
yarn dev
```

该应用程序现在在[http://localhost:3000](http://localhost:3000)上运行。做得好！

> Nuxt.js入门的另一种方法是使用CodeSandbox，这是快速使用Nuxt.js和/或与他人共享代码的好方法。

### 手动安装

从头开始创建Nuxt.js项目仅需要一个文件和一个目录。

我们将使用终端创建目录和文件，并使用您选择的编辑器随意创建它们。

#### 设置您的项目

使用项目名称创建一个空目录，然后进入其中：

```shell
mkdir <project-name>
cd <project-name>
```

用项目名称替换`<project-name>`。

创建`package.json`文件：

```sh
touch package.json
```

用以下内容填充您的`package.json`：

```json
{
  "name": "my-app",
  "scripts": {
    "dev": "nuxt",
    "build": "nuxt build",
    "generate": "nuxt generate",
    "start": "nuxt start"
  }
}
```

`scripts`定义将用命令`npm run <command>`或`yarn <command>`启动的Nuxt.js命令。

**什么是`package.json`文件？**

`package.json`类似于你的项目的身份证。它包含所有项目依赖项以及更多内容。如果您不知道`package.json`文件是什么，我们强烈建议您快速阅读[`npm`文档](https://docs.npmjs.com/creating-a-package-json-file)。

#### 安装Nuxt

`package.json`被创建后，像下面这样通过`npm`或`yarn`添加`nuxt`到您的项目：

```sh
yarn add nuxt
```

此命令将`nuxt`作为依赖项添加到您的项目中，并将其添加到您的`package.json`中。`node_modules`目录还将被创建，在此目录中将存储所有已安装的软件包和依赖项。

> 还会创建一个`yarn.lock`或`package-lock.json`，以确保项目中安装的软件包的一致安装和兼容依赖性。

#### 创建您的第一个页面

Nuxt.js将`pages`目录中的每个`*.vue`文件转换为应用程序的路由。

在您的项目中创建`pages`目录：

```sh
mkdir pages
```

然后，在`pages`目录中创建一个`index.vue`文件：

```sh
touch pages/index.vue
```

重要的是此页面须被命名为`index.vue`，因为这将是打开应用程序时Nuxt显示的默认主页。

在编辑器中打开`index.vue`文件并添加以下内容：

```vue
<template>
  <h1>Hello world!</h1>
</template>
```

#### 启动项目

通过在终端中键入以下命令之一来运行项目：

```sh
yarn dev
```

> 在开发模式下运行应用程序时，我们使用`dev`命令。

该应用程序现在运行在[http://localhost:3000](http://localhost:3000)上。

通过在终端中单击链接在浏览器中打开它，您应该看到我们在上一步中复制的文本“Hello World”。

> 在开发模式下启动Nuxt.js时，它将侦听大多数目录中的文件更改，因此在添加新页面时无需重新启动应用程序

> 当您运行`dev`命令时，它将创建.nuxt文件夹。此文件夹应在版本控制中忽略。您可以通过在根级别创建.gitignore文件并添加.nuxt来忽略文件。


## 路由

### 自动路线

大多数网站将有多个页面（即主页，关于页面，联系页面等）。为了显示这些页面，我们需要一个Router。`vue-router`就是这样。使用Vue应用程序时，您必须设置一个配置文件（即`router.js`），然后将所有路由手动添加到该文件中。Nuxt.js根据`pages`目录中提供的Vue文件自动为您生成`vue-router`配置。这意味着您无需再编写路由器配置！Nuxt.js还为您提供了所有路由的自动代码拆分功能。

换句话说，在应用程序中进行路由所需要做的就是在`pages`文件夹中创建`.vue`文件。

> 了解有关[路由](https://nuxtjs.org/docs/2.x/features/file-system-routing)的更多信息

### 导航

要在应用程序的页面之间导航，应使用NuxtLink组件。该组件包含在Nuxt.js中，因此您不必像其他组件那样导入它。它与HTML`<a>`标记相似，不同之处在于，我们使用`to="/about"`而不是使用`href="/about"`。如果您以前使用过`vue-router`，可以将`<NuxtLink>`视为`<RouterLink>`的替代品

指向`pages`文件夹中`index.vue`页面的简单链接：

```vue
<template>
  <NuxtLink to="/">Home page</NuxtLink>
</template>
```

对于您网站内页面的所有链接，请使用`<NuxtLink>`。如果您有指向其他网站的链接，则应使用`<a>`标签。参见以下示例：

```vue
<template>
  <main>
    <h1>Home page</h1>
    <NuxtLink to="/about">
      About (internal link that belongs to the Nuxt App)
    </NuxtLink>
    <a href="https://nuxtjs.org">External Link to another page</a>
  </main>
</template>
```

> 了解有关[NuxtLink组件](https://nuxtjs.org/docs/2.x/features/nuxt-components#the-nuxtlink-component)的更多信息。


## 目录结构

默认的Nuxt.js应用程序结构旨在为小型和大型应用程序提供一个很好的起点。您可以随意组织自己的应用程序，也可以在需要时创建其他目录。

让我们创建项目中尚不存在的目录和文件。

```sh
mkdir components assets static
touch nuxt.config.js
```

这些是我们构建Nuxt.js应用程序时使用的主要目录和文件。您将在下面找到它们各自的说明。

> 使用这些名称创建目录将启用Nuxt.js项目中的功能。

### 目录

#### pages目录

`pages`目录包含您的应用程序的视图和路由。正如您在上一章中学到的，Nuxt.js读取该目录中的所有`.vue`文件，并使用它们创建应用router。

> 了解有关[pages目录](https://nuxtjs.org/docs/2.x/directory-structure/pages)的更多信息

#### components目录

`components`目录是放置所有Vue.js组件的位置，这些组件随后将导入到页面中。

使用Nuxt.js，您可以创建组件并将其自动导入到.vue文件中，这意味着无需在脚本部分中手动导入它们。一旦将组件设置为true，Nuxt.js将为您扫描并自动导入这些文件。

> 了解有关[components目录](https://nuxtjs.org/docs/2.x/directory-structure/components)的更多信息

#### assets目录

`assets`目录包含您的未编译资源，例如样式，图像或字体。

> 了解有关[assets目录](https://nuxtjs.org/docs/2.x/directory-structure/assets)的更多信息

#### static目录

`static`目录直接映射到服务器根目录，并且包含必须保留其名称（例如`robots.txt`）*或*可能不会更改的文件（例如favicon）。

> 了解有关[static目录](https://nuxtjs.org/docs/2.x/directory-structure/static)的更多信息

#### nuxt.config.js文件

`nuxt.config.js`文件是Nuxt.js的单点配置。如果要添加模块或覆盖默认设置，则可以在此处应用更改。

> 了解有关[nuxt.config.js文件](https://nuxtjs.org/docs/2.x/directory-structure/nuxt-config)的更多信息

#### package.json文件

`package.json`文件包含应用程序的所有依赖关系和脚本。

### 有关项目结构的更多信息

还有更多有用的目录和文件，包括内容，布局，中间件，模块，插件和store。由于它们对于小型应用程序不是必需的，因此这里不介绍它们。

> 要详细了解所有目录，请随时阅读[“目录结构”](https://nuxtjs.org/docs/2.x/directory-structure/nuxt)。


## 命令与部署

Nuxt.js附带了一组有用的命令，用于开发和生产目的。

### 在package.json中使用

您应该在`package.json`中包含以下命令：

```json
"scripts": {
  "dev": "nuxt",
  "build": "nuxt build",
  "start": "nuxt start",
  "generate": "nuxt generate"
}
```

您可以通过`yarn <command>`或`npm run <command>`（例如：`yarn dev`/`npm run dev`）启动命令。

### 开发环境

要在开发模式下在[http://localhost:3000](http://localhost:3000)上以热更换模块启动Nuxt：

```sh
yarn dev
```

### 命令清单

您可以根据目标运行不同的命令：

#### 目标：`server`（默认值）

* **nuxt dev** - 启动开发服务器。
* **nuxt build** - 使用webpack构建和优化您的应用程序以进行生产。
* **nuxt start** - 启动生产服务器（运行`nuxt build`后）。将其用于诸如Heroku，Digital Ocean等的Node.js托管。

#### 目标： `static`

* **nuxt dev** - 启动开发服务器。
* **nuxt generate** - 构建应用程序（如果需要），将每个路由生成为HTML文件，并静态导出到`dist/`目录（用于静态托管）。
* **nuxt start** - 像静态托管一样处理`dist/`目录（Netlify，Vercel，Surge等），非常适合在部署之前进行测试。

### Webpack配置检查

您可以检查nuxt用于构建类似于[vue inspect](https://cli.vuejs.org/guide/webpack.html#inspecting-the-project-s-webpack-config)的项目的webpack配置。

* **nuxt webpack \[query...]**

**参数**：

* `--name`：要检查的捆绑包名称。（客户端，服务器，现代）
* `--dev`：检查webpack配置是否处于开发模式
* `--depth`：检查深度。默认为2以防止产生冗长的输出。
* `--no-colors`：禁用ANSI颜色（当TTY不可用或通过管道传输到文件时默认禁用）

**例子**：

* `nuxt webpack`
* `nuxt webpack devtool`
* `nuxt webpack resolve alias`
* `nuxt webpack module rules`
* `nuxt webpack module rules test=.jsx`
* `nuxt webpack module rules test=.pug oneOf use.0=raw`
* `nuxt webpack plugins constructor.name=WebpackBar options reporter`
* `nuxt webpack module rules loader=vue-`
* `nuxt webpack module rules "loader=.*-loader"`

### 生产部署

Nuxt.js允许您在服务器部署或静态部署之间进行选择。

#### 服务器部署

要部署SSR应用程序，我们使用`target: 'server'`，其中server是默认值。

```sh
yarn build
```

Nuxt.js将创建一个`.nuxt`目录，其中所有内容都可以部署在您的服务器托管上。

> 我们建议您将`.nuxt`放入`.npmignore`或`.gitignore`。

构建应用程序后，您可以使用`start`命令查看应用程序的生产版本。

```sh
yarn start
```

#### 静态部署（预渲染）

Nuxt.js使您能够在任何静态主机上托管您的Web应用程序。

要部署一个静态生成的网站，需要确保在`nuxt.config.js`里有`target: 'static'`（对于Nuxt> = 2.13）：

```js
export default {
  target: 'static'
}
```

```sh
yarn generate
```

Nuxt.js将创建一个`dist/`目录，里面的所有内容都可以部署在静态托管服务上。

从Nuxt v2.13开始，安装了搜寻器，当基于这些链接使用`nuxt generate`命令时，该搜寻器现在将对您的链接标签进行搜索并生成您的路由。

> **警告**：使用Nuxt <= v2.12：[API配置生成](https://nuxtjs.org/docs/2.x/configuration-glossary/configuration-generate)时，`generate`命令将忽略动态路由

> 使用`nuxt generate`生成Web应用程序时，给asyncData和fetch赋予的上下文将没有`req`和`res`。

**错误失败**

要在遇到页面错误时返回非零状态代码，并使CI/CD部署或构建失败，可以使用`--fail-on-error`参数。

```sh
yarn generate --fail-on-error
```

### 下一步是什么？

> 阅读我们的[常见问题](https://nuxtjs.org/faq)，以找到部署到常见主机的示例。


## 结论

恭喜你！现在，您已经创建了自己的第一个Nuxt.js应用程序，现在您可以将自己视为Nuxter了，但是要学习的东西太多了，使用Nuxt.js可以做的事情还很多。以下是一些建议：

> 查阅[概念](https://nuxtjs.org/docs/2.x/concepts/views)

> 使用[asyncData](https://nuxtjs.org/docs/2.x/features/data-fetching#async-data)

> 在不同的[渲染模式](https://nuxtjs.org/docs/2.x/features/rendering-modes)之间进行选择

> 到目前为止，您喜欢Nuxt.js吗？不要忘记在GitHub上[为我们的项目加注星标](https://github.com/nuxt/nuxt.js)


## 升级

*升级Nuxt.js很快，但是比更新package.json更复杂*

如果要升级到Nuxt v2.14，并且想使用静态托管，则需要将`target:static`添加到nuxt.config.js文件中，以使generate命令正常工作。

```js
export default {
  target: 'static'
}
```

### 入门

1. 查看发行说明中要升级的版本，以了解该特定版本是否还有其他说明。
2. 更新`nuxt`包的版本为`package.json`文件中指定的版本。

完成此步骤后，说明会有所不同，具体取决于您使用的是Yarn还是npm。*Yarn是使用Nuxt时的首选软件包管理器，因为它是针对测试编写的开发工具。*

### Yarn

3. 删除`yarn.lock`文件
4. 删除`node_modules`目录
5. 运行`yarn`命令
6. 安装完成并运行测试后，请考虑也升级其他依赖项。可以使用`yarn outdated`命令。

### npm

3. 删除`package-lock.json`文件
4. 删除`node_modules`目录
5. 运行`npm install`命令
6. 安装完成并运行测试后，请考虑也升级其他依赖项。可以使用`npm outdated`命令。


# 概念

## 视图

“视图”部分描述了在Nuxt.js应用程序中为特定路由配置数据和视图所需的所有知识。视图由应用模板，布局和实际页面组成。此外，您可以为每页的首页定义自定义元标签，这对于SEO（搜索引擎优化）很重要。

![Nuxt.js中视图的组成](https://d33wubrfki0l68.cloudfront.net/ea81946f531a74084921fd77710afc3aca28ceb9/6a4c2/docs/2.x/views.png)
Nuxt.js中视图的组成

### 页面

每个Page组件都是Vue组件，但是Nuxt.js添加了特殊的属性和功能，以使您的应用程序的开发尽可能容易。

```vue
<template>
  <h1 class="red">Hello World</h1>
</template>

<script>
  export default {
    head() {
      // Set Meta Tags for this Page
    }
    // ...
  }
</script>

<style>
  .red {
    color: red;
  }
</style>
```

### 页面组件的属性

页面组件有许多属性，例如上例中的head属性。

> 请参阅[“目录结构”](https://nuxtjs.org/docs/2.x/directory-structure/nuxt)，以了解有关可以在页面上使用的所有属性的更多信息

### 布局

当您想要更改Nuxt.js应用的外观时，布局很有用。例如，您想要加一个侧边栏或在移动设备和桌面设备上有不同的布局。

#### 默认布局

您可以通过在layouts目录中添加`default.vue`文件来定义默认布局。这将用于所有未指定布局的页面。您需要包含在布局中的唯一一个玩意就是渲染page component的`<Nuxt />`组件。

```vue
<template>
  <Nuxt />
</template>
```

> 在组件一章中了解有关[Nuxt组件](https://nuxtjs.org/docs/2.x/features/nuxt-components)的更多信息

#### 自定义布局

您可以通过将`.vue`文件添加到layouts目录来创建自定义布局。为了使用自定义布局，您需要在要使用该布局的page component中设置`layout`属性。其值将是您创建的自定义布局的名称。

要创建一个blog布局，请添加一个`blog.vue`文件到您的layouts目录`layouts/blog.vue`：

```vue
<template>
  <div>
    <div>My blog navigation bar here</div>
    <Nuxt />
  </div>
</template>
```

> ⚠ 创建布局以实际包含页面组件时，请确保添加`<Nuxt/>`组件。

然后，在我们要使用该布局的页面中，使用值为`'blog'`的`layout`属性。

```vue
<template>
  <!-- Your template -->
</template>
<script>
  export default {
    layout: 'blog'
    // page component definitions
  }
</script>
```

如果未在页面上添加布局属性，例如`layout: 'blog'`，则将使用`default.vue`布局。

### 错误页面

错误页面是在发生错误时始终显示的*page component*（在服务端渲染时不会显示）。

> ⚠ 尽管此文件位于 layouts 文件夹中，但应将其视为页面。

如上所述，这种布局是特殊的，因为您不应将`<Nuxt/>`组件包含在其`template`中。您必须将此布局视为发生错误（`404`， `500`等）时显示的组件。与其他页面组件类似，您也可以按照常规方式为错误页面设置自定义布局。

您可以通过添加`layouts/error.vue`文件来自定义错误页面：

```vue
<template>
  <div>
    <h1 v-if="error.statusCode === 404">Page not found</h1>
    <h1 v-else>An error occurred</h1>
    <NuxtLink to="/">Home page</NuxtLink>
  </div>
</template>

<script>
  export default {
    props: ['error'],
    layout: 'error' // you can set a custom layout for the error page
  }
</script>
```

### 文件：App.html

应用模板用于为Nuxt.js应用程序创建document的实际HTML框架，该框架注入内容以及`head`和`body`的变量。该文件是为您自动创建的，通常很少需要修改。您可以通过在项目的源目录（默认情况下是根目录）中创建`app.html`文件来自定义Nuxt.js使用的HTML应用模板，以包含脚本或条件CSS类。

Nuxt.js使用的默认模板是：

```html
<!DOCTYPE html>
<html {{ HTML_ATTRS }}>
  <head {{ HEAD_ATTRS }}>
    {{ HEAD }}
  </head>
  <body {{ BODY_ATTRS }}>
    {{ APP }}
  </body>
</html>
```

使用自定义应用模板的一个用例是为IE添加条件CSS类：

```html
<!DOCTYPE html>
<!--[if IE 9]><html class="lt-ie9 ie9" {{ HTML_ATTRS }}><![endif]-->
<!--[if (gt IE 9)|!(IE)]><!--><html {{ HTML_ATTRS }}><!--<![endif]-->
  <head {{ HEAD_ATTRS }}>
    {{ HEAD }}
  </head>
  <body {{ BODY_ATTRS }}>
    {{ APP }}
  </body>
</html>
```

> 虽然您可以在`app.html`中添加JavaScript和CSS文件，但建议您使用`nuxt.config.js`完成这些任务！

---

***`//End of Article`***

---


# 参考文献

* [nuxtjs.org](https://nuxtjs.org/)


![公众号二维码](https://neptliang.github.io/img/Article/WeChatBlog.png)