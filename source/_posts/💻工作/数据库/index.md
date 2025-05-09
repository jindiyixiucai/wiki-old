---
title: 数据库操作记录
toc: true
tags: MySQL
categories:
  - "\U0001F4BB 工作"
  - 数据库
date: 2020-06-06 12:27:56
---

## 创建数据库及用户
### 创建数据库
- 语法格式
```plain
CREATE DATABASE [IF NOT EXISTS] <数据库名>
[[DEFAULT] CHARACTER SET <字符集名>] 
[[DEFAULT] COLLATE <校对规则名>];
```
- 实例
```sql
CREATE DATABASE ODSP DEFAULT CHARACTER SET utf8;
Query OK, 1 row affected (0.01 sec)
```
参见[MySQL 创建数据库（CREATE DATABASE 语句）](http://c.biancheng.net/view/2413.html)
### 创建用户
- 语法格式
```plain
grant 权限 on 数据库.* to 用户名@登录主机 identified by '密码';
```
- 实例
1. 以 root 用户登录数据库
```bash
mysql -uroot -p
```
输入密码登录；
2. 创建用户
```plain
mysql> grant all privileges on MyDB.* to imoyao@localhost identified by 'p@ssw0rd';
Query OK, 0 rows affected, 1 warning (0.04 sec)

mysql> flush privileges;
```
3. 以新用户身份登录
```bash
mysql -uperabytes -p
```
查询数据库并使用
```sql
show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| MyDB               |
+--------------------+
2 rows in set (0.00 sec)

mysql> use MyDB
Database changed
mysql> show tables;
Empty set (0.00 sec)
```
参见[mysql 创建一个用户，指定一个数据库_许喜乐-CSDN 博客_在本地主机创建用户账号 st_01,密码为 123456。](https://blog.csdn.net/xuxile/article/details/53161908)