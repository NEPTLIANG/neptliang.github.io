---
layout:       post
title:        "Create React App + TS + VS Code 配置 SASS"
subtitle:     "undefined"
date:         2023-02-08 14:53:21
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - FrontEnd
---


# 配置

## 1. 安装 `sass`

参考[《Create React App 文档》添加 Sass 样式表章节（create-react-app.dev/docs/adding-a-sass-stylesheet）](https://create-react-app.dev/docs/adding-a-sass-stylesheet/)可知，要使用 Sass，首先**安装 `sass`**：

```sh
$ npm install sass
# or
$ yarn add sass
```


## 2. `d.ts` 文件

* 装完 `sass` 后 VS Code 里的 SCSS 模块导入语句会报错：

    > 找不到模块“./index.module.scss”或其相应的类型声明。 `ts(2307)`

    故还需添加 .d.ts 文件

参考[夜葬@segmentfault 在《搭建 React + Typescript 环境，使用cssModule的正确姿势是怎样的？》（segmentfault.com/q/1010000017979602）中的回答](https://segmentfault.com/q/1010000017979602)与[Null@segmentfault 在《为什么react+typescript加载scss或者css会报错，找不到模块》（segmentfault.com/q/1010000018446035）中的回答](https://segmentfault.com/q/1010000018446035)可知，可以在项目根目录或者 src 目录下**建立一个 typings/module.d.ts [*`[2]`*](#参考)，内容如下： [*`[3]`*](#参考)**

```TS
declare module '*.scss';
```

## 3. tsconfig.json

* 令人迷惑的是，加了 .d.ts 之后，如果把 VS Code 窗口中的 module.d.ts 文件关掉，VS Code 又会报上节所述错误，故还需添加 tsconfig.json

参考[夜葬@segmentfault 在《搭建 React + Typescript 环境，使用cssModule的正确姿势是怎样的？》（segmentfault.com/q/1010000017979602）中的回答](https://segmentfault.com/q/1010000017979602)可知，可以在项目根目录**建立一个 tsconfig.json，把上节建立的 typings 目录导入，参考内容如下： [*`[2]`*](#参考)**

```JSON with Comments
{
    "compilerOptions": {
        "typeRoots": ["./src/typings"],     //其实不加这行也可，只要有 tsconfig.json 且其中包含以下两行就行

        // 如果只创建 tsconfig.json 但没写入下面两行，也会报其他错误
        "jsx": "react",
        "esModuleInterop": true
    }
}
```

---


# 附：React 中使用 SASS 实践 [*`[4]`*](#参考)

my-component/**index.module.scss**:

```SCSS
// 根节点使用 CSSModules 形式的类名（驼峰命名）
.parentNode {
    //一般不这么定义，而是按照下方 :global 包裹的方式定义
    .classA {}      

    // 使用 :global 包裹其他子节点的类名，在 JSX 中使用时就可以用 className="title" 形式的类名
    // 如果不加 :global，就必须写成 className={styles.title} 形式
    :global {
        // 其他所有的子节点，都使用普通的 CSS 类名
        .class-b {}
        .class-c {}
    }
}
```

my-component/**index.tsx**:

```JSX
import styles from './index.module.scss';

const myComponent = () => (
    // 不被 :global 包裹的类名，须写成 styles.title 形式
    <div className={styles.parentNode} >
        <div className={styles.classA} />
        {/* 使用 :global 包裹的类名，可以用 className="title" 形式 */}
        <div className="class-b" />
        <div className="class-c" />
    </div>
);
```

---


# 参考

* [1]《Create React App 文档》添加 Sass 样式表章节[（create-react-app.dev/docs/adding-a-sass-stylesheet）](https://create-react-app.dev/docs/adding-a-sass-stylesheet/)

* [2] 夜葬@segmentfault [在《搭建 React + Typescript 环境，使用cssModule的正确姿势是怎样的？》（segmentfault.com/q/1010000017979602）中的回答](https://segmentfault.com/q/1010000017979602)

* [3] Null@segmentfault [在《为什么react+typescript加载scss或者css会报错，找不到模块》（segmentfault.com/q/1010000018446035）中的回答](https://segmentfault.com/q/1010000018446035)

* [4] 风妮@掘金《react中sass的使用，解决样式污染，样式穿透》[（juejin.cn/post/7031556713329197093）](https://juejin.cn/post/7031556713329197093)


---

***`//End of Article`***

---


![公众号二维码](https://neptliang.github.io/img/Article/WeChatBlog.png)