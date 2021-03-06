---
title: 深入理解javascript函数系列第四篇——ES6函数扩展
date: 2018-05-15 16:51:56
tags: 随笔
---

# 前面的话
## ES6标准关于函数部分的扩展，主要涉及以下四个方面：参数默认值、rest参数、扩展运算符和箭头函数。

# 一、参数默认值
一般地，为参数设置默认值进行如下设置。
```
function log(x, y) {
  y = y || 'World';
  console.log(x, y);
}
```
但这样设置实际上是有问题的，如果y的值本身是假值(包括false、undefined、null、''、0、-0、NaN)，则无法取得本身值。

<!-- more -->
```
function log(x, y) {
  y = y || 'World';
  console.log(x, y);
}
log('Hello') // Hello World
log('Hello', 'China') // Hello China
log('Hello', NaN) // Hello World
```
ES6允许为函数的参数设置默认值，即直接写在参数定义的后面。
```
function log(x, y = 'World') {
  console.log(x, y);
}
log('Hello') // Hello World
log('Hello', 'China') // Hello China
log('Hello', NaN) // Hello NaN
```
[注意点]：参数变量是默认声明的，所以不能再使用let或const再次声明。
```
function foo(x = 5) {
	//但是默认是使用什么关键字进行声明的
  let x = 1; //SyntaxError: Identifier 'x' has already been declared
  const x = 2; //SyntaxError: Identifier 'x' has already been declared
}
```
尾参数
通常情况下，定义了默认值的参数，应该是函数的尾参数。因为这样比较容易看出来，到底省略了那些参数。如果非尾部的参数设置默认值，实际上这个参数是没法省略的。
```
function f(x = 1, y) {
  return [x, y];
}
f() // [1, undefined]
f(2) // [2, undefined])
f(, 1) // 报错
f(undefined, 1) // [1, 1]
```
如果传入的是undefined，将触发该参数的默认值，null则没有这个效果。
```
function foo(x = 5, y = 6) {
  console.log(x, y);
}
foo(undefined, null)// 5 null
```
length：指定了默认值以后，函数的length属性，将返回没有指定默认值的参数的个数。
```
(function (a) {}).length // 1
(function (a = 5) {}).length // 0
(function (a, b, c = 5) {}).length // 2
```
[注意点]：如果设置了默认值的参数不是尾参数，那么length属性也不再后面的参数了。
```
(function (a = 0, b, c) {}).length // 0
(function (a, b = 1, c) {}).length // 1
```
## 作用域
如果参数的默认值是一个变量，则变量所处的作用域，与其它变量的作用域规则是一样的，即先是当前函数的作用域，然后才是全局作用域。
```
var x = 1;
function f(x, y = x) {
  console.log(y);
}
f(2) // 2
```
如果函数调用时，函数作用域内部的变量x没有生成，则x指向全局变量。
```
var x = 1;
function f(y = x) {
	//调用时x的值还没有被赋予2
  var x = 2;
  console.log(y);
}
f() // 1
```
## 应用
利用参数默认值，可以指定某一参数不得省略，如果省略就抛出一个错误。
```
function throwIfMissing() {
  throw new Error('Missing parameter');
}
function foo(mustBeProvided = throwIfMissing()) {
  return mustBeProvided;
}
foo()// Error: Missing parameter
```
[注意点]：将参数默认值设置为undefined，表明这个参数可以省略。
```
function foo(optional = undefined) {
    //todo
}
```

# 二、rest参数
ES6引入rest参数（形式为“...变量名”），用于获取函数的多余参数，这样就不在使用arguments对象了，rest参数搭配的变量是一个数组，该变量将多余的参数放入这个数组中。
```
function add(...values) {
  var sum = 0;
  //val代表接收值的变量，values被迭代枚举其属性的对象。
  for (var val of values) {
    sum += val;
  }
  return sum;
}
add(2, 5, 3) //10
```
rest参数中的变量代表一个数组，所以数组特有的方法都可以用于这个变量。
用rest参数改写数组push方法的例子
```
function push(array, ...items) {
  items.forEach(function(i) {
	//这里的i代表的是items中的每一内容
    array.push(i);
    console.log(i);
  });
}
var a = [];
push(a, 1, 2, 3);
```
函数的length属性不包括rest参数
```
(function(a) {}).length  // 1
(function(...a) {}).length  // 0
(function(a, ...b) {}).length  // 1
```
[注意点]：rest参数之后不能再有其余参数
```
//Uncaught SyntaxError: Rest parameter must be last formal parameter
function f(a, ...b, c) {
  //todo
}
```
# 三、扩展运算符
扩展运算符（spread）是三个点（...）。它好比rest参数的逆运算，用来将一个数组转化为用逗号分隔的参数序列。
```
console.log(...[1, 2, 3])// 1 2 3
console.log(1, ...[2, 3, 4], 5)// 1 2 3 4 5
[...document.querySelectorAll('div')]// [<div>, <div>, <div>]
```
该运算符主要用于函数调用
```
function add(x, y) {
  return x + y;
}
var numbers = [4, 38];
//将一个数组当函数的实参传递
add(...numbers) // 42
```
Math.max()方法简化
```
// ES5
Math.max.apply(null, [14, 3, 77])

// ES6
Math.max(...[14, 3, 77])

//等同于
Math.max(14, 3, 77)
```
push方法简化
```
// ES5
var arr1 = [0, 1, 2];
var arr2 = [3, 4, 5];
//在这里push方法是基于arr1调用的，所以相当于把arr2添加到arr1中
Array.prototype.push.apply(arr1, arr2);

// ES6
var arr1 = [0, 1, 2];
var arr2 = [3, 4, 5];
arr1.push(...arr2);
```
扩展运算符可以将字符串转化为真正的数组。
```
[...'hello']// [ "h", "e", "l", "l", "o" ] //对于这一点还是有很多不理解
```

转载自：小火柴的蓝色理想
<http://www.cnblogs.com/xiaohuochai/p/5613593.html>
在阅读老师的博客时，我把它当做看书来对待，所以我的博客大部分来自老师的原话以及实例，再加上自已的一些理解，并且做了适当的标注，还有对老师的代码进行了一些测试。