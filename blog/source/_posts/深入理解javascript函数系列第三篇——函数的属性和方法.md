---
title: 深入理解javascript函数系列第三篇——函数的属性和方法
date: 2018-05-15 16:51:26
tags: 随笔
---

# 前面的话
## 函数是javascript中特殊的对象，可以拥有属性和方法，就像普通对象拥有属性和方法一样。甚至可以用Function()构造函数来创建新的函数对象。

# 一、属性
## 【length属性】：arguments对象的length属性表示实参的个数，而函数的length属性则表示形参的个数。
```
function add(x,y){
    console.log(arguments.length)//3
    console.log(add.length);//2
}
add(1,2,3);
```
<!-- more -->
## 【name属性】：函数定义了一个非标准的name属性，通过这个属性可以访问到给定函数指定的名字，而这个属性的值永远等于跟在function 关键字后面的标识符，匿名函数的name属性为空。
```
//IE11-浏览器无效，均输出undefined
//chrome在处理匿名函数的name属性时有问题，会显示函数表达式的名字
function fn(){};
console.log(fn.name);//'fn'
var fn = function(){};
console.log(fn.name);//''，在chrome浏览器中会显示'fn'
var fn = function abc(){};
console.log(fn.name);//'abc'
```
[注意点]：name属性早就被浏览器广泛支持，但是只到ES6才将其写入标准。
ES6对这个属性的行为做出了一些修改，如果将一个匿名函数赋值给一个变量，ES5的name属性，会返回空的字符串。而ES6的name属性会返回实际的函数名。
```
var func1 = function () {};
func1.name //ES5:  ""
func1.name //ES6: "func1"
```
如果将一个具名函数赋值给一个变量，则ES5和ES6的name属性都返回这个具名函数原本的名字。
```
var bar = function baz() {};
bar.name //ES5: "baz"
bar.name //ES6: "baz"
```
Function 构造函数返回的函数实例，name属性的值为"anonymous "。
```
(new Function).name // "anonymous"
```
可见通过构造函数生成的函数对象是一个匿名函数。
bind返回的函数，name属性值会加上”bound”前缀。
```
function foo() {};
foo.bind({}).name // "bound foo"
(function(){}).bind({}).name // "bound "
```
## 【prototype属性】
每一个函数都有一个prototype属性，这个属性指向一个对象的引用，这个对象成为原型对象（prototype Object）。每一个函数都包含不同的原型对象。将函数用作构造函数时，新创建的对象会从原型对象上继承属性。
```
function fn(){};
var obj = new fn;
fn.prototype.a = 1;
console.log(obj.a);//1
```
#二、方法
## 【apply()和call()】
每个函数都包含两个非继承而来的方法：apply()和call()。这两个方法的用途都是在特定的作用域中调用函数。实际上相当于将函数体内的this指向这个新传入的对象。
要想以对象o的方法来调用函数f()，可以这样使用call()和apply()。
```
f.call(o);
f.apply(o);
```
假设o中不存在m方法，则等价于
```
o.m = f; //将f存储为o的临时方法
o.m(); //调用它，不传入参数
delete o.m; //将临时方法删除
```
一个具体的例子
```
window.color = "red";
var o = {color: "blue"};
function sayColor(){
    console.log(this.color);
}
sayColor();            //red
sayColor.call(this);   //red
sayColor.call(window); //red
sayColor.call(o);      //blue
```
```
//sayColor.call(o)等价于:
o.sayColor = sayColor;
o.sayColor();   //blue 这里其实是方法调用，所有函数中的this指向o这个对象。
delete o.sayColor;
```
apply()方法接收两个参数：一个是在其中运行函数的作用域（可以说是要调用函数的母对象，它是调用上下文，在函数体内通过this来获得对它的引用），另一个是参数数组。其中，第二个参数可以是Array的实例，也可以是arguments对象。
```
function sum(num1, num2){
    return num1 + num2;
}
//因为运行函数的作用域是全局作用域，所以this代表的是window对象
function callSum1(num1, num2){
    return sum.apply(this, arguments);
}
function callSum2(num1, num2){
    return sum.apply(this, [num1, num2]);
}
console.log(callSum1(10,10));//20
console.log(callSum2(10,10));//20
```
call()方法与apply()方法的作用相同，它们的区别仅仅在于接收的第二个参数。apply()接收的是一个数组，call()接收的是参数列表。
```
function sum(num1, num2){
    return num1 + num2;
}
function callSum(num1, num2){
    return sum.call(this, num1, num2);
}
console.log(callSum(10,10));   //20
```
　至于是使用apply()还是call()，完全取决于采取哪种函数传递参数的方式最方便。如果打算直接传入arguments对象，或者包含函数中先接收到的也是一个数组，那么使用apply()肯定更方便；否则，选择call()可能更合适。
　在非严格模式下，使用函数的call()或apply()方法时，null或undefined值会被转换为全局对象。而在严格模式下，函数的this值始终是指定的值。
```
var color = 'red';
function displayColor(){
    console.log(this.color);
}
displayColor.call(null);//red
```
```
var color = 'red';
function displayColor(){
    'use strict';
    console.log(this.color);
}
displayColor.call(null);//TypeError: Cannot read property 'color' of null
```
#三、应用
## 【1】：调用对象的原生方法
```
var obj = {};
obj.hasOwnProperty('toString');// false
obj.hasOwnProperty = function (){
  return true;
};
obj.hasOwnProperty('toString');// true
Object.prototype.hasOwnProperty.call(obj, 'toString');// false
```
## 【2】：找出数组中的最大元素
```
var a = [20,30,40,50,100];
Math.max.apply(null,a); //100
```
## 【3】将数组对象转换为真正的数组。
```
Array.prototype.slice.apply({0:1,length:1});//[1]
//或者
[].prototype.slice.apply({0:1,length:1});//[1]
```
传入的就是一个伪数组。
## 【4】：将一个数组的值push另一个数组中
```
var a = [];
Array.prototype.push.apply(a,[1,2,3]);
console.log(a);//[1,2,3]
Array.prototype.push.apply(a,[2,3,4]);
console.log(a);//[1,2,3,2,3,4]
```
底层实现原理
```
a.push = push; //把push方法存储为a的一个临时方法
a.push([1,2,3]); //调用它 
delete a.push; //将临时方法删除
```
如果使用ES6中的不定参数则非常简单
```
var a  = [...[1,2,3],...[2,3,4]];
console.log(a);//[1,2,3,2,3,4]
```
## 【5】绑定回调函数的对象
由于apply方法（或者call方法）不仅绑定函数执行时所在的对象，还会立即执行函数，因此不得不把绑定语句写在一个函数体内。更简洁的写法是采用下面介绍的bind方法。
```
var o = {};
o.f = function () {
  console.log(this === o);
}
var f = function (){
  o.f.apply(o);
};
$('#button').on('click', f);
```
## 6、【toString()】：函数的toString()实例方法返回函数代码的字符串，而静态的toString()方法返回一个类似于'[native code]'的字符串作为函数体。
```
function test(){
    alert(1);//test
}
test.toString();/*"function test(){
                    alert(1);//test
                  }"*/
Function.toString();//"function Function() { [native code] }"
```
## 7、【toLocateString()】：函数的toLocateString()方法和toString()方法的返回结果相同。
```
function test(){
    alert(1);//test
}
test.toLocaleString();/*"function test(){
                    alert(1);//test
                  }"*/
Function.toLocaleString();//"function Function() { [native code] }"
```
## 8、【valueOf()】：函数的valueOf()方法返回函数本身。
```
function test(){
    alert(1);//test
}
test.valueOf();/*function test(){
                    alert(1);//test
                  }*/
typeof test.valueOf();//'function'
Function.valueOf();//Function() { [native code] }
```
并且这个属性的返回值是可执行的。
```
function test(){
       console.log(1);
   }
   test.valueOf()(); //1;
```

转载自：小火柴的蓝色理想
<http://www.cnblogs.com/xiaohuochai/p/5613593.html>
在阅读老师的博客时，我把它当做看书来对待，所以我的博客大部分来自老师的原话以及实例，再加上自已的一些理解，并且做了适当的标注，还有对老师的代码进行了一些测试。