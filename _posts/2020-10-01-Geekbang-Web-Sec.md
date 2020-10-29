---
layout:       post
title:        "极客时间Web安全笔记"
subtitle:     "null"
date:         2020-10-27 02:49:54
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: true
tags:
    - Sec
    - WebSec
---


# 12. 文件上传初阶：后缀名绕过 & 原理探究

apache的php模块配置文件中：

```xml
<FilesMatch ".+\.ph(p[345]?|t|tml)$">
    SetHandler application/x-httpd-php
</FileMatch>
```

**php, php3, php4, php5, pht, phtml**后缀文件都会被当成php脚本解析

* *很多防火墙检测木马的时候针对特征检测，`eval`是很明显的特征*
* *常用到php中的`get_current_user`函数（获取当前用户名称）和`getcwd`函数（获取当前路径）*

---


# 13. 文件上传中阶：前端验证绕过、.htaccess绕过、大小写绕过

## 一、iis 5.x/6.0解析漏洞

1. **.asp结尾的目录**下的任意文件都解析成asp文件

    **例**：domain/xx.asp/xx.jpg

2. 服务器默认不解析**分号以后**的内容

    **例**：domain/xx.asp;.jpg

windows下iis 5.x/6.0调用asp.dll解析文件，代码不够完善

## 二、nginx解析漏洞
**低版本**nginx中，url中有**不存在的文件**时，php默认向前解析

**例**：domain/xxx.jpg/1.php，1.php不存在时，xxx.jpg解析成php文件

* *exploit database查询最新漏洞*

## 三、apache 1.x/2.x解析漏洞

从右往左判断后缀，**跳过不可识别的后缀**，直到找到可识别的后缀，按照该可识别后缀进行解析

例：访问上传的shell.php.test，服务器会解析成php文件

## 四、前端验证绕过

前端验证减轻服务器负担，但前端输入不可信，很容易绕过

1. **改包**
2. 浏览器**禁止/删除js代码**

## 五、.htaccess绕过

分布式配置文件，使web server配置可以随文件夹不同而不同

前提是web server启用了对它的支持

可绕过**黑名单**过滤

**例**（针对Apache）：

1. **上传.htaccess文件**，内容为`AddType application/x-httpd-php .test`（将test文件解析成php文件）
2. 上传xxx.test
3. 浏览器访问xxx.test

## 六、大小写绕过
win下文件名大小写不敏感，linux下开发者可能也会把web server配置成url大小写不敏感

针对黑名单

**.php->.pHp**

---


# 14. 文件上传高阶：文件流绕过、字符串截断绕过、文件头检测绕过

## 一、windows文件流特性绕过
ntfs实现了**多文件流**特性，日常写文件时写的就是默认数据流，也可以通过手动指定数据流的方式（`test.txt::$data`）把内容写到文件中

**例**：在cmd中执行

1. 
    ```cmd
    echo 111 > test.txt:111.txt
    ```
    创建文件test.txt，指明其文件流为111.txt，文件流内容为111（直接打开test.txt可看到文件内容为空，`notepad test.txt:111.txt`可打开文件流并看到文件流内容为111）
2. 
    ```cmd
    echo test > test.txt
    ```
    打印test到文件test.txt中
3. 
    ```cmd
    echo 222 > test.txt::$data
    ```
    在test.txt的默认文件流中输入222（打开test.txt可看到文件内容变为222）

通过输入文件流的方式，使创建的文件所解析的数据流不是我们写入的数据流实现绕过

### win平台文件流特性和php结合

默认数据流背后的数据会被认为是数据流写入到文件，可绕过文件类型检测

## 二、`%00`截断绕过
`%00`是字符串中或文件数据流中的**结束符**的url编码，属于编码绕过，可绕过**白名单**

### (1) 截断路径

**例**：代码采用白名单校验，只允许上传图片格式文件，不好绕过
但后面保存时，进行了路径拼接，而路径又是从前端获取的，故可以在路径进行截断，上传一个内容为一句话木马的jpg文件，并将`../upload/a.php%00`传给路径参数

### (2) 截断文件名

**条件**：
1. 文件类型**符合白名单策略**
2. 上传后是可执行或者说是服务器**可解析**的文件

**例**：`shell.png%00.jpg` -> `shell.png`
1. 在bp中拦包
2. 在文件后缀名前插一个空格或者别的字符
3. 切换到hex选项卡
4. 将插入的字符的十六进制值改成`00`
5. forward

