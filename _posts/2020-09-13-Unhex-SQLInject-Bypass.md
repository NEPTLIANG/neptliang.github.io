---
layout:       post
title:        "unhex在MySQL终端工具不可用的问题说明"
subtitle:     "null"
date:         2020-09-13 02:02:34
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: true
tags:
    - MySQL
    - SQLInject
    - Bypass
---


# 0x00 前言

FlappyPig的《CTF特训营》Web篇第2章2.10《SQL读写文件》中讲到

> 如果题目所考察的点不是通过SQL读取文件，则可以考虑是否能通过SQL语句进行写文件，包括但不限于**Webshell、计划任务等**

其中介绍的一个写文件的Payload如下：

```sql
union select unhex(一句话Shell的十六进制) into dumpfile'/var/www/html/shell.php'
```

但是在我用MySQL 8.0.20的cli客户端尝试的过程中，发现`unhex`输出的始终是原来的十六进制字符串：

```sql
mysql> select version();
+-----------+
| version() |
+-----------+
| 8.0.20    |
+-----------+
1 row in set (0.00 sec)

mysql> select hex('<?php eval($_GET[233]); ?>');
+------------------------------------------------------+
| hex('<?php eval($_GET[233]); ?>')                    |
+------------------------------------------------------+
| 3C3F706870206576616C28245F4745545B3233335D293B203F3E |
+------------------------------------------------------+
1 row in set (0.00 sec)

mysql> select unhex('3C3F706870206576616C28245F4745545B3233335D293B203F3E');
+------------------------------------------------------------------------------------------------------------------------------+
| unhex('3C3F706870206576616C28245F4745545B3233335D293B203F3E')                                                                |
+------------------------------------------------------------------------------------------------------------------------------+
| 0x3C3F706870206576616C28245F4745545B3233335D293B203F3E                                                                       |
+------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)
```

而在我Windows上的MySQL 5.0.22却可以：

```sql
mysql> select version();
+---------------------+
| version()           |
+---------------------+
| 5.0.22-community-nt |
+---------------------+
1 row in set (0.01 sec)

mysql> select unhex('3C3F706870206576616C28245F4745545B3233335D293B203F3E');
+---------------------------------------------------------------+
| unhex('3C3F706870206576616C28245F4745545B3233335D293B203F3E') |
+---------------------------------------------------------------+
| <?php eval($_GET[233]); ?>                                    |
+---------------------------------------------------------------+
1 row in set (0.00 sec)
```

这令我十分困惑，莆田搜索了老半天也没见到有人提到，本嘤语渣迫于无奈只好靠着谷歌翻译和有道词典去翻官方文档，现在记录一下以便日后翻看

---


# 0x01 MySQL 8.0 UNHEX(str) 官方文档机翻

在MySQL官网顶部搜索栏搜索unhex，可以找到官方文档《
Functions and Operators》章节中的[**12.8 String Functions and Operators** (dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_unhex)](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_unhex)，内容机翻如下：

> 对于字符串参数str，`UNHEX(str)`将参数中的每对字符解释为十六进制数字，并将其转换为该数字表示的字节。返回值是一个二进制字符串。
> 
> ```sql
> mysql> SELECT UNHEX('4D7953514C');
>         -> 'MySQL'
> mysql> SELECT X'4D7953514C';
>         -> 'MySQL'
> mysql> SELECT UNHEX(HEX('string'));
>         -> 'string'
> mysql> SELECT HEX(UNHEX('1267'));
>         -> '1267'
> ```
>
> 在参数字符串中的字符必须是合法的十六进制数字：'0'... '9'，'A'... 'F'，'a'... 'f'。如果参数包含任何非十六进制数字，则结果为`NULL`：
> 
> ```sql
> mysql> SELECT UNHEX('GG');
> +-------------+
> | UNHEX('GG') |
> +-------------+
> | NULL        |
> +-------------+
> ```
> 
> 如果`UNHEX()`的参数是`BINARY`字段，则可能出现`NULL`结果，因为值在存储时使用`0x00`字节填充，但**在取回时没有剥去这些字节**。例如，'41'作为'41'存储在`CHAR(3)`字段中，并以'41'的形式取回(去掉后面的填充)，因此该字段值的`UNHEX()`返回`X'41'`。相反，'41'作为`'41\0'`存储在`BINARY(3)`字段中，并以`'41\0'`的形式返回(后面的`0x00`字节填充没有被剥离)。`'\0'`不是一个合法的十六进制数字，因此该字段值的`UNHEX()`返回`NULL`。
> 
> 对于数值参数N，`HEX(N)`的逆操作**不是由`UNHEX()`执行的**。使用`CONV(HEX(N),16,10)`代替。

---


# 0x02 `--binary-as-hex`

