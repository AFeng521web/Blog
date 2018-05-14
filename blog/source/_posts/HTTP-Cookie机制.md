---
title: HTTP--Cookie机制
date: 2018-05-14 19:31:42
tags: 随笔
---

# 一、概述
**Cookie是服务器保存在浏览器的一小段文本信息，每个Cookie的大小一般不超过4KB，浏览器每次向服务器发出请求时，就会自动附上这段信息。**

**Cookie主要用来分辨两个请求是否来自同一个用户，以及用来保存一些状态信息**，
它的常用场合主要有：
----------

 1. 会话管理：保存登录、购物车等需要记录的信息。
 2. 个性化：保存用户的偏好，比如网页的字体大小，背景色。
 3. 追踪：记录和分析用户的行为
 
----------
# 二、Cookie的工作过程

----------

 4. 当用户在浏览器发送需要被记住的信息时，服务器就会在Set-Cookie字段生成Cookie信息，然后发送到客户端，客户端会把这些信息保存下来。
 5. 当浏览器再次访问这个域名时，浏览器首先会读取本地的Cookie信息。
 6. 然后把Cookie信息通过Cookie字段发送到服务器。
 7. 服务器验证Cookie信息。
 8. 响应给客户端。
 9. 用户看到已登录的状态。
 
----------

<!-- more -->

## Cookie包含的信息
```
Cookie的名字
Cookie的值（真正的数据写在这 里面）
到期时间
所属域名（默认是当前域名）
生效的路径（默认是当前网址）

```

# 三、Cookie是属性

**1、Expires,Max-Age:都是指定Cookie的过期时间**
Expires属性指定了一个具体的到期时间，到了这个指定的时间以后，浏览器就不在保留这个Cookie.
```
Set-Cookie: id=Afeng;  Expires=Wed, 21 Oct 2018 07:28:00 GMT;
```
如果不设置该属性，或者设为null，Cookie只在当前会话（session）有效，浏览器一旦关闭，该Cookie就会被删除。

Max-Age属性指定从现在开始Cookie存在的秒数，过了这个时间，浏览器就不在保留这个Cookie。

**如果同时指定了Expires和Max-Age,那么Max-Age的值优先生效。**
  
**2、Domain, Path**
**domain是域名，path是路径，两者加起来就构成了 URL，domain和path一起来限制 cookie 能被哪些 URL 访问。**

**3、Secure, HttpOnly**
**Secure属性指定浏览器只有在加密协议 HTTPS 下，才能将这个 Cookie 发送到服务器。**

cookie不会带secure选项(即为空)。所以默认情况下，不管是HTTPS协议还是HTTP协议的请求，cookie 都会被发送至服务端。但要注意一点，secure选项只是限定了在安全情况下才可以传输给服务端，但并不代表你不能看到这个 cookie。

HttpOnly属性指定该 Cookie 无法通过 JavaScript 脚本拿到，主要是Document.cookie属性、XMLHttpRequest对象和 Request API 都拿不到该属性。这样就防止了该 Cookie 被脚本读到，只有浏览器发出 HTTP 请求时，才会带上该 Cookie。

# 四、Cookie的一些特殊属性
 document.Cookie属性用于**读写**当前网页的Cookie
**读取的时候，它会返回当前网页的所有Cookie，但写入的时候一次只能写入一个Cookie.而且写入并不是覆盖，而是添加。**

document.cookie读写行为的差异（一次可以读出全部 Cookie，但是只能写入一个 Cookie），与 HTTP 协议的 Cookie 通信格式有关。浏览器向服务器发送 Cookie 的时候，Cookie字段是使用一行将所有 Cookie 全部发送；服务器向浏览器设置 Cookie 的时候，Set-Cookie字段是一行设置一个 Cookie。

**window.navigator.cookieEnabled属性返回一个布尔值，表示浏览器是否打开Cookie功能。**
```
window.navigator.cookieEnabled = true //浏览器开启Cookie功能
```
# 五、Cookie的操作
修改Cookie:
要想修改一个Cookie,只需重新赋值就可以了，但是path/domain这几个选项都要保持不变。

删除Cookie:
要删除一个Cookie,只需将Cookie的过期时间设置为一个过去的时间。


# 六、特殊的Cookie用法——session会话管理

----------

 - 首先访问这个网站的时候，服务器通过Set-Cookie给我们分配一个sessionID(一个32位的16进制编码)；
 
 - 这样，我们客户端就有了一个唯一的身份标识；

 - 并且在服务器的临时文件下生成了一个以sessionID命名的文本文件，然后把sessionID的值保存在里面；
 - 这样浏览器下次访问的时候，就把这个sessionID加在Cookie字段中发送给服务器。
 

----------

 **那么通过sessionID,文本文件，以及文本文件里面的内容这三种共同构成了一种状态保持的机制。**
 像淘宝这样的多用户网站，如果都将sessionID文件存储在临时文件夹中，访问的时候，会造成性能问题，应将sessionID存储在内存，或者关系型数据库中。
 
 参考链接
 <https://javascript.ruanyifeng.com/bom/cookie.html>
 <https://segmentfault.com/a/1190000004556040>

 