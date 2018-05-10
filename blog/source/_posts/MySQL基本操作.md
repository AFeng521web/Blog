---
title: MySQL基本操作
date: 2018-05-11 00:16:44
tags: 随笔
---

#一、数据库的操作
**创建数据库（create database）**
```
create database 数据库名
```
**查看数据库（show databases）**
```
show databases;
```
```
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| learnmysql         |
| mysql              |
| performance_schema |
| test               |
+--------------------+
5 rows in set (0.17 sec)
```
<!-- more -->

**打开数据库（use database）**
创建完数据库，如果不打开，那么并没有在这个数据库下进行相关操作。
```
use 数据库名
```
**删除数据库（drop database）**
```
drop database 数据库名
```
删除数据库时应该谨慎操作，一旦执行该操作，数据库的所有结构和数据都会被删除

#二、数据表的操作

在对MySQL数据表进行操作之前，必须先使用use语句选择数据库，才可以在指定的数据库中对数据表进行操作。

**创建数据表（create table）**
```
create table table_name(列名1 属性，列名2 属性....)
```
```
mysql> create table user(
    -> id int primary key auto_increment,
    -> username varchar(30) not null,
    -> password varchar(30) not null
    -> );
Query OK, 0 rows affected (0.52 sec)
```
**查看数据表结构（show columns 或 describe）**
```
show columns from 数据表名 [from 数据库名]；
或者
show columns from 数据库名.数据表名；
```
```
mysql> show columns from learnmysql.user;
+----------+-------------+------+-----+---------+----------------+
| Field    | Type        | Null | Key | Default | Extra          |
+----------+-------------+------+-----+---------+----------------+
| id       | int(11)     | NO   | PRI | NULL    | auto_increment |
| username | varchar(30) | NO   |     | NULL    |                |
| password | varchar(30) | NO   |     | NULL    |                |
+----------+-------------+------+-----+---------+----------------+
3 rows in set (0.15 sec)
```
```
describe 数据表名
或者
describe 数据表名 列名
```
```
mysql> describe user id;
+-------+---------+------+-----+---------+----------------+
| Field | Type    | Null | Key | Default | Extra          |
+-------+---------+------+-----+---------+----------------+
| id    | int(11) | NO   | PRI | NULL    | auto_increment |
+-------+---------+------+-----+---------+----------------+
1 row in set (0.06 sec)
```
**修改数据表结构（alter table）**
修改数据表的结构是指增加或删除字段，修改字段名称或者字段类型，设置取消主键外键，设置取消索引以及修改表的注释等。
```
alter [ignore] table 数据表名 alter_spec[,alter_spec] ...
```
当指定ignore时，如果出现重复关键的行，则只执行一行，其余重复的行会被删除。
alter table语句允许指定多个动作，其动作之间使用逗号分隔。
```
mysql> alter table user add email varchar(50) not null,modify username varchar(50);
Query OK, 0 rows affected (0.47 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> show columns from user;
+----------+-------------+------+-----+---------+----------------+
| Field    | Type        | Null | Key | Default | Extra          |
+----------+-------------+------+-----+---------+----------------+
| id       | int(11)     | NO   | PRI | NULL    | auto_increment |
| username | varchar(50) | YES  |     | NULL    |                |
| password | varchar(30) | NO   |     | NULL    |                |
| email    | varchar(50) | NO   |     | NULL    |                |
+----------+-------------+------+-----+---------+----------------+
4 rows in set (0.01 sec)
```
**重命名表 （rename table）**
```
rename table 数据表1 to 数据表2
```
该语句可以同时对多个数据表进行重命名，多个表之间以逗号“，”分隔。
```
mysql> rename table user to user1;
Query OK, 0 rows affected (0.22 sec)

mysql> desc user1;
+----------+-------------+------+-----+---------+----------------+
| Field    | Type        | Null | Key | Default | Extra          |
+----------+-------------+------+-----+---------+----------------+
| id       | int(11)     | NO   | PRI | NULL    | auto_increment |
| username | varchar(50) | YES  |     | NULL    |                |
| password | varchar(30) | NO   |     | NULL    |                |
| email    | varchar(50) | NO   |     | NULL    |                |
+----------+-------------+------+-----+---------+----------------+
4 rows in set (0.01 sec)
```
**删除数据表（drop table）**
```
drop table if exists 数据表名;
```
#三、MySQL语句的操作
**插入记录（insert）**
```
insert into 数据表名(column_name1,column_name2,...) values(value1,value2,...)
```
```
mysql> insert into user(username,password,email) values('AFeng','12345','12345@qq.com');
Query OK, 1 row affected (0.11 sec)

mysql> select * from user;
+----+----------+----------+--------------+
| id | username | password | email        |
+----+----------+----------+--------------+
|  1 | AFeng    | 12345    | 12345@qq.com |
+----+----------+----------+--------------+
1 row in set (0.00 sec)
```
**查询数据表记录（select）**
要从数据库中把数据查询出来，就要用到数据查询语句select,select是最常用的查询语句，它的功能很强大。
```
mysql> select * from user;
+----+----------+----------+--------------+
| id | username | password | email        |
+----+----------+----------+--------------+
|  1 | AFeng    | 12345    | 12345@qq.com |
+----+----------+----------+--------------+
1 row in set (0.00 sec)

mysql> select id,username from user;
+----+----------+
| id | username |
+----+----------+
|  1 | AFeng    |
+----+----------+
1 row in set (0.00 sec)
```
**修改记录（update）**
```
update 数据表名 set column_name1 = new value1,column_name2 = new value2, where condition
```
```
mysql> update user set password = 67890 where id = 1;
Query OK, 1 row affected (0.13 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> select * from user where id=1;
+----+----------+----------+--------------+
| id | username | password | email        |
+----+----------+----------+--------------+
|  1 | AFeng    | 67890    | 12345@qq.com |
+----+----------+----------+--------------+
1 row in set (0.00 sec)
```
set字句指出要修改的列和它们给定的值，where字句是可选的，指定过滤条件，如果不给出，所有的记录都会被修改。

**删除记录（delete）**
```
delete from 数据表名 where condition;
```
```
mysql> delete from user where id=1;
Query OK, 1 row affected (0.09 sec)

mysql> select * from user;
Empty set (0.00 sec)
```
该语句在执行过程中，如果没有指定where条件，将删除所有的记录，如果指定了where字句，将按照指定的条件进行删除。