继续翻搜索结果，发现下面有个官方文档《MySQL Programs》章节中[**4.5.1.1 mysql Client Options** (dev.mysql.com/doc/refman/8.0/en/mysql-command-options.html#option_mysql_binary-as-hex)](https://dev.mysql.com/doc/refman/8.0/en/mysql-command-options.html#option_mysql_binary-as-hex)，机翻如下：

> ## --binary-as-hex
> 
> 如果指定了此选项，则mysql将**使用十六进制表示法（`0xvalue`）显示二进制数据**。无论整体输出显示格式是tabular，vertical，HTML还是XML，都会发生这种情况。
> 
> `--binary-as-hex`启用后会影响所有二进制字符串的显示，**包括诸如`CHAR()`和`UNHEX()`等函数返回的二进制字符串**。下面的示例使用A的ASCII码（十进制65，十六进制41）来说明这一点：
> 
> `--binary-as-hex`禁用：
> 
> ```sql
> mysql> SELECT CHAR(0x41), UNHEX('41');
> +------------+-------------+
> | CHAR(0x41) | UNHEX('41') |
> +------------+-------------+
> | A          | A           |
> +------------+-------------+
> ```
> 
> `--binary-as-hex`已启用：
> 
> ```sql
> mysql> SELECT CHAR(0x41), UNHEX('41');
> +------------------------+--------------------------+
> | CHAR(0x41)             | UNHEX('41')              |
> +------------------------+--------------------------+
> | 0x41                   | 0x41                     |
> +------------------------+--------------------------+
> ```
> 
> 要编写一个二进制字符串表达式，以便无论是否启用`--binary-as-hex`都能显示为字符串，请使用以下技术：
> 
> `CHAR()`函数有一个`USING charset`子句：
> 
> ```sql
> mysql> SELECT CHAR(0x41 USING utf8mb4);
> +--------------------------+
> | CHAR(0x41 USING utf8mb4) |
> +--------------------------+
> | A                        |
> +--------------------------+
> ```
> 
> 更常见的是，使用`CONVERT()`将表达式转换为给定的字符集：
> 
> ```sql
> mysql> SELECT CONVERT(UNHEX('41') USING utf8mb4);
> +------------------------------------+
> | CONVERT(UNHEX('41') USING utf8mb4) |
> +------------------------------------+
> | A                                  |
> +------------------------------------+
> ```
> 
> 从MySQL 8.0.19开始，当mysql以交互模式运行时，默认情况下启用此选项。此外，当隐式或显式启用该选项时，`status`（或`\s`）命令的输出包括以下行：
> 
> ```
> Binary data as: Hexadecimal
> ```
> 
> 要禁用十六进制表示法，请使用`--skip-binary-as-hex`

可知从**MySQL 8.0.19**开始，当mysql以**交互模式**运行时，**默认**情况下启用`--binary-as-hex`，使用十六进制表示法（`0xvalue`）显示二进制数据，会影响所有二进制字符串的显示，包括`UNHEX()`

---


# 0x03 MySQL工作日志中的描述

> ## 关于工作日志
> 
> MySQL工作日志是针对更改的**设计规范**，这些更改可以定义过去的工作，也可以考虑用于将来的开发。

莆田搜索`--binary-as-hex`的时候找到了对应的MySQL工作日志[**《WL#13038: Make mysql command line tool's `--binary-as-hex` be on by default for interactive terminals》**（dev.mysql.com/worklog/task/?id=13038）](https://dev.mysql.com/worklog/task/?id=13038)（看来必应这回稍逊一筹），机翻如下：

> ## WL＃13038：对于交互式终端，默认情况下启用mysql命令行工具的`--binary-as-hex`
> 
> **影响**：Server-8.0 —&nbsp;&nbsp;&nbsp;**状态**：已完成
> 
> **描述**  
> mysql命令行要求服务器将接收到的所有数据转换为文本，以便可以显示它们。但是对于某些数据，转换会生成可能包含**不可打印符号**的二进制字符串。这些会使某些终端**乱码**。当在终端上以交互模式运行时，mysql所做的某些事情有所不同。这里的建议是在这种情况下也要打开`--binary-as-hex`。

可知其目的是防止终端乱码

*//End of Article*

**参考文献**：

* [《MySQL :: **MySQL 8.0 Reference Manual** :: 12.8 String Functions and Operators》（dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_unhex）](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_unhex)
* [《MySQL :: **MySQL 8.0 Reference Manual** :: 4.5.1.1 mysql Client Options》（dev.mysql.com/doc/refman/8.0/en/mysql-command-options.html#option_mysql_binary-as-hex）](https://dev.mysql.com/doc/refman/8.0/en/mysql-command-options.html#option_mysql_binary-as-hex)
* [《MySQL :: **WL#13038**: Make mysql command line tool's `--binary-as-hex` be on by default for interactive terminals》（dev.mysql.com/worklog/task/?id=13038）](https://dev.mysql.com/worklog/task/?id=13038)

![公众号二维码](https://neptliang.github.io/img/Article/WeChatBlog.png)