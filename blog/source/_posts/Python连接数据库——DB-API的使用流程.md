---
title: Python连接数据库——DB-API的使用流程
date: 2018-05-24 08:26:30
tags: 随笔
toc: true
---

# 一、Python中的BD-API
## 1、出现背景
**在没有DB-API之前，接口程序混乱。具体的就是说，由于最底层用的数据库技术不同，所以在应用程序层就要针对特定的数据库进行特定的编码，如果要改变一个版本所使用的底层数据库，那么之前编写的应用程序中关于数据库的代码也要进行相应的改变。**
![这里写图片描述](https://img-blog.csdn.net/20180523200334503?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzk3MjcyMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![这里写图片描述](https://img-blog.csdn.net/20180523200252874?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzk3MjcyMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

## 2、详解
python DB-API：python访问数据库的统一接口规范。

<!-- more -->

![这里写图片描述](https://img-blog.csdn.net/20180523200549150?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzk3MjcyMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

这里有一个约定：程序员使用python与任何底层数据库交互时都使用DB-API，而不论这个数据库技术具体是什么，这么做是因为，利用驱动程序，程序员无需了解与数据库具体API交互的底层细节，因为DB-API在代码与驱动程序之间提供了一个抽象层，这里的想法是：通过使用DB-API编程，可以根据需要替换掉底层数据库技术，而无须丢弃现有的代码。

## 3、python DB-API包含的内容
![这里写图片描述](https://img-blog.csdn.net/2018052320200844?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzk3MjcyMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

## 4、使用python DB-API访问数据库的流程
![这里写图片描述](https://img-blog.csdn.net/20180523202143975?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzk3MjcyMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

下面分步骤详解：

 - 定义连接属性
 连接到MySQL服务器时需要4部分信息： 运行MySQL服务器的计算机（称为主机）的IP地址/主机名；要使用的用户ID；与用户ID关联的口令；这一用户ID想要交互的数据库名。
	 ![这里写图片描述](https://img-blog.csdn.net/20180523202933442?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzk3MjcyMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
```
dbconfig = {
	"host": "127.0.0.1",
	"user": "vsearch",
	"password": "vseacrh",
	"database": vsearchlogDB
}
```
 -  导入数据库驱动程序
 ```
 import mysql.connector
 ```
 
 - 建立与MySQL服务器的一个连接
 ```
 conn = mysql.connector.connect(**dbconfig)
 ```
(**)记法告诉connect函数用一个变量提供了参数字典，如果看到这种记法，connect函数会把这个字典展开为4个单独的参数，然后在connect函数中使用这些参数来建立连接。
 -  打开一个游标
 要向数据库发送SQL命令（通过刚才打开的连接）以及从数据库接收结果，我们需要一个游标，可以把游标理解成数据库中文件的句柄。
 创建游标很简单：只需要调用cursor方法，每个连接对象都有这个方法。
前面我们所做的这些操作，在无论使用什么数据库时，都是通用的。只是连接属性可能不同而已。

![这里写图片描述](https://img-blog.csdn.net/20180523204650358?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzk3MjcyMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![这里写图片描述](https://img-blog.csdn.net/20180523204704665?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzk3MjcyMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/——dissolve/70)

 - 完成SQL查询
 可以利用cursor变量向MySQL发送SQL查询，以及获取MySQL处理查询生成的结果，我认为的是这个跟ajax中的request请求对象很像。
```
_SQL = """show tables"""
cursor.execute(_SQL);
```
一般用三重双引号来编写要发送给数据库服务器的SQL,之所以使用三重双引号，是因为三重双引号可以暂定不启用python解释器中的“行末即语句结束的规则”。
执行cursor.execute()命令时，这个SQL查询会被发送到MySQL的服务器，它会执行这个查询（假设正确），不过结果并不会立即显示，必须要请求结果。
![这里写图片描述](https://img-blog.csdn.net/2018052323404291?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzk3MjcyMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![这里写图片描述](https://img-blog.csdn.net/2018052323421230?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzk3MjcyMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

这里要特别注意点是：fetchall()方法，它返回的是客户端缓冲区现在还没有被影响的记录。并且返回的数据类型是一个元组列表。

![这里写图片描述](https://img-blog.csdn.net/20180523234627100?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzk3MjcyMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![这里写图片描述](https://img-blog.csdn.net/20180523234636782?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzk3MjcyMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

当使用insert操作时，由于写数据库是一个开销很大的操作，所以数据库系统会缓存insert，然后再一次性应用全部的insert。这带来的问题就是，刚刚插入的数据可能访问不到。但是可以通过事务提交（conn.commit()）来强制插入数据到数据库，这样既可解决访问不到的问题。
![这里写图片描述](https://img-blog.csdn.net/20180523235145173?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzk3MjcyMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

 - 关闭游标和连接
 数据提交到数据库表中后，要进行清理，关闭游标和连接。
 ```
 cursor.close()
 conn.close()
 ```
 这是因为数据库系统是一组有限的资源，不关闭会导致资源严重浪费，其它用户无法访问。

下面是最近做的一个项目中关于数据库连接的源码：
```
dbconfig = {"host": "127.0.0.1",
			"user": "vsearch",
			"password": "vsearch",
			"database": "vsearchlogDB",
}

import mysql.connector

conn = mysql.connector.connect(**dbconfig)
cursor = conn.cursor()

_SQL = """insert into log
		  (phrase, letters, ip, browser_string, results)
		  values
		  (%s, %s, %s, %s, %s)"""

cursor.execute(_SQL, ("afeng", "jingjing", "127.0.0.1", "opera", "{'x', 'y'}"))

conn.commit()

_SQL = """select * from log"""
cursor.execute(_SQL)

for row in cursor.fetchall():
	print(row)

cursor.close()
conn.close()
```

# 声明这个笔记主要是根据head first python以及慕课网的Python操作MySQL数据库课程总结而来。
<https://www.imooc.com/learn/475>