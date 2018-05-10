---
title: MySQL数据查询(上)
date: 2018-05-11 00:17:11
tags: 随笔
---

# select 语句介绍
语法
```
select selection_list
from 数据表名
where 字句   //查询时必须满足的条件，行必须满足的条件
group by字句 //如何对结果进行分组
order by字句 //如何对结果进行排序
having 字句 //对分组进行过滤
limit 字句 //限制结果的数量
```

<!-- more -->

# 一、单表查询
## 1、查询所有字段
```
select * from 数据表名；
```
```
mysql> select * from user;
+----+----------+----------+------------+
| id | username | password | email      |
+----+----------+----------+------------+
|  2 | AFeng    | 123      | 123.qq.com |
|  3 | Jing     | 456      | 456.qq.com |
|  4 | Chao     | 789      | 789.qq.com |
+----+----------+----------+------------+
3 rows in set (0.06 sec)
```
## 2、查询指定字段
```
select 字段名 from 数据表名；
```
**如果查询多个字段用逗号“，”分隔。**
```
mysql> select username from user;
+----------+
| username |
+----------+
| AFeng    |
| Jing     |
| Chao     |
+----------+
3 rows in set (0.03 sec)
```
## 3、查询指定数据（使用where字句进行过滤）
```
mysql> select * from user where username = 'AFeng';
+----+----------+----------+------------+
| id | username | password | email      |
+----+----------+----------+------------+
|  2 | AFeng    | 123      | 123.qq.com |
+----+----------+----------+------------+
1 row in set (0.09 sec)
```
## 4、带有in关键字的查询
in关键字可以判断**某个字段的值是否在指定的集合中**
```
select * from 数据表名 where 字段名 [not] in (元素1，元素2，...,元素n);
```
```
mysql> select * from user where username in ('AFeng','Jing');
+----+----------+----------+------------+
| id | username | password | email      |
+----+----------+----------+------------+
|  2 | AFeng    | 123      | 123.qq.com |
|  3 | Jing     | 456      | 456.qq.com |
+----+----------+----------+------------+
2 rows in set (0.05 sec)
```
## 5、带between and的范围查询
between and 关键字可以判断**某个字段的值是否在指定的范围内**。

```
select * from 数据表名 where 字段名 between 取值1 and 取值2；
```
```
mysql> select * from user where id between 2 and 3;
+----+----------+----------+------------+
| id | username | password | email      |
+----+----------+----------+------------+
|  2 | AFeng    | 123      | 123.qq.com |
|  3 | Jing     | 456      | 456.qq.com |
+----+----------+----------+------------+
2 rows in set (0.00 sec)
```
## 6、带有like的字符串匹配查询
link属于比较常用的比较运算符，常用于**实现模糊匹配**。
%：实现匹配0个或多个字符；
_：实现匹配一个字符。
```
select * from 数据表名 where 字段名 like 实现匹配的字符串
```
```
mysql> select * from user where username like '%n%';
+----+----------+----------+------------+
| id | username | password | email      |
+----+----------+----------+------------+
|  2 | AFeng    | 123      | 123.qq.com |
|  3 | Jing     | 456      | 456.qq.com |
+----+----------+----------+------------+
2 rows in set (0.06 sec)

mysql> select * from user where username like '%n_';
+----+----------+----------+------------+
| id | username | password | email      |
+----+----------+----------+------------+
|  2 | AFeng    | 123      | 123.qq.com |
|  3 | Jing     | 456      | 456.qq.com |
+----+----------+----------+------------+
2 rows in set (0.00 sec)
```
## 7、用is null 关键字查询空值
用来判断某个字段**是否为空**
```
select * from 数据表名 where 字段名 is [not] null;
```
```
mysql> select * from user where username is not null;
+----+----------+----------+------------+
| id | username | password | email      |
+----+----------+----------+------------+
|  2 | AFeng    | 123      | 123.qq.com |
|  3 | Jing     | 456      | 456.qq.com |
|  4 | Chao     | 789      | 789.qq.com |
+----+----------+----------+------------+
3 rows in set (0.00 sec)
```
## 8、带and 的多条件查询
只有**满足所有查询条件**的记录才会被查询出来。
```
select * from 数据表名 where 条件1 and 条件2 [...and 条件n];
```
```
mysql> select * from user where username = 'AFeng' and password = '123';
+----+----------+----------+------------+
| id | username | password | email      |
+----+----------+----------+------------+
|  2 | AFeng    | 123      | 123.qq.com |
|  5 | AFeng    | 123      | 135qq.com  |
+----+----------+----------+------------+
2 rows in set (0.00 sec)
```
## 9、带有or 的条件查询
只要**满足其中的一个条件**，那么次记录就会被查询出来。
```
select * from 数据表名 where 条件1 or 条件2 [...or 条件n];
```
```
mysql> select * from user where password = '123'or username = 'AFeng';
+----+----------+----------+------------+
| id | username | password | email      |
+----+----------+----------+------------+
|  2 | AFeng    | 123      | 123.qq.com |
|  5 | AFeng    | 123      | 135qq.com  |
+----+----------+----------+------------+
2 rows in set (0.05 sec)
```
## 10、用distinct 关键字去除结果中的重复行
```
select distinct 字段名 from 数据表名;
```
```
mysql> select distinct username from user;
+----------+
| username |
+----------+
| AFeng    |
| Jing     |
| Chao     |
+----------+
3 rows in set (0.08 sec)
```
## 11、用order by 关键字对查询结果进行排序
```
order by 字段名 [asc | desc];
```
```
mysql> select * from user order by id desc;
+----+----------+----------+------------+
| id | username | password | email      |
+----+----------+----------+------------+
|  5 | AFeng    | 123      | 135qq.com  |
|  4 | Chao     | 789      | 789.qq.com |
|  3 | Jing     | 456      | 456.qq.com |
|  2 | AFeng    | 123      | 123.qq.com |
+----+----------+----------+------------+
4 rows in set (0.00 sec)
```
## 12、用 group by 关键字分组查询
```
mysql> select id,username from user group by username;
+----+----------+
| id | username |
+----+----------+
|  2 | AFeng    |
|  4 | Chao     |
|  3 | Jing     |
+----+----------+
//分组以后也进行了默认排序（字母升序）
3 rows in set (0.00 sec)
```
## 13、用limit 限制查询结果的数量
```
mysql> select * from user order by id limit 2;
+----+----------+----------+------------+
| id | username | password | email      |
+----+----------+----------+------------+
|  2 | AFeng    | 123      | 123.qq.com |
|  3 | Jing     | 456      | 456.qq.com |
+----+----------+----------+------------+
2 rows in set (0.00 sec)
```
**使用limit 还可以取查询结果的中间部分值，指定两个参数（第一个起始位置，默认从0开始，第二个参数：返回多少个数据）**；
```
mysql> select * from user order by id limit 0,1;
+----+----------+----------+------------+
| id | username | password | email      |
+----+----------+----------+------------+
|  2 | AFeng    | 123      | 123.qq.com |
+----+----------+----------+------------+
1 row in set (0.00 sec)
```