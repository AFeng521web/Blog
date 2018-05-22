---
title: 深入理解javascript函数系列第六篇——高阶函数
date: 2018-05-16 09:15:28
tags: 随笔
toc: true
---

# 一、定义
## 高阶函数（Higher-order function）：javascript中的函数其实都是指向某个变量，既然变量可以指向函数（用一个变量表示函数），函数的参数能接收变量，那么一个函数就可以接受另一个函数作为参数，这种函数就叫做高阶函数，也就是操作函数的函数，有以下两种情况：

 - 函数可以作为参数被传递.
 - 函数可以作为返回值输出.

## javascript中的函数显示满足高阶函数的条件，在实际开发的，无论是将函数当做参数传递，还是让函数的执行返回另外一个函数。这两种情形都有许多应用场景。下面分别详细介绍。

# 二、函数当做参数传递
把函数当做参数传递，代表可以抽离出一部分容易变化的业务逻辑，把这部分业务逻辑放在函数参数中，这样一来可以把业务代码中变化的部分和不变的部分分离开。其中最常见的一个应用场景就是回调函数。

<!-- more -->

## 【回调函数】
在ajax异步请求的应用中，回调函数的使用非常频繁。想在ajax请求返回之后做一些事情，但又并不知道请求返回的确切时间时，最常见的方案就是把callback函数当作参数传入发起ajax请求的方法中，待请求完成之后执行callback函数。

```
var getUserInfo = function( userId, callback ){
  $.ajax( 'http://xx.com/getUserInfo?' + userId, function( data ){
    if ( typeof callback === 'function' ){
      callback( data );
    }
  });
}
getUserInfo( 123, function( data ){ 
  alert ( data.userName );
});
```
回调函数的应用不仅只在异步请求中，当一个函数不适合执行一些请求时，也可以把这些请求封装成一个函数，并把它作为参数传递给另一个函数，委托另一个函数来执行。
比如：想在页面上创建100个div节点，然后把这些div节点设置为隐藏的，下面是一种编写代码的方式。
```
var appendDiv = function(){
  for ( var i = 0; i < 100; i++ ){
    var div = document.createElement( 'div' );
    div.innerHTML = i;
    document.body.appendChild( div );
    div.style.display = 'none';
  }
};
appendDiv();
```
把div.style.display = "none"的逻辑硬编码在appendDiv中显示是不合理的，appendDiv的功能太过多样，成为一个难以复用的函数，并不是每个人创建了节点之后都会选择立刻将它隐藏。

于是把div.style.display = "none"这行代码抽取出来，封装成一个函数，用回调函数的形式传入appendDiv()方法。
```
 var appendDiv = function(callback) {
        for(var i=0; i<100; i++) {
            var div = document.createElement("div");
            div.innerHTML = i;
            document.body.appendChild(div);
            if(typeof callback == "function") {
                callback(div);
            }
        }
    };

 appendDiv(function(node) {
        node.style.display  = "none";
 });
```
可以看到，隐藏节点的请求实际上是由客户端发起的，但是客户并不知道节点什么时候会创建好，于是把隐藏节点的逻辑放在回调函数中。“委托”给appendDiv()方法，appendDiv()方法当然知道节点什么创建好，所以在节点创建好的时候，appendDiv()会执行之前用户传入的回调函数。

## 【数组排序】
函数作为参数传递的另一个常见的场景是数组排序函数的sort()。Array.prototype.sort接受一个函数当做参数，这个函数定义了数组元素排序的规则（正序还是逆序）。目的是对数组进行排序，这是不变的部分；而使用什么规则进行排序，则是可变的部分。把可变的部分封装到函数里面，动态传入Array.prototype.sort，使得Array.prototype.sort可以灵活的被我们控制。
```
// 从小到大排列，输出: [ 1, 3, 4 ]
[ 1, 4, 3 ].sort( function( a, b ){ 
  return a - b;
});

// 从大到小排列，输出: [ 4, 3, 1 ]
[ 1, 4, 3 ].sort( function( a, b ){ 
  return b - a;
});
```
# 三、函数当做返回值输出
相比把函数当做参数传递，函数当做返回值输出应用场景也很多，让函数继续返回一个可执行的函数，意味着运算过程是可持续的。
下面是使用Object,prototype.toString方法判断数据类型的一系列的isType函数。
```
var isString = function( obj ){
  return Object.prototype.toString.call( obj ) === '[object String]';
};
var isArray = function( obj ){
  return Object.prototype.toString.call( obj ) === '[object Array]';
};
var isNumber = function( obj ){
  return Object.prototype.toString.call( obj ) === '[object Number]';
};
```
实际上，这些函数的大部分实现都是相同的，不同的是Object.prototype.toString.call(obj)返回的字符串。为了避免多余的代码，可以把这些字符串作为参数提前传入isType函数。代码如下：
```
var isType = function( type ){ 
  return function( obj ){
    return Object.prototype.toString.call( obj ) === '[object '+ type +']';
  }
};

var isString = isType( 'String' ); //调用这个函数返回的是内层函数
var isArray = isType( 'Array' ); 
var isNumber = isType( 'Number' );

console.log( isArray( [ 1, 2, 3 ] ) );    // 输出：true
```
当然，还可以使用循环语句，来批量注册这些函数。
```
 var Type = {};
   for(var i=0, type; type = ['String', 'Array', 'Number' ][i++];) {
       (function(type) {
           Type['is' + type] = function(obj) {
               return Object.prototype.toString.call(obj) == "[object " + type +"]";
           }
       })(type)
   }
 
console.log(Type.isArray([])); //true
console.log(Type.isString("str")); //true
```

转载自：小火柴的蓝色理想
<http://www.cnblogs.com/xiaohuochai/p/5613593.html>
在阅读老师的博客时，我把它当做看书来对待，所以我的博客大部分来自老师的原话以及实例，再加上自已的一些理解，并且做了适当的标注，还有对老师的代码进行了一些测试。