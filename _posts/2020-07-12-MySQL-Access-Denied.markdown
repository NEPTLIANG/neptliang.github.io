---
layout:       post
title:        "通过旧方式修改MySQL密码导致Access denied抢救思路"
subtitle:     "ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: YES)"
date:         2020-07-12 ‏‎22:14:30
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: true
tags:
    - MySQL
---


# 0x00 前言

之前想改MySQL密码，上网搜索了一下，找到一篇教程[《MySQL修改密码》（cnblogs.com/mrhonest/p/10881646.html)](https://www.cnblogs.com/mrhonest/p/10881646.html)，内容摘要如下：

> 第二种方式：
> 方法1： 用SET PASSWORD命令   
> 首先登录MySQL。  
> 格式：mysql> set password for 用户名@localhost = password('新密码');  
> 例子：mysql> set password for root@localhost = password('123');  

执行之后报错，大意是函数```password```不存在，于是继续搜索，遂发现菜鸟教程[《启动及关闭 MySQL 服务器》（unoob.com/mysql/mysql-administration.html）](https://www.runoob.com/mysql/mysql-administration.html)中介绍如下：

> 注意：password() 加密函数已经在 8.0.11 中移除了，可以使用 MD5() 函数代替。

于是我一通操作

```sql
mysql> set password for 用户名@localhost = MD5('新密码');
```

执行成功，然而过了一会重新登陆的时候，哦豁：```ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: YES)```

接下来就是各种搜索，各种尝试，老半天后才终于解决，在这里记录一下临时修复思路（大致）

# 0x01 ```skip-grant-tables```

```sh
vim /etc/my.cnf  #注：windows下修改的是my.ini
```

在```[mysqld]```后面任意一行添加```skip-grant-tables```用来跳过密码验证的过程，保存文档并退出

接下来我们需要重启MySQL：

```sh
/etc/init.d/mysql restart  #有些用户可能需要使用/etc/init.d/mysqld restart
```

重启之后执行mysql即可进入mysql。

# 0x02 修改新密码

从MySQL的官方文档可以知道（可见学好嘤语的重要性（流泪）），修改密码的正确方式如下：

> **注意**
> 
> 帐户更改（包括分配密码）的首选语句 不是使用SET PASSWORD 分配密码，而是使用ALTER USER该语句。例如：
> 
> ```sql
> ALTER USER user IDENTIFIED BY 'auth_string';
> ```

但是这时候会报错：```ERROR 1290 (HY000): The MySQL server is running with the --skip-grant-tables option so it cannot execute this statemen```

这时候应该执行

```sql
flush privileges;
```

这个命令执行后会重新载入授权表，就可以修改密码了

# 0x03 去除```skip-grant-tables```

编辑my.cnf,去掉刚才添加的```skip-grant-tables```，然后重启MySQL

# 0x04 后注

windows平台去安装目录下找my.ini

# 0x05 期末作业开发过程中的一些其他小问题记录

## 1. 正则匹配串口数据的时候报错```cannot use a string pattern on a bytes-like```

py中用正则匹配读取串口返回的数据的时候：

```python
res = ser.readline()
    if re.match("^\$GPGLL", res):
        print(res)
```

报错：```TypeError: cannot use a string pattern on a bytes-like```

看到zhisheng_blog@CSDN的博文[《_compile(pattern, flags).findall(string) TypeError: cannot use a string pattern on a bytes-like》（blog.csdn.net/tzs_1041218129/article/details/52228905）](https://blog.csdn.net/tzs_1041218129/article/details/52228905)可知：

> 出错的主要原因是py3的urlopen返回的不是string是bytes
> 
> 解决方法是把’html’类型调整一下：html.decode(‘utf-8’)

串口读出的数据同理，在```readline()```后加上```.decode('utf-8')```即可

## 2. 解决终端SSH连接服务器一段时间不操作之后卡死的问题

参考[@一个潘达《解决终端SSH连接服务器一段时间不操作之后卡死的问题》（cnblogs.com/pandawan/p/9893364.html）](https://www.cnblogs.com/pandawan/p/9893364.html)可知，

卡死是因为LIUNX安全设置问题，在一段时间内没有使用数据的情况下会自动断开，解决方法就是让本地或者服务器隔一段时间发送一个请求给对方即可

在本地打开配置文件（不建议在server端设置）

```sh
sudo vim /etc/ssh/ssh_config
```
添加以下参数，如果有直接修改

```
ServerAliveInterval 50 #每隔50秒就向服务器发送一个请求
ServerAliveCountMax 3  #允许超时的次数，一般都会响应
```
修改完之后重启一下ssh服务

```sh
sudo /etc/init.d/ssh restart
```

# 0x06 参考文献：

1. _[@古木堂《重置密码解决MySQL for Linux错误 ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: YES)》（cnblogs.com/gumuzi/p/5711495.html）](https://www.cnblogs.com/gumuzi/p/5711495.html)_

2. _[《ERROR 1290 (HY000): The MySQL server is running with the --skip-grant-tables option so it cannot execute this statemen》（cnblogs.com/iosdev/archive/2013/07/15/3190431.html）](https://www.cnblogs.com/iosdev/archive/2013/07/15/3190431.html)_

3. _[《MySQL 8.0 Reference Manual》13.7.1.10 SET PASSWORD Statement（dev.mysql.com/doc/refman/8.0/en/set-password.html）](https://dev.mysql.com/doc/refman/8.0/en/set-password.html)_

_//未完待xu_