---
title: MySQL数据查询(下)
date: 2018-05-11 00:17:48
tags: 随笔
---

# 一、子查询
## 1、什么是子查询（sub query）
**查询是在某个查询结果之上进行的（即外层查询要使用内层查询的结果集），也就是说一条 select语句内部包含了另外一条或多条 select 语句。**

子查询总是先执行内部查询，然后把它的结果集传给外层查询使用。子查询可以用在任何可以使用表达式的地方，它必须由父查询包含。

<!-- more -->

## 2、引发子查询的几种情况分析
 - **带有in关键字的子查询**
```
select * from 数据表名 where 字段名 in （select 字段名 from 数据表名）
```
 - **带有比较运算符的子查询**
只有子查询返回的结果列只包含一个值时，比较运算符才适用。
```
mysql> select id,username from user where id>=(select id from user where id>4);
+----+----------+
| id | username |
+----+----------+
|  5 | AFeng    |
+----+----------+
1 row in set (0.00 sec)
```
 - **带有exists关键字的子查询**
使用exists关键字时，内层查询语句不返回查询的记录，而只是返回一个真假值，满足内层查询语句条件就返回true，此时外层查询语句就会执行。
```
mysql> select * from user where exists (select * from user where id = 2);
+----+----------+----------+------------+
| id | username | password | email      |
+----+----------+----------+------------+
|  2 | AFeng    | 123      | 123.qq.com |
|  3 | Jing     | 456      | 456.qq.com |
|  4 | Chao     | 789      | 789.qq.com |
|  5 | AFeng    | 123      | 135qq.com  |
+----+----------+----------+------------+
4 rows in set (0.02 sec)
```
 - **带有 any 关键字的子查询**
any 关键字表示只要**满足内层查询语句返回的结果集中任意一个**，就可以通过该条件来执行外层查询语句。

 - **带有 all 关键字的子查询**
只有满足内层查询语句返回的所有结果集，才可以执行外层查询语句。

## 3、利用子查询进行过滤
需求：查询出订购物品RGAN01的所有顾客
步骤：

 1. 检索出包含物品RGAN01的所有订单的编号
 2. 检索出具有前一步骤列出订单编号的所有顾客的ID
 3. 检索出前一步骤返回的所有顾客ID的所有顾客信息

```
mysql> select cust_name, cust_contact
    -> from Customers
    -> where cust_id in (select cust_id
    ->                    from Orders
    ->                    where order_num in(select order_num
    ->                                        from Orderitems
    ->                                        where prod_id = 'RGAN01'));
+---------------+--------------------+
| cust_name     | cust_contact       |
+---------------+--------------------+
| Fun4All       | Denise L. Stephens |
| The Toy Store | Kim Howard         |
+---------------+--------------------+
2 rows in set (0.10 sec)
```
如以上步骤所示，其中每一个步骤就是一个子查询。依次是从最内层的子查询到最外层。父子查询使用子子查询的返回的结果集。

## 4、使用计算字段作为子查询
需求：显示customers表中每个顾客的订单总数。
步骤：
从customers表中检索出顾客列表
对于检索出的每个顾客，统计其在orders表中的订单数目。
```
mysql> select cust_name,cust_state,
    ->       (select count(*) from orders
    ->       where orders.cust_id = customers.cust_id) as orders
    -> from customers
    -> order by cust_name;
+---------------+------------+--------+
| cust_name     | cust_state | orders |
+---------------+------------+--------+
| Fun4All       | IN         |      1 |
| Fun4All       | AZ         |      1 |
| Kids Place    | OH         |      0 |
| The Toy Store | IL         |      1 |
| Village Toys  | MI         |      2 |
+---------------+------------+--------+
5 rows in set (0.13 sec)
```

## 5、组合查询
将多个select 查询语句的结果合并，作为一个查询的结果集返回。

**两种需要使用组合查询的情景**

 1. 在一个查询从不同的表返回结果数据
 2. 对一个表执行多个查询，按一个查询返回数据。

