---
title: HTTP缓存
date: 2018-05-14 19:27:04
tags: 随笔
---

# 一、缓存的概念
HTTP协议提供了非常强大的缓存，了解这些缓存机制对提高网站性能非常有帮助。缓存无处不在，有浏览器缓存，有服务器缓存，有代理服务器缓存，对象缓存，数据库缓存。

**HTTP中具有的缓存是浏览器缓存，以及代理服务器缓存。**

**HTTP缓存是指：当Web请求到达缓存时，如果本地有“已缓存”的副本，并且还没有过期，就可以直接从本地存储设备读取，而不是去原始服务器提取这个文档。**


# 二、缓存的好处：

 1. 减少冗余的数据传输，节省了网费。
 2. 减少了服务器的负担，大大提高了网站的性能。
 3. 加快了客户端用户加载网页的速度。

<!-- more -->
# 三、如何判断缓存的新鲜度
**web服务器通过2种方式来判读浏览器缓存是否是最新的。**

**第一种：浏览器把缓存文件的最后修改时间通过header "if-Modified-Since"来告诉Web服务器。**

处理机制：

 1. 浏览器客户端想请求一个文档，首先检查本地缓存，发现存在这个文档的缓存，获取缓存文档的最后修改时间，通过：if-Modified-Since，发送request给Web服务器。
 2. Web服务器受到request,将服务器的文档修改时间（last-Modified）:跟request header中的，if-Modified-Since相比较，如果**小于或者等于**，说明服务器端缓存没有发生更改，也就是说客户端缓存还是可用的，Web服务器将发送304 Not Modified给浏览器客户端，告诉客户端直接使用本地缓存版本。
 
 ![这里写图片描述](https://img-blog.csdn.net/20180405133936197?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzk3MjcyMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

假设last-Modified的值**大于**了if-Modified-Since的值，说明浏览器缓存后，服务器端的文档修改过了，此时就不能直接使用缓存，那么Web服务器将发送该文档的最新版本给浏览器客户端。


![这里写图片描述](https://img-blog.csdn.net/20180405134253483?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzk3MjcyMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

如果同时存在cache-control和Expires时；浏览器总是优先使用cache-control,然后才会考虑使用Expires;

**第二种：浏览器把缓存文件的ETag，通过header "If-None-Match"告诉Web服务器。**
ETag是实体标签（Entity Tag） 的缩写，根据实体内容生成的一段hash字符串，可以标识实体资源的状态，当资源发生改变时，ETag也随之发生变化。

ETag是由Web服务器生成的，然后发送给浏览器客户端。
为什么要使用ETag？
主要是为了解决Last-Modified无法解决的问题
1、某些服务器不能精确得到文件的最后修改时间，所以无法通过最后修改时间判断资源是否是最新的。
2、某些文件修改非常频繁，在秒以下的时间内进行修改，Last-Modified只能精确到秒。
3、一些文件的最后修改时间改变了，但是内容并未发生改变。

**处理机制：**

1.浏览器把本地缓存文件的ETag值加在if-None-Match中发送到服务器
2.服务器将if-None-Match的值和服务器端资源的ETag值比较，若两个相等，说明文件没有更新。
3.服务器返回304（Not Modified）,告诉客户端使用本地缓存。

## 直接使用缓存，不去服务器端验证
按F5刷新浏览器和在地址栏输入网址然后回车，这两个行为是不一样的。
按F5刷新浏览器，浏览器回去Web服务器验证缓存。
如果是在地址栏输入网址然后回车，浏览器会直接使用有效缓存，而不会发送Http request去服务器验证缓存。这种情况叫做缓存命中。