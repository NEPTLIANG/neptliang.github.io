---
layout:       post
title:        "MariaDB 安装配置问题总结"
subtitle:     "undefined"
date:         2024-03-08 11:12:34
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - Linux
---


# `0x00` 前言

*`/*` 照着 Arch 维基 MariaDB 文档安装配置 MariaDB 的时候遇到些问题，花了不少时间谷歌解决办法，故在此记录下 `*/`*

---


# `0x01` `version 'GLIBCXX_3.4.20' not found`

## 背景

最近比较忙，所用的 Arch Linux 很久没更新软件包了，装好 MariaDB 之后启动报错：

```
version `GLIBCXX_3.4.20' not found
```

谷歌了半天，直到看到 Agrover112@stack overflow [在《Where can I find GLIBCXX_3.4.29?》的回答（https://stackoverflow.com/a/70990697）](https://stackoverflow.com/a/70990697)

## 解决办法

**更新了 GCC**，就好了

---


# `0x02` `ERROR 1348 (HY000): Column 'Password' is not updatable`

## 背景

刚开始是用 Linux 非 `root` 用户执行的各条 shell 命令（官方文档里其实标了 shell 提示符是`#`），结果各种意料之外的情况：

1. 执行 3.1 Improve initial security 的 `mariadb-secure-installation` 要求先输入 MariaDB `root` 账户的密码，但是安装过程中并不曾设置密码

3. 按照 2.1 Add user 所述添加用户的时候，通过 `mariadb -u root` 登录又要求输入密码；直接跑 `mariadb` 虽然能自动登录上，但 `CREATE USER` 又报 `Access denied`

2. 使用 `mysqld_safe --skip-grant-tables &` 跳过授权登录之后，通过 `update mysql.user` 设置密码又报错：
    ```
    ERROR 1348 (HY000): Column 'Password' is not updatable
    ```

又谷歌了半天，直到看到 jernst@GitHub[《Column 'Password' is not updatable》（https://github.com/uboslinux/ubos-admin/issues/692#issuecomment-536024629）](https://github.com/uboslinux/ubos-admin/issues/692#issuecomment-536024629)才知道，默认情况下会创建 root 和拥有数据目录的操作系统用户（通常是 mysql）。如果你是系统root用户，你可以以root@locahost身份登录，无需密码。如果您在本地安装 MariaDB（例如从压缩包），您将不希望使用 sudo 来登录。这就是为什么 MariaDB 创建第二个全权用户，其名称与拥有数据目录的系统用户相同。在本地（不是系统范围）安装中，这将是安装 MariaDB 的用户。即使 MariaDB 是在系统范围内安装的，您可能不想以系统 root 身份运行数据库维护脚本——现在您可以以系统 mysql 用户身份运行它们。

## 解决办法

**改用 `sudo mariadb`** 登录后就可以 `CREATE USER` 了，`sudo mariadb -u root` 也可以直接登录，`sudo mariadb-secure-installation` 也不用先输入密码

虽然最后 `mariadb-secure-installation` 还是建议我们给 MariaDB 的 `root` 账户设置密码……

## 附：MariaDB 文档相应内容机翻整理

向所有人开放的万能root账户终于消失了。安装脚本将不再要求您“PLEASE REMEMBER TO SET A PASSWORD FOR THE MariaDB root USER !”，因为 root 帐户是自动安全地创建的。

默认情况下会创建两个万能帐户—— root 和拥有数据目录的操作系统用户（通常是 mysql）。它们如同这样被创建：

```SQL
CREATE USER root@localhost IDENTIFIED VIA unix_socket OR mysql_native_password USING 'invalid'
CREATE USER mysql@localhost IDENTIFIED VIA unix_socket OR mysql_native_password USING 'invalid'
```
         
使用unix_socket意味着如果你是系统root用户，你可以以root@locahost身份登录，无需密码。这项技术是由 Otto Kekäläinen 在 Debian MariaDB 软件包中首创的，并且早在MariaDB 10.0就已经[在 Debian 中成功使用](https://mariadb.com/kb/en/differences-in-mariadb-in-debian-and-ubuntu/)。

它基于一个简单的事实，即向系统 root 询问密码不会增加额外的安全性—— root 无论如何都可以完全访问所有数据文件和所有进程内存。但不要求密码意味着没有 root 密码可以忘记（不需要大量关于“如何重置 MariaDB root 密码”的教程）。而且如果你想编写一些繁琐的数据库工作的脚本，则不需要以纯文本形式存储 root 密码以供脚本使用（不需要 debian-sys-maint 用户）。

尽管如此，一些用户可能希望以 MariaDB root 身份登录而不使用 sudo。因此，旧的身份验证方法——传统的 MariaDB 密码——仍然可用。默认情况下它是禁用的（“invalid”不是有效的密码散列），但可以使用常用的SET PASSWORD语句设置密码。并且仍然保留通过 sudo 的无密码访问。

如果您在本地安装 MariaDB（例如从压缩包），您将不希望使用 sudo 来登录。这就是为什么 MariaDB 创建第二个全权用户，其名称与拥有数据目录的系统用户相同。在本地（不是系统范围）安装中，这将是安装 MariaDB 的用户——他们会自动获得方便的类似 root 的无密码访问权限，因为他们无论如何都可以访问所有数据文件。

即使 MariaDB 是在系统范围内安装的，您可能不想以系统 root 身份运行数据库维护脚本——现在您可以以系统 mysql 用户身份运行它们。而且您会知道，即使您在 shell 脚本中犯了拼写错误，它们也永远不会破坏您的整个系统。

然而，习惯了旧方式的经验丰富的 MariaDB DBA 确实需要做出一些改变。


# `0xff` 参考文献

1. Agrover112@stack overflow [在《Where can I find GLIBCXX_3.4.29?》的回答（https://stackoverflow.com/a/70990697）](https://stackoverflow.com/a/70990697)

1. MariaDB Server Documentation[《Authentication from MariaDB 10.4》（https://mariadb.com/kb/en/authentication-from-mariadb-10-4/）](https://mariadb.com/kb/en/authentication-from-mariadb-10-4/)

2. jernst@GitHub[《Column 'Password' is not updatable》（https://github.com/uboslinux/ubos-admin/issues/692#issuecomment-536024629）](https://github.com/uboslinux/ubos-admin/issues/692#issuecomment-536024629)


---

***`//End of Article`***

---


![公众号二维码](https://neptliang.github.io/img/Article/WeChatBlog.png)