**使用union**
union关键字是将所有结果合并到一起，然后去除相同的记录。而union all 只是简单的将结果合并到一起。
```
mysql> select cust_name, cust_contact, cust_email
    -> from customers
    -> where cust_state in ('IL','IN','MI');
+---------------+--------------+-----------------------+
| cust_name     | cust_contact | cust_email            |
+---------------+--------------+-----------------------+
| Village Toys  | John Smith   | sales@villagetoys.com |
| Fun4All       | Jim Jones    | jjones@fun4all.com    |
| The Toy Store | Kim Howard   | NULL                  |
+---------------+--------------+-----------------------+
3 rows in set (0.08 sec)
```
```
mysql> select cust_name, cust_contact, cust_email
    -> from customers
    -> where cust_name = 'Fun4All';
+-----------+--------------------+-----------------------+
| cust_name | cust_contact       | cust_email            |
+-----------+--------------------+-----------------------+
| Fun4All   | Jim Jones          | jjones@fun4all.com    |
| Fun4All   | Denise L. Stephens | dstephens@fun4all.com |
+-----------+--------------------+-----------------------+
2 rows in set (0.01 sec)
```
合并以上两个查询的结果
```
mysql> select cust_name, cust_contact, cust_email
    -> from customers
    -> where cust_state in ('IL','IN','MI')
    -> union
    -> select cust_name, cust_contact, cust_email
    -> from customers
    -> where cust_name = 'Fun4All';
+---------------+--------------------+-----------------------+
| cust_name     | cust_contact       | cust_email            |
+---------------+--------------------+-----------------------+
| Village Toys  | John Smith         | sales@villagetoys.com |
| Fun4All       | Jim Jones          | jjones@fun4all.com    |
| The Toy Store | Kim Howard         | NULL                  |
| Fun4All       | Denise L. Stephens | dstephens@fun4all.com |
+---------------+--------------------+-----------------------+
4 rows in set (0.05 sec)
```
使用union会去除重复的行，以上结果以表明这一点。
要想不去除重复的行，可使用 union all 关键字。
```
mysql> select cust_name, cust_contact, cust_email
    -> from customers
    -> where cust_state in ('IL','IN','MI')
    -> union all
    -> select cust_name, cust_contact, cust_email
    -> from customers
    -> where cust_name = 'Fun4All';
+---------------+--------------------+-----------------------+
| cust_name     | cust_contact       | cust_email            |
+---------------+--------------------+-----------------------+
| Village Toys  | John Smith         | sales@villagetoys.com |
| Fun4All       | Jim Jones          | jjones@fun4all.com    |
| The Toy Store | Kim Howard         | NULL                  |
| Fun4All       | Jim Jones          | jjones@fun4all.com    |
| Fun4All       | Denise L. Stephens | dstephens@fun4all.com |
+---------------+--------------------+-----------------------+
5 rows in set (0.05 sec)
```
**对组合查询的结果进行排序**
只能使用一条order by 字句，且位于最后一条 select 语句最后，但却是对所有结果集进行排序。
```
mysql> select cust_name, cust_contact, cust_email
    -> from customers
    -> where cust_state in ('IL','IN','MI')
    -> union
    -> select cust_name, cust_contact, cust_email
    -> from customers
    -> where cust_name = 'Fun4All'
    -> order by cust_name, cust_contact;
+---------------+--------------------+-----------------------+
| cust_name     | cust_contact       | cust_email            |
+---------------+--------------------+-----------------------+
| Fun4All       | Denise L. Stephens | dstephens@fun4all.com |
| Fun4All       | Jim Jones          | jjones@fun4all.com    |
| The Toy Store | Kim Howard         | NULL                  |
| Village Toys  | John Smith         | sales@villagetoys.com |
+---------------+--------------------+-----------------------+
4 rows in set (0.00 sec)
```
