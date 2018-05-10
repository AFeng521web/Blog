---
title: 一次编写多次调用--MySQL存储过程
date: 2018-05-11 00:19:20
tags: 随笔
---

# 存储过程（Srored Procedure）
## 一、什么是存储过程
一组可编程的函数，是为了完成特定功能的SQL语句集，进编创建并保存在数据库的服务器中，它的存储和调用都是在服务器进行的，用户可以通过调用存储过程的名字并给定参数（需要时）来调用并执行。
## 二、优点
将重复性很高的一些操作，封装到一个存储过程中，简化了对这些SQL的调用
批量处理： SQL+ 循环，并且存储过程是在MySQL服务器中存储和执行的，可以减少客户端和服务器端的数据传输，减少网络流量。
统一接口，确保数据安全。

<!--more-->

## 三、存储过程的基本操作
###创建和调用
存储过程就是具有名字的一段代，用来完成一个特定的功能。
创建的存储过程是保存在数据库的数据字典中的。
```
create procedure sp_name([参数列表])
[存储过程特性] 
过程体
```
调用存储过程
```
call 存储过程名字
```
创建不带参数的存储过程
```
mysql> CREATE PROCEDURE sp1()
    -> SELECT VERSION();
    -> //
Query OK, 0 rows affected (0.05 sec)

mysql> CALL sp1();
    -> //
+-----------+
| VERSION() |
+-----------+
| 5.5.59    |
+-----------+
1 row in set (0.02 sec)

Query OK, 0 rows affected (0.02 sec)
```
创建带有IN类型的参数
表示调用者向存储过程传入值，相当于定义了一个形参，调用时传入实参。传入值的类型可以是字面量或变量
```
mysql> CREATE TABLE users(
    -> id SMALLINT UNSIGNED PRIMARY KEY AUTO_INCREMENT,
    -> username VARCHAR(20) NOT NULL,
    -> password VARCHAR(32) NOT NULL,
    -> age TINYINT UNSIGNED NOT NULL DEFAULT 10,
    -> sex BOOLEAN
    -> )//
Query OK, 0 rows affected (0.19 sec)

mysql> CREATE PROCEDURE removeUsersById(IN p_id INT UNSIGNED)
    -> BEGIN
    -> DELETE FROM users WHERE id = p_id;
    -> END
    -> //
Query OK, 0 rows affected (0.00 sec)

mysql> CALL removeUsersById(2);
    -> //
Query OK, 1 row affected (0.09 sec)

mysql> SELECT * FROM users;//
+----+----------+----------------------------------+-----+------+
| id | username | password                         | age | sex  |
+----+----------+----------------------------------+-----+------+
|  1 | A        | 7fc56270e7a70fa81a5935b72eacbe29 |  20 |    0 |
|  3 | C        | 0d61f8370cad1d412f80b84d143e1257 |  23 |    1 |
|  4 | D        | f623e75af30e62bbd73d6df5b50bb7b5 |  24 |    1 |
|  5 | E        | 3a3ea00cfc35332cedf6e5e9a32e94da |  24 |    0 |
|  6 | F        | 800618943025315f869e4e1f09471012 |  23 |    0 |
|  7 | G        | dfcf28d0734569a6a693bc8194de62bf |  22 |    0 |
|  8 | H        | c1d9f50f86825a1a2302ec2449c17196 |  23 |    0 |
|  9 | I        | dd7536794b63bf90eccfd37f9b147d7f |  23 |    0 |
| 10 | J        | ff44570aca8241914870afbc310cdb85 |  22 |    1 |
| 11 | K        | a5f3c6a11b03839d46af9fb43c97c188 |  22 |    1 |
| 12 | L        | d20caec3b48a1eef164cb4ca81ba2587 |  22 |    0 |
| 13 | M        | 69691c7bdcc3ce6d5d8a1361f22d04ac |  24 |    1 |
| 14 | N        | 8d9c307cb7f3c4a32822a51922d1ceaa |  21 |    0 |
| 15 | O        | f186217753c37b9b9f958d906208506e |  20 |    0 |
| 16 | P        | 44c29edb103a2872f519ad0c9a0fdaaa |  20 |    1 |
| 17 | Q        | f09564c9ca56850d4cd6b3319e541aee |  24 |    1 |
| 18 | R        | e1e1d3d40573127e9ee0480caf1283d6 |  24 |    1 |
+----+----------+----------------------------------+-----+------+
17 rows in set (0.00 sec)

mysql> SELECT * FROM users WHERE id = 2;
    -> //
Empty set (0.05 sec)
```
创建带有in和out类型的参数的存储过程
OUT输出参数：表示过程向调用者传出值(可以返回多个值)（传出值只能是变量）
要在调用存储过程时定义一个全局变量用于接收out类型的值
```
mysql> USE test;
Database changed
mysql> SELECT * FROM users;
+----+----------+----------------------------------+-----+------+
| id | username | password                         | age | sex  |
+----+----------+----------------------------------+-----+------+
|  1 | A        | 7fc56270e7a70fa81a5935b72eacbe29 |  20 |    0 |
|  3 | C        | 0d61f8370cad1d412f80b84d143e1257 |  23 |    1 |
|  4 | D        | f623e75af30e62bbd73d6df5b50bb7b5 |  24 |    1 |
|  5 | E        | 3a3ea00cfc35332cedf6e5e9a32e94da |  24 |    0 |
|  6 | F        | 800618943025315f869e4e1f09471012 |  23 |    0 |
|  7 | G        | dfcf28d0734569a6a693bc8194de62bf |  22 |    0 |
|  8 | H        | c1d9f50f86825a1a2302ec2449c17196 |  23 |    0 |
|  9 | I        | dd7536794b63bf90eccfd37f9b147d7f |  23 |    0 |
| 10 | J        | ff44570aca8241914870afbc310cdb85 |  22 |    1 |
| 11 | K        | a5f3c6a11b03839d46af9fb43c97c188 |  22 |    1 |
| 12 | L        | d20caec3b48a1eef164cb4ca81ba2587 |  22 |    0 |
| 13 | M        | 69691c7bdcc3ce6d5d8a1361f22d04ac |  24 |    1 |
| 14 | N        | 8d9c307cb7f3c4a32822a51922d1ceaa |  21 |    0 |
| 15 | O        | f186217753c37b9b9f958d906208506e |  20 |    0 |
| 16 | P        | 44c29edb103a2872f519ad0c9a0fdaaa |  20 |    1 |
| 17 | Q        | f09564c9ca56850d4cd6b3319e541aee |  24 |    1 |
| 18 | R        | e1e1d3d40573127e9ee0480caf1283d6 |  24 |    1 |
+----+----------+----------------------------------+-----+------+
17 rows in set (0.01 sec)

mysql> DELIMITER //

mysql> CREATE PROCEDURE removeUserAndReturnNums(IN p_id INT UNSIGNED,OUT userNums INT UNSIGNED)
    -> BEGIN
    -> DELETE FROM users WHERE id = p_id;
    -> SELECT count(id) FROM users INTO userNums;
    -> END
    -> //
Query OK, 0 rows affected (0.09 sec)

mysql> DELIMITER ;

mysql> SELECT count(id) FROM users;
+-----------+
| count(id) |
+-----------+
|        17 |
+-----------+
1 row in set (0.06 sec)

mysql> CALL removeUserAndReturnNums(5,@nums);
Query OK, 1 row affected (0.09 sec)

mysql> SELECT @nums;
+-------+
| @nums |
+-------+
|    16 |
+-------+
1 row in set (0.00 sec)

mysql> SELECT * FROM users;
+----+----------+----------------------------------+-----+------+
| id | username | password                         | age | sex  |
+----+----------+----------------------------------+-----+------+
|  1 | A        | 7fc56270e7a70fa81a5935b72eacbe29 |  20 |    0 |
|  3 | C        | 0d61f8370cad1d412f80b84d143e1257 |  23 |    1 |
|  4 | D        | f623e75af30e62bbd73d6df5b50bb7b5 |  24 |    1 |
|  6 | F        | 800618943025315f869e4e1f09471012 |  23 |    0 |
|  7 | G        | dfcf28d0734569a6a693bc8194de62bf |  22 |    0 |
|  8 | H        | c1d9f50f86825a1a2302ec2449c17196 |  23 |    0 |
|  9 | I        | dd7536794b63bf90eccfd37f9b147d7f |  23 |    0 |
| 10 | J        | ff44570aca8241914870afbc310cdb85 |  22 |    1 |
| 11 | K        | a5f3c6a11b03839d46af9fb43c97c188 |  22 |    1 |
| 12 | L        | d20caec3b48a1eef164cb4ca81ba2587 |  22 |    0 |
| 13 | M        | 69691c7bdcc3ce6d5d8a1361f22d04ac |  24 |    1 |
| 14 | N        | 8d9c307cb7f3c4a32822a51922d1ceaa |  21 |    0 |
| 15 | O        | f186217753c37b9b9f958d906208506e |  20 |    0 |
| 16 | P        | 44c29edb103a2872f519ad0c9a0fdaaa |  20 |    1 |
| 17 | Q        | f09564c9ca56850d4cd6b3319e541aee |  24 |    1 |
| 18 | R        | e1e1d3d40573127e9ee0480caf1283d6 |  24 |    1 |
+----+----------+----------------------------------+-----+------+
16 rows in set (0.00 sec)
```
创建带有多个参数的存储过程
```
mysql> CREATE PROCEDURE removeUserByAgeAndReturnInfos(IN p_age SMALLINT UNSIGNED,OUT deleteUser SMALLINT UNSIGNED, OUT userCounts SMALLINT UNSIGNED)
    -> BEGIN
    -> DELETE FROM users WHERE age=p_age;
    -> SELECT ROW_COUNT() INTO deleteUser;
    -> SELECT COUNT(id) FROM users INTO userCounts;
    -> END
    -> //
Query OK, 0 rows affected (0.06 sec)

mysql> SELECT * FROM users;
    -> //
+----+----------+----------------------------------+-----+------+
| id | username | password                         | age | sex  |
+----+----------+----------------------------------+-----+------+
|  1 | A        | 7fc56270e7a70fa81a5935b72eacbe29 |  20 |    0 |
|  3 | C        | 0d61f8370cad1d412f80b84d143e1257 |  23 |    1 |
|  4 | D        | f623e75af30e62bbd73d6df5b50bb7b5 |  24 |    1 |
|  6 | F        | 800618943025315f869e4e1f09471012 |  23 |    0 |
|  7 | G        | dfcf28d0734569a6a693bc8194de62bf |  22 |    0 |
|  8 | H        | c1d9f50f86825a1a2302ec2449c17196 |  23 |    0 |
|  9 | I        | dd7536794b63bf90eccfd37f9b147d7f |  23 |    0 |
| 10 | J        | ff44570aca8241914870afbc310cdb85 |  22 |    1 |
| 11 | K        | a5f3c6a11b03839d46af9fb43c97c188 |  22 |    1 |
| 12 | L        | d20caec3b48a1eef164cb4ca81ba2587 |  22 |    0 |
| 13 | M        | 69691c7bdcc3ce6d5d8a1361f22d04ac |  24 |    1 |
| 14 | N        | 8d9c307cb7f3c4a32822a51922d1ceaa |  21 |    0 |
| 15 | O        | f186217753c37b9b9f958d906208506e |  20 |    0 |
| 16 | P        | 44c29edb103a2872f519ad0c9a0fdaaa |  20 |    1 |
| 17 | Q        | f09564c9ca56850d4cd6b3319e541aee |  24 |    1 |
| 18 | R        | e1e1d3d40573127e9ee0480caf1283d6 |  24 |    1 |
+----+----------+----------------------------------+-----+------+
16 rows in set (0.00 sec)

mysql> DELIMITER ;
mysql> CALL removeUserByAgeAndReturnInfos(20,@a,@b);
Query OK, 1 row affected (0.32 sec)

mysql> SELECT @a,@b;
+------+------+
| @a   | @b   |
+------+------+
|    3 |   13 |
+------+------+
1 row in set (0.00 sec)

mysql> SELECT COUNT(id) FROM users;
+-----------+
| COUNT(id) |
+-----------+
|        13 |
+-----------+
1 row in set (0.00 sec)
```
查看存储过程
show procedure status [like "pattern"]
```
mysql> show procedure status;
+------------+-------------------------------+-----------+----------------+---------------------+---------------------+---------------+---------+----------------------+----------------------+--------------------+
| Db         | Name                          | Type      | Definer        | Modified            | Created             | Security_type | Comment | character_set_client | collation_connection | Database Collation |
+------------+-------------------------------+-----------+----------------+---------------------+---------------------+---------------+---------+----------------------+----------------------+--------------------+
| learnmysql | loop_example                  | PROCEDURE | root@localhost | 2018-03-15 16:37:13 | 2018-03-15 16:37:13 | DEFINER       |         | utf8                 | utf8_general_ci      | utf8_general_ci    |
| learnmysql | p1                            | PROCEDURE | root@localhost | 2018-03-15 15:56:44 | 2018-03-15 15:56:44 | DEFINER       |         | utf8                 | utf8_general_ci      | utf8_general_ci    |
| learnmysql | p2                            | PROCEDURE | root@localhost | 2018-03-15 16:00:44 | 2018-03-15 16:00:44 | DEFINER       |         | utf8                 | utf8_general_ci      | utf8_general_ci    |
| learnmysql | while_example                 | PROCEDURE | root@localhost | 2018-03-15 16:06:47 | 2018-03-15 16:06:47 | DEFINER       |         | utf8                 | utf8_general_ci      | utf8_general_ci    |
| test       | removeUserAndReturnNums       | PROCEDURE | root@localhost | 2018-03-15 08:00:27 | 2018-03-15 08:00:27 | DEFINER       |         | utf8                 | utf8_general_ci      | utf8_general_ci    |
| test       | removeUserByAgeAndReturnInfos | PROCEDURE | root@localhost | 2018-03-15 08:29:43 | 2018-03-15 08:29:43 | DEFINER       |         | utf8                 | utf8_general_ci      | utf8_general_ci    |
| test       | removeUsersById               | PROCEDURE | root@localhost | 2018-03-14 23:37:41 | 2018-03-14 23:37:41 | DEFINER       |         | gbk                  | gbk_chinese_ci       | utf8_general_ci    |
| test       | sp1                           | PROCEDURE | root@localhost | 2018-03-14 22:56:51 | 2018-03-14 22:56:51 | DEFINER       |         | gbk                  | gbk_chinese_ci       | utf8_general_ci    |
+------------+-------------------------------+-----------+----------------+---------------------+---------------------+---------------+---------+----------------------+----------------------+--------------------+
8 rows in set (0.19 sec)
```
show create procedure 名字；
```
mysql> show create procedure sp1;
+-----------+----------------------------------------------------------------+----------------------------------------------------------------------+----------------------+----------------------+--------------------+
| Procedure | sql_mode                                                       | Create Procedure                                                     | character_set_client | collation_connection | Database Collation |
+-----------+----------------------------------------------------------------+----------------------------------------------------------------------+----------------------+----------------------+--------------------+
| sp1       | STRICT_TRANS_TABLES,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION | CREATE DEFINER=`root`@`localhost` PROCEDURE `sp1`()
SELECT VERSION() | gbk                  | gbk_chinese_ci       | utf8_general_ci    |
+-----------+----------------------------------------------------------------+----------------------------------------------------------------------+----------------------+----------------------+--------------------+
1 row in set (0.13 sec)
```
两种方式区别
show status语句只能查看存储过程所操作的数据库对象，如存储过程的名称、类型、定义者，修改时间等信息，并不能查看存储过程的具体定义。show create 可以查看具体定义。

删除存储过程
drop procedure if exists 存储过程名字；
```
mysql> drop procedure if exists sp1;
Query OK, 0 rows affected (0.17 sec)
```