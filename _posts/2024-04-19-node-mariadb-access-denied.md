---
layout:       post
title:        "Node 中以“second all-powerful user”不使用密码连接本地 MariaDB 时，报 Access denied 处理"
subtitle:     "得配 socketPath"
date:         2024-04-19 17:15:43
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - NodeJS
---


# 背景

在 Node 中以“second all-powerful user”**不使用密码**连接本地 MariaDB 时，报 `Access denied`，但是在终端里使用对应用户可以直接登录
* Node 中使用的是 `mariadb` 包
* MariaDB 服务器上该用户的**认证方式**结合文档和 Arch Linux Wiki 的建议配置成了

    ```SQL
    IDENTIFIED VIA mysql_native_password USING 'HASHOFPASSWORD' OR unix_socket
    ```

谷歌了好久，直到看到 ruiquelhas@stack overflow [在问题《Connection with mysql on remote server via UNIX socket》下的回答（https://stackoverflow.com/a/60395955/10838083）](https://stackoverflow.com/a/60395955/10838083)

才整明白原来还得配 `socketPath`，故在此记录下


# 解决办法

使用 unix_socket 连接时，可以在 `createPool` 时使用 `socketPath`：*[[1]](#参考文献)*

```JS
const pool = mariadb.createPool({
  socketPath: '/path/to/your/unix/socket/file.sock'
  user: 'fake_user',
});
```

您需要设置的具体 socket 路径由服务器系统变量 `socket` 定义。如果您不知道它，可以从服务器检索。*[[2]](#参考文献)*

```SQL
SHOW VARIABLES LIKE 'socket';
```


# 参考文献

1. ruiquelhas@stack overflow [在问题《Connection with mysql on remote server via UNIX socket》下的回答（https://stackoverflow.com/a/60395955/10838083）](https://stackoverflow.com/a/60395955/10838083)
2. npm mariadb Homepage README promise documentation [《Connecting to Local Databases》（https://github.com/mariadb-corporation/mariadb-connector-nodejs/blob/master/documentation/promise-api.md#connecting-to-local-databases）](https://github.com/mariadb-corporation/mariadb-connector-nodejs/blob/master/documentation/promise-api.md#connecting-to-local-databases)


---

***`//End of Article`***

---


![公众号二维码](https://neptliang.github.io/img/Article/WeChatBlog.png)