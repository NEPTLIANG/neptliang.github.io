---
layout:       post
title:        "Vue Cli 实践"
subtitle:     "undefined"
date:         2023-11-01 15:00:12
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - FrontEnd
---


# `0x01` 路由配置

**src/router/index.js**(`export const constantRoutes`):

```JS
{
    path: '/path/in/url',
    component: () =>
        import('@/views/page-file-name')
},
```


# `0x02` 命名插槽与参数传递

```HTML
<template v-slot:name="param">
    {{ param ? 'true' : 'false' }}
</template>
```


# `0x03` node 与页面共用值

.env / .env.development、.env.production 配置环境变量，只有以 `VUE_APP_` 开头才能被页面读到


# `0x04` 跨域代理

## 开发环境

**vue.config.js**(`module.exports.devServer.proxy`):

```JS
'api/path/base': {
    target: 'https://api.server.site',
    ws: true,   //代理 Web Socket
    changeOrigin: true      // 必须配置为true，启用代理
}
```

[文档：https://cli.vuejs.org/zh/config/#devserver-proxy](https://cli.vuejs.org/zh/config/#devserver-proxy)

页面中发送对应路径的请求就会被代理转发到目标服务器

```JS
axios({
    method: 'GET',
    baseURL: '/api/path/base',
    url: '/concrete/path'
})      //会被转发到 https://api.server.site/api/path/base/concrete/path
```

## 生产环境

nginx 实现，nginx 安装目录（往往是/usr/local/nginx/）下，**conf/nginx.conf**(`http`-`server`):

```conf
        location /api/path/base {
            proxy_pass https://api.server.site;
        }
```

改了之后`./sbin/nginx -s reload`


# `0x05` 常见问题

## 1. “[Vue warn]: Error in v-on handler: "TypeError: Object(...) is not a function"”

有可能是回调里的代码有问题，比如把 `import { something } from 'module'` 写成了 `import something from 'module'`

## 2. Cannot read property ‘upgrade’ of undefined

检查 vue.config.js 中 `module.exports.devServer.proxy` 是否有 `target` 为空字符串的配置

## 3. `ERR_OSSL_EVP_UNSUPPORTED`

尝试使用 Node.js 17 之前的版本

<!-- # `<a-modal/>`

* `destroy-on-cloes`
* `mask-closable`
* `@cancel`


# `<a-input/>`

* `v-decorator`
    * `id`
    * `options`
        * `initialValue`
* `disabled`
* `placeholder` -->


---

***`//End of Article`***

---


![公众号二维码](https://neptliang.github.io/img/Article/WeChatBlog.png)