## 三、文件头检测绕过

常见文件头格式：
```
PNG: 98 50 4E 47
JPG: FF D8 FF DB
GIF: 47 49 46 38
```

绕过**文件内容**检测

文件内容检测不可直接传马，故把php代码插在**图片文件后面**

程序解析器遇到图片内容中无法解析的字符可能会停止解析，故**建议把图片头部后面的内容去掉**

## 四、总结

**绕过类型**：
1. `Content-Type`绕过
2. 前端绕过
3. 文件解析规则绕过
4. Windows环境特性绕过
5. 大小写/双写/点空格/文件头/条件竞争绕过

**操作系统**本身特性、**web server**配置不合理等都可能导致漏洞

---


# 22. SQL注入实战：判断SQL注入点&防御方式

*TIPS:带参数的动态网页且该网页访问了数据库，就有可能存在SQL注入*

## 一、注入点判断：单引号判断法

字符型整型都可

* *靶机：sqli-labs, xforburp.com, bwapp*

* *注不出数据的时候可尝试加`%`应对LIKE查询*

## 二、防御

### (1) 减少错误信息反馈
### (2) 对输入的特殊符号进行转义（黑名单机制）

`\x00`		`\n`	`\r`	`\`	`'`	`"`	`\x1a`

### (3) 对输入的特殊词组进行过滤（黑名单机制）（PHP的`mysql_escape_string`）

`and` `or` `union` `select` 空格

---


# 34 SQL注入实战：如何绕过WAF之混淆注入原理

大小写绕过

* *字段名也不是大小写敏感的*

---


# 35 SQL注入实战：如何绕过WAF之`union`、`where`、`limit`过滤绕过

## 一、`&&`和`||`绕过

用`&&`和`||`替代`and`和`or`

## 二、`union`过滤绕过

原来的 
```sql
union select user, password from users
```

替换成 
```sql
|| (select user from users where user_id=1) = 'admin'
```

这里`||`的使用类似于布尔盲注

## 三、`where`过滤绕过

原来的 
```sql
|| (select user from users where user_id=1) = 'admin'
```
替换成 
```sql
|| (select user from users limit 1,1) = 'admin'
```

## 四、`limit`过滤绕过

原来的 
```sql
|| (select user from users limit 1,1) = 'admin'
```

替换成 
```sql
|| (select min(user) from group by user_id having user_id=1) = 'admin'
```

通过`having`选择`group`并通过`min`从中取出单个user

---


# 36 SQL注入实战：如何绕过WAF之`group by`、`select`、单引号、`hex`、`unhex`、`substr`绕过

## 一、`group by`过滤绕过
原来的 
```sql
|| (select min(user from group by user_id having user_id=1) 'admin'
```

替换成 
```sql
|| select substr((select group_comcat(name)name from test), 1, 1) = 't'
```

## 二、`select`及单引号过滤绕过

原来的 
```sql
|| select substr((select group_concat(name)name from test), 1, 1) = 't'
```

替换成 
```sql
|| substr(name, 1, 1)=0x74
```
或 
```sql
|| substr(name, 1, 1)=unhex(74)
```

这里`||`的使用类似于布尔盲注，使用时可以不要`select`；

用**十六进制值**逐个字符进行判断就不需要引号了

## 三、`hex`、`unhex`及`substr`过滤绕过——`binary`

原来的 
```sql
|| substr(name, 1, 1) = unhex(74)
```

替换成 
```sql
|| binary(name) = 0x74657374
```

`binary`返回数据的十六进制值

---


# 37 SQL注入实战：如何绕过WAF之空格、等号、双写、双重编码绕过

## 一、空格过滤绕过

```sql
select/**/name/**/from/**/test;
```

或通过**URL编码**绕过WAF

## 二、等号过滤绕过

使用`like`关键字代替等号

## 三、双写绕过

## 四、双重编码绕过

有些WAF只解析一次URL编码后过滤，如果**编码两次**的话就可能会绕过WAF，并被后端执行

## 五、转义函数也不是万能的

## 六、总的来说，分为以下几种方式

1. SQL语句的同义变形体
2. 利用WAF规则设定的缺陷（如双写）
3. 利用技术链中间环节绕过（如编码）

![公众号二维码](https://neptliang.github.io/img/Article/WeChatBlog.png)