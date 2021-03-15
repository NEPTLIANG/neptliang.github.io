---
layout:       post
title:        "MySQL报错ERROR 1449 (HY000): The user specified as a definer ('mysql.infoschema'@'localhost') does not exist解决"
subtitle:     "null"
date:         2020-12-30 14:23:45
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - MySQL
---


# 0x00 背景

之前按照网上的旧方法改MySQL密码，结果改完登录不上MySQL，折腾好久才解决[（neptliang.github.io/2020/07/12/MySQL-Access-Denied/）](https://neptliang.github.io/2020/07/12/MySQL-Access-Denied/)，但是登录上后发现**没法查数据库表字段信息**，必应了好久都没修好。

后来看到[@xiaoer wang在StackOverflow的问题《ERROR 1449 (HY000): The user specified as a definer ('mysql.infoschema'@'localhost') does not exist》下的回答（stackoverflow.com/a/65574847/10838083）](https://stackoverflow.com/a/65574847/10838083)得知，其实**删除用户`mysql.infoschema`然后重新创建**就行，之前怕出事没敢尝试。在此记录一下

查数据库表字段信息报错：

```sql
mysql> flush privileges;
Query OK, 0 rows affected (0.08 sec)

mysql> show databases;
ERROR 1449 (HY000): The user specified as a definer ('mysql.infoschema'@'localhost') does not exist

mysql> select distinct table_name from information_schema.tables where table_schema='mysql' limit 0,5;
ERROR 1449 (HY000): The user specified as a definer ('mysql.infoschema'@'localhost') does not exist
```

用户明明存在，`host`也对得上

```sql
mysql> select user,host from mysql.user where user='mysql.infoschema';
+------------------+-----------+
| user             | host      |
+------------------+-----------+
| mysql.infoschema | localhost |
+------------------+-----------+
1 row in set (0.00 sec)
```

授权也不彳亍，创建也不行

```sql
mysql> GRANT SELECT ON *.* TO `mysql.infoschema`@`localhost` ;
ERROR 1410 (42000): You are not allowed to create a user with GRANT

mysql> grant all on *.* to 'mysql.infoschema'@'localhost';
ERROR 1410 (42000): You are not allowed to create a user with GRANT

mysql> create user 'mysql.infoschema'@'localhost';
ERROR 1396 (HY000): Operation CREATE USER failed for 'mysql.infoschema'@'localhost'
```

`mysql.user`表里的`root`用户的授权和创建用户权限明明🈚异常

```sql
mysql> select Grant_priv, user from mysql.user;
+------------+------------------+
| Grant_priv | user             |
+------------+------------------+
| N          | mysql.infoschema |
| N          | mysql.session    |
| N          | mysql.sys        |
| Y          | root             |
+------------+------------------+
5 rows in set (0.00 sec)

mysql> select Create_user_priv, user from mysql.user;
+------------------+------------------+
| Create_user_priv | user             |
+------------------+------------------+
| N                | mysql.infoschema |
| N                | mysql.session    |
| N                | mysql.sys        |
| Y                | root             |
+------------------+------------------+
5 rows in set (0.00 sec)
```

`mysql.infoschema`也🈶查询权限

```sql
mysql> select host, user, select_priv, insert_priv, update_priv, delete_priv, create_priv, drop_priv, grant_priv from mysql.user;
+-----------+------------------+-------------+-------------+-------------+-------------+-------------+-----------+------------+
| host      | user             | select_priv | insert_priv | update_priv | delete_priv | create_priv | drop_priv | grant_priv |
+-----------+------------------+-------------+-------------+-------------+-------------+-------------+-----------+------------+
| localhost | mysql.infoschema | Y           | N           | N           | N           | N           | N         | N          |
| localhost | mysql.session    | N           | N           | N           | N           | N           | N         | N          |
| localhost | mysql.sys        | N           | N           | N           | N           | N           | N         | N          |
| localhost | root             | Y           | Y           | Y           | Y           | Y           | Y         | Y          |
+-----------+------------------+-------------+-------------+-------------+-------------+-------------+-----------+------------+
5 rows in set (0.00 sec)
```

但是`show grants`有丶不对劲

```sql
mysql> show grants;
+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Grants for root@localhost                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, SHUTDOWN, PROCESS, FILE, REFERENCES, INDEX, ALTER, SHOW DATABASES, SUPER, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER, CREATE TABLESPACE, CREATE ROLE, DROP ROLE ON *.* TO `root`@`localhost` WITH GRANT OPTION                                                                                                                                               |
| GRANT APPLICATION_PASSWORD_ADMIN,AUDIT_ADMIN,BACKUP_ADMIN,BINLOG_ADMIN,BINLOG_ENCRYPTION_ADMIN,CLONE_ADMIN,CONNECTION_ADMIN,ENCRYPTION_KEY_ADMIN,GROUP_REPLICATION_ADMIN,INNODB_REDO_LOG_ARCHIVE,INNODB_REDO_LOG_ENABLE,PERSIST_RO_VARIABLES_ADMIN,REPLICATION_APPLIER,REPLICATION_SLAVE_ADMIN,RESOURCE_GROUP_ADMIN,RESOURCE_GROUP_USER,ROLE_ADMIN,SERVICE_CONNECTION_ADMIN,SESSION_VARIABLES_ADMIN,SET_USER_ID,SHOW_ROUTINE,SYSTEM_USER,SYSTEM_VARIABLES_ADMIN,TABLE_ENCRYPTION_ADMIN,XA_RECOVER_ADMIN ON *.* TO `root`@`localhost` WITH GRANT OPTION |
| GRANT PROXY ON ''@'' TO 'root'@'localhost' WITH GRANT OPTION                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
3 rows in set (0.00 sec)

mysql> show grants for 'mysql.infoschema'@'localhost';
ERROR 1141 (42000): There is no such grant defined for user 'mysql.infoschema' on host 'localhost'
```

可见还是`mysql.infoschema`的权限没了

以下是解决过程

# 0x01 删除用户`mysql.infoschema`

```sql
mysql> drop user 'mysql.infoschema'@'localhost';
Query OK, 0 rows affected (0.03 sec)

mysql> select user,host from mysql.user;
+---------------+-----------+
| user          | host      |
+---------------+-----------+
| mysql.session | localhost |
| mysql.sys     | localhost |
| root          | localhost |
+---------------+-----------+
4 rows in set (0.00 sec)
```

# 0x02 重建用户`mysql.infoschema`

```sql
mysql> create user 'mysql.infoschema'@'localhost';
Query OK, 0 rows affected (0.02 sec)
```

# 0x03 授权

这下就可以授权了

```sql
mysql> GRANT SELECT ON *.* TO `mysql.infoschema`@`localhost` ;
Query OK, 0 rows affected (0.01 sec)

mysql> flush privileges;
Query OK, 0 rows affected (0.01 sec)
```

就完事了

```sql
mysql> select distinct table_schema from information_schema.tables;
+--------------------+
| TABLE_SCHEMA       |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
5 rows in set (0.00 sec)

mysql> use mysql
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> show tables;
+------------------------------------------------------+
| Tables_in_mysql                                      |
+------------------------------------------------------+
| columns_priv                                         |
| component                                            |
| db                                                   |
| default_roles                                        |
| engine_cost                                          |
| ...                                                  |
+------------------------------------------------------+
35 rows in set (0.00 sec)

mysql> show grants;
+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Grants for root@localhost                                                         |
+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, SHUTDOWN, PROCESS, FILE, REFERENCES, INDEX, ALTER, SHOW DATABASES, SUPER, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER, CREATE TABLESPACE, CREATE ROLE, DROP ROLE ON *.* TO `root`@`localhost` WITH GRANT OPTION                                                         |
| GRANT APPLICATION_PASSWORD_ADMIN,AUDIT_ADMIN,BACKUP_ADMIN,BINLOG_ADMIN,BINLOG_ENCRYPTION_ADMIN,CLONE_ADMIN,CONNECTION_ADMIN,ENCRYPTION_KEY_ADMIN,GROUP_REPLICATION_ADMIN,INNODB_REDO_LOG_ARCHIVE,INNODB_REDO_LOG_ENABLE,PERSIST_RO_VARIABLES_ADMIN,REPLICATION_APPLIER,REPLICATION_SLAVE_ADMIN,RESOURCE_GROUP_ADMIN,RESOURCE_GROUP_USER,ROLE_ADMIN,SERVICE_CONNECTION_ADMIN,SESSION_VARIABLES_ADMIN,SET_USER_ID,SHOW_ROUTINE,SYSTEM_USER,SYSTEM_VARIABLES_ADMIN,TABLE_ENCRYPTION_ADMIN,XA_RECOVER_ADMIN ON *.* TO `root`@`localhost` WITH GRANT OPTION |
| GRANT PROXY ON ''@'' TO 'root'@'localhost' WITH GRANT OPTION                                                         |
+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
3 rows in set (0.00 sec)

mysql> show grants for 'mysql.infoschema'@'localhost';
+-------------------------------------------------------+
| Grants for mysql.infoschema@localhost                 |
+-------------------------------------------------------+
| GRANT SELECT ON *.* TO `mysql.infoschema`@`localhost` |
+-------------------------------------------------------+
1 row in set (0.00 sec)
```


---

***`//End of Article`***

---


# 参考文献

* [@xiaoer wang在StackOverflow的问题《ERROR 1449 (HY000): The user specified as a definer ('mysql.infoschema'@'localhost') does not exist》下的回答（stackoverflow.com/a/65574847/10838083）](https://stackoverflow.com/a/65574847/10838083)


![公众号二维码](https://neptliang.github.io/img/Article/WeChatBlog.png)