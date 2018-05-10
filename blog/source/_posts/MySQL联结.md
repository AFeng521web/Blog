---
title: MySQL联结
date: 2018-05-11 00:18:30
tags: 随笔
---

# 一、连接表
 SQL最强大的功能之一就是能在数据检索查询的执行中联结（join）表，联结是利用SQL的select 能执行的最重要的操作。
 
在说联结之前，我们先看关系型数据库的设计。
![这里写图片描述](https://img-blog.csdn.net/20180408161535550?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzk3MjcyMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**通过主键和外键来建立表之间的联系，维护表间引用的完整性，外键相当于一个指针，指向另一个表的主键**。

## 1、使用关系型数据库存储数据的好处（以存储供应商的产品和供应商的信息为例）

同一供应商生产的多个产品，其供应商信息都相同，对于每个产品重复存储此信息既浪费时间又浪费空间。
如果供应商的信息发生变化，只需修改一次即可。
由于数据不重复，数据显然是一致的，使得处理数据和生成报表更简单。

<!-- more -->

## 2、为什么使用联结表

如果数据存储在多个表中，怎样才能用一条 select 语句检索出数据呢？ 答案就是使用联结，重要的是要理解联结是一种机制，用来在一条 select 语句中关联表，不是物理实体，它在实际的数据库表中并不存在，DBMS会根据需要建立联结，它会在查询期间一直存在。

**完全限定列名**
在引用的列可能产生歧义时，必须使用完全限定列名（**数据表名.列名**）

**where字句的重要性**
select 语句中联结几个表时，相应的关系是在运行中构造的，在数据表的定义中，不存在指示MySQL如何对表进行联结的东西，必须自己做这件事情。在联结两个表时，实际上做的就是将第一个表中的每一行与第二个表中的每一行进行配对，然后使用where 字句进行过滤，过滤出只包含匹配给定条件（联结条件）的行。

**联结原理：**
产生笛卡尔积，然后利用条件去过滤不需要的行。

## 3、联结的分类
**内联结（等值联结），它是基于两个表之间的相等测试。**
![这里写图片描述](https://img-blog.csdn.net/20180408173813165?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzk3MjcyMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
```
mysql> select vend_name, prod_name, prod_price
    -> from vendors inner join products
    -> on vendors.vend_id = products.vend_id;
+-----------------+---------------------+------------+
| vend_name       | prod_name           | prod_price |
+-----------------+---------------------+------------+
| Bears R Us      | 8 inch teddy bear   |       5.99 |
| Bears R Us      | 12 inch teddy bear  |       8.99 |
| Bears R Us      | 18 inch teddy bear  |      11.99 |
| Doll House Inc. | Fish bean bag toy   |       3.49 |
| Doll House Inc. | Bird bean bag toy   |       3.49 |
| Doll House Inc. | Rabbit bean bag toy |       3.49 |
| Doll House Inc. | Raggedy Ann         |       4.99 |
| Fun and Games   | King doll           |       9.49 |
| Fun and Games   | Queen doll          |       9.49 |
+-----------------+---------------------+------------+
9 rows in set (0.14 sec)
```
明确指定了联结类型（inner join）,所以联结条件就要用 on 指定。
```
mysql> select vend_name, prod_name, prod_price
    -> from vendors, products
    -> where vendors.vend_id = products.vend_id;
+-----------------+---------------------+------------+
| vend_name       | prod_name           | prod_price |
+-----------------+---------------------+------------+
| Bears R Us      | 8 inch teddy bear   |       5.99 |
| Bears R Us      | 12 inch teddy bear  |       8.99 |
| Bears R Us      | 18 inch teddy bear  |      11.99 |
| Doll House Inc. | Fish bean bag toy   |       3.49 |
| Doll House Inc. | Bird bean bag toy   |       3.49 |
| Doll House Inc. | Rabbit bean bag toy |       3.49 |
| Doll House Inc. | Raggedy Ann         |       4.99 |
| Fun and Games   | King doll           |       9.49 |
| Fun and Games   | Queen doll          |       9.49 |
+-----------------+---------------------+------------+
9 rows in set (0.00 sec)
```
## 4、创建高级联结
**自联结：就是自己联结自己的操作。**
需求：如要给与Jim Jones同一公司的所有顾客发送一封信件。
先找公司，在找所有顾客。
利用子查询解决
```
mysql> select cust_id,cust_name,cust_contact
    -> from customers
    -> where cust_name = (select cust_name
    ->                   from customers
    ->                   where cust_contact = 'Jim Jones');
+------------+-----------+--------------------+
| cust_id    | cust_name | cust_contact       |
+------------+-----------+--------------------+
| 1000000003 | Fun4All   | Jim Jones          |
| 1000000004 | Fun4All   | Denise L. Stephens |
+------------+-----------+--------------------+
2 rows in set (0.00 sec)
```
利用联结
```
mysql> select c1.cust_id, c1.cust_name, c1.cust_contact
    -> from customers as c1, customers as c2
    -> where c1.cust_name = c2.cust_name
    -> and c2.cust_contact = 'Jim jones';
+------------+-----------+--------------------+
| cust_id    | cust_name | cust_contact       |
+------------+-----------+--------------------+
| 1000000003 | Fun4All   | Jim Jones          |
| 1000000004 | Fun4All   | Denise L. Stephens |
+------------+-----------+--------------------+
2 rows in set (0.00 sec)
```
**自然联结**
无论何时对表进行联结，应该至少有一列不止出现在一个表中（联结的列），标准联结返回所有数据，相同的列甚至多次出现，自然联结排除多次出现，使每一列只返回一次。
```
mysql> select c.*,o.order_num, o.order_date,
    -> oi.prod_id,oi.quantity,oi.item_price
    -> from customers as c, orders as o, orderitems as oi
    -> where c.cust_id = o.cust_id
    -> and oi.order_num = o.order_num
    -> and prod_id = 'RGAN0l';
Empty set (0.00 sec)
```

**外联结**
适用场合
对每个顾客下的订单进行计数，包括哪些至今尚未下订单的顾客。
列出所有产品以及订购数量，包括没有人订购的产品。

![这里写图片描述](https://img-blog.csdn.net/20180408173916147?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzk3MjcyMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
####<center>左外连接

![这里写图片描述](https://img-blog.csdn.net/2018040817394891?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzk3MjcyMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
####<center>右外连接
```
mysql> select customers.cust_id, orders.order_num
    -> from customers inner join orders
    -> on customers.cust_id = orders.cust_id;
+------------+-----------+
| cust_id    | order_num |
+------------+-----------+
| 1000000001 |     20005 |
| 1000000001 |     20009 |
| 1000000003 |     20006 |
| 1000000004 |     20007 |
| 1000000005 |     20008 |
+------------+-----------+
5 rows in set (0.05 sec)
```
将内联结改成左外联结，使其包含没有订单顾客在内的所有顾客。
```
mysql> select customers.cust_id, orders.order_num
    -> from customers left outer join orders
    -> on customers.cust_id = orders.cust_id;
+------------+-----------+
| cust_id    | order_num |
+------------+-----------+
| 1000000001 |     20005 |
| 1000000001 |     20009 |
| 1000000002 |      NULL |
| 1000000003 |     20006 |
| 1000000004 |     20007 |
| 1000000005 |     20008 |
+------------+-----------+
6 rows in set (0.05 sec)
```
左外联结和右外联结可以互换
**只要把右表移到左边，把左表移到右边，就可以实现使用left outer join 实现右外联结的功能，这种做法在不支持右外联结时，可以做到有效的处理。**


**使用带聚集函数的联结**
聚集函数总是用来汇总数据的
检索所有顾客以及每个顾客所下的订单数
```
mysql> select customers.cust_id,
    -> count(orders.order_num) as num_ord
    -> from customers inner join orders
    -> on customers.cust_id = orders.cust_id
    -> group by customers.cust_id;
+------------+---------+
| cust_id    | num_ord |
+------------+---------+
| 1000000001 |       2 |
| 1000000003 |       1 |
| 1000000004 |       1 |
| 1000000005 |       1 |
+------------+---------+
4 rows in set (0.05 sec)
```
使用的是内联结，所以只有同时满足两个表的条件才会被筛选出来。