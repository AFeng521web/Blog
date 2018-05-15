---
title: 深入理解javascript函数系列第一篇——函数概述
date: 2018-05-15 16:50:24
tags: 随笔
---

# 前面的话
## 函数对于任何一门语言来说都是核心的概念。通过函数可以封装任意多条语句，
而且可以在任何地方，任何时候调用执行。在javascript世界里，函数即就是对象，
程序可以随意操控它们，函数可以嵌套在其他函数中定义，这样它们就可以访问它们被定义时所处作用域中的任何变量，
它（函数）给javascript带来了非常强劲的编程能力。

<!-- more -->

# 一、函数定义：总共有三种函数定义的方式
## 【1】：函数声明语句
使用function关键字，后跟一组参数及函数体
```
function funcName（arg1[,arg2...,argn]）{
	statement
}

```
funcName是要声明的函数名称的标识符，函数名之后的圆括号中的是参数列表，参数之间使用逗号隔开，这些参数列表成为函数的形参，当调用函数时，这些标识符则指代传入函数的实参。
【注意】function语句里的花括号是必须的，这和while循环语句和其它一些语句所使用的语句块是不同的，即使函数体只包含一条语句，仍然必须使用花括号将其括起来。
```
function test()//SyntaxError: Unexpected end of input
function test(){};//不报错
while(true);//不报错
```
提升
以函数声明语句声明的函数，函数名称和函数体都会提升
```
foo();
function foo() {
	console.log(1); //1
}
```
上面代码片段之所以能够在控制台输出1，就是因为foo()函数声明进行了提升，如下所示：
```
function foo() {
	console.log(1); //1
}
foo();
```
重复：变量的重复声明是无用的，但函数的重复声明会覆盖前面的声明（无论是变量还是函数声明）
```
//变量的重复声明无用
var a = 1;
var a;
console.log(a); //1
```
```
//由于函数声明提升优于变量声明提示，所以变量的声明无作用
var a;
function a() {
	console.log(1);
}
a(); //1
```
```
//后面的函数声明会覆盖前面的函数声明
a(); //2
function a() {
	console.log(1);
}
function a() {
	console.log(2);
}
```
所以应该避免在同一作用域中重复声明

删除：和删除变量一样，函数声明语句创建的变量无法删除。
```
function foo() {
	console.log(1);
}
delete foo; //false;
foo(); //1;
```

## 【2】：函数定义表达式
以表达式方式定义的函数，函数的名称是可选的。
```
var functionName = function([arg1 [,arg2 [...,argn]]]){
    statement;
}

var functionName = function funcName([arg1 [,arg2 [...,argn]]]){
    statement;
}
```
匿名函数（anonymous function）也叫拉姆达函数，是function关键字后面没有表示符的函数。
通常而言，以表达式方式定义函数时都不需要名称，这样会让代码更加紧凑，函数定义表达式特别适合用来定义那些只会使用一次的函数。
```
var tensquared = (function(x) {return x*x;}(10)); 
console.log(tensquared); //100
```
而一个函数定义表达式包含名称时，函数的局部作用域将会包含一个绑定到函数对象的名称，实际上，函数的名称将会成为函数内部的一个局部变量。
```
var test = function fn(){
       return fn;
   };
   console.log(test);
   console.log(test());
   console.log(test()());
```
以下是上面代码片段执行的结果
![这里写图片描述](https://img-blog.csdn.net/20180429155838136?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzk3MjcyMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

个人理解，对于具名的函数表达式来说，函数名称相当于函数的形参，只能在函数的内部使用，而变量名称相当于函数对象的实参，在函数内部和外部都可以使用。
```
var test = function fn() {
	return fn === test;
};
console.log(test()); //true
console.log(test === fn); //ReferenceError: fn is not defined
```
函数定义了一个非标准的name属性，通过这个属性可以访问到给定函数指定的名字，这个值永远等于跟在function关键字后面的标识符，匿名函数的name属性为空。
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
## 【3】Function 构造函数
Function构造函数接收任意数量的参数，但是最后一个参数始终被看成是函数体，而前面的参数则枚举出了新函数的参数。
```
var functionName = new Function(['arg1' [,'arg2' [...,'argn']]],'statement;');
```
注意点：构造函数创建函数时的参数是以字符串形式传入的。并且Function构造函数无法指定函数的名称，它创建的是一个匿名函数。
从技术上讲，这是一个函数表达式，但是，不推荐这样使用，因为这种语法会导致解析两次代码，第一次是解析常规的javascript代码，第二次是解析传入构造函数的字符串，影响性能。
```
var sum = new Function('num1','num2','return num1 + num2');
//等价于
var sum = function(num1,num2){
    return num1+num2;
}
```
Function构造函数创建的函数，其函数体的编译总是会在全局作用域中执行，于是，Function()构造函数类似于在全局作用域中执行的eval();
```
var test = 0;
function fn(){
    var test = 1;
    return new Function('return test');
}
console.log(fn()());//0
```
上面这个是老师的例子，我感觉下面这个例子更能说明为问题。
```
var test = 0;
   function fn(){
       var test = 1;
       var foo =  new Function('return test');
       console.log(foo());
   }
   fn();//0
```
[注意]：并不是所有的函数都可以成为构造函数的
```
var o = new Math.min();//Uncaught TypeError: Math.min is not a constructor
```
函数的返回值
函数中的return语句用来返回函数调用后的返回值
```
return expression
```
return语句只能出现在函数体内，如果不是会语法报错。
```
return 1; //SyntaxError: Illegal return statement
```
如果没有return语句，则函数调用仅仅是执行函数体内的每一条语句直到函数结束，最后返回调用程序（销毁当前执行环境，返回到调用时的环境），在这种情况下，调用表示式的结果是undefined。
```
var test = function fn() {}
console.log(test()); //undefined
```
当函数执行到return语句时，函数会终止执行，并返回expression的值给调用程序。
```
var test = function fn() {
	return 2;
};
console.log(test()); //2
```
[注意]：并不是函数中return语句后的所有语句都不执行，finally是个例外，return语句不会阻止finally字句的执行。
```
function testFinnally(){
    try{
        return 2;
    }catch(error){
        return 1;
    }finally{
        return 0;
    }
}
testFinnally();//0
```
[注意]：由于javascript可以自动插入分号，因此在return关键字和后面的表达式之间不能有换行。
```
var test = function() {
	return
	2;
}
console.log(test()); //undefined
```
一个函数中可以有多个return语句，但最终会执行到的只能有一个return语句。
```
function diff(num1,num2) {
	if(num1>num2) {
	   return num1 - num2;
	} else {
	   return num2 - num1;
	}
}
```
return语句可以单独使用而不必带有expression,这样的话也会向调用程序返回undefined。
```
var test = function fn() {	
	return;
}
console.log(test()); //undefined
```
return语句经常作为函数内的最后一条语句出现，这是因为rerurn语句可以用来使函数提前返回。当rerurn被执行时，函数立即返回而不再执行余下的语句。
```
//并没有弹出1
var test = function fn() {
	return;
	alert(1);
};
console.log(test()); //undefined
```
如果函数调用时在前面加上了new前缀，而且返回值不是一个对象，则返回this（该新对象），其实是把这个函数当做构造函数使用了，而构造函数中的this指的就是新初始化出来的对象（new出来的对象）。
```
 function fn() {
       this.a = 2;
       return 1;
   }
   var test = new fn();
   console.log(test);
   console.log(test.constructor);
```
运行结果：
![这里写图片描述](https://img-blog.csdn.net/20180429174839153?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzk3MjcyMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

如果返回值是一个对象，则返回该对象。
```
function fn() {
	this.a = 2;
	return {a:1};
}
var test = new fn();
console.log(test);
console.log(test.constructor);
```
运行结果：
![这里写图片描述](https://img-blog.csdn.net/20180429175306855?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzk3MjcyMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

# 二、函数调用
只有函数被调用时，才会被执行。调用运算符是跟在任何产生一个函数值的表达式之后的一对圆括号，圆括号内可包含零个或多个用逗号隔开的表达式。每个表达式都会产生一个参数值，每个参数值被赋予函数声明时定义的形参名。
javascript一共有四种调用模式：函数调用模式，方法调用模式，构造器调用模式和间接调用模式。
## 【1】：函数调用模式
当一个函数并非一个对象的属性时，那么它就是被当做一个函数来调用的。对于普通的函数调用来说，函数的返回值就是调用表达式的值。
```
function add(x,y) {
	return x+y;
}
var sum = add(3,5);
console.log(sum); //8
```
使用函数调用模式调用函数时，非严格模式下，this被绑定到全局对象；在严格模式下，this是undefined。
```
function add(x,y) {
	console.log(this); //window
}
add();
```
```
function add(x,y) {
	'use strict'
	console.log(this); //undefined
}
add();
```
因此，“this”可以用来判断当前函数是否是严格模式。
使用一个立即执行函数来判断
```
var strict = (function() {return !this}());
console.log(strict); //非严格模式下，返回false.
```
重写：因为函数调用模式中的this被绑定到了全局对象（window），所以会发生全局属性被重写的现象。
```
var a = 0;
function fn() {
	this.a = 1;
}
fn();
console.log(this,this.a,a); //window， 1，1 
```
## 【2】：方法调用模式
当一个函数被保存在为对象的一个属性时，我们称它为一个方法，当一个方法被调用时，this被绑定到该对象（调用这个方法的对象）。如果表达式包含一个属性提取的动作，那么它就是被当做一个方法来调用的。
```
var o = {
    m: function(){
        console.log(1);
    }
};
o.m();//1
```
方法可以使用this访问自已所属的对象，所以它能从对象中取值或者对对象进行修改。this到对象的绑定发生在调用的时候。通过this可以取得它们所属对象的上下文的方法称为公共方法。
```
var o = {
    a: 1,
    m: function(){
        return this;
    },
    n: function(){
        this.a = 2;
    }
};
console.log(o.m().a);//1
o.n();
console.log(o.m().a);//2
```
任何函数只要作为方法调用实际上都会传入一个人隐式的实参——这个实参是一个对象，方法调用的母体就是这个对象，通常来讲，基于这个对象的方法可以进行多种操作，方法调用的语法已经来清楚 的表明一个函数将基于一个对象进行操作。
```
rect.setSize(width,height);
setRectSize(rect,width,height);
```
假设上面这两行代码的功能完全是一样的，它们都作用于一个假定的对象rect。可以看出，第一行的方法调用非常清楚的表明这个函数执行的载体是rect对象，函数中的所有操作都是基于这个对象。

和变量不同，关键字this没有作用域的限制，嵌套的函数不会从调用它的函数中继承this。如果嵌套的函数为方法调用，那么this指向调用它的对象。如果嵌套函数作为函数调用，其this不是全局对象（非严格模式下）就是undefined（严格模式下）。
```
var o = {
    m: function(){
         function n(){
             return this;
         }
         return n();//这里返回的是 执行n()这个函数的返回值，因为这里是函数调用模式，所以返回的是window
    }
}
console.log(o.m());//window
```
```
var o = {
    m: function(){
         function n(){
             'use strict';
             return this;
         }
         return n();
    }
}
console.log(o.m());//undefined
```
如果想访问这个外部函数的this值，需要将this的值保存在一个变量里，这个变量和内部函数在同一个作用域内，通常使用变量that来保存this。
```
var o = {
       m: function(){
           var that = this;
           console.log(this === o);//true
           //因为m函数是方法调用模式的，所以this指的是调用这个m的那个对象。
           function n(){
               console.log(this === o);//false
               console.log(this === window); //true
               console.log(that === o); //true
               return that;
           }
           return n();
       }
   };
   console.log(o.m() === o);//true
```
## 【3】：构造函数调用模式
如果函数或者方法调用之前带有关键字new，它就构成构造函数调用。
```
function fn() {
	this.a = 1;//构造函数中的this指的是新初始化出来的对象
}
var obj = new fn();
console.log(obj); //{a:1};
```
如果构造函数调用的圆括号内包含一组实参列表，先计算这些实参表达式，然后传入函数体内。
```
function fn(x) {
	this.a = x;
};
var obj = new fn(2);
console.log(obj.a); //2;
```
如果构造函数没有形参，javascript构造函数调用语法是允许省略实参列表和圆括号的。
```
var o = new Object()
//等价于
var o = new Object;
```
[注意]：尽管构造函数看起来像一个方法调用，但它依然会使用这个新对象作为调用上下文。也就是说，在表达式 new  o.m()中，调用上下文并不是o，而是新new出来的对象。
```
var o = {
    m: function(){
        return this;
    }
}
var obj = new o.m();
console.log(obj,obj === o);//{} false
console.log(obj.constructor === o.m);//true
```
构造函数通常不使用rerurn关键字，它们通常用来初始化对象，当构造函数的函数体执行完毕时，它会显示返回，在这种情况下，构造函数调用表达式的计算结果就是这个新对象的值。
```
function fn(){
    this.a = 2;
}
var test = new fn();
console.log(test);//{a:2}
```
如果构造函数使用return语句但没有指定返回值，或者返回一个原始值，那么这时将会忽略返回值，同时使用这个新对象作为调用结果。
```
function fn(){
    this.a = 2;
    return;
}
var test = new fn();
console.log(test);//{a:2}
```
如果构造函数显示的使用return语句返回一个对象，那么调用表达式的值就是这个对象。
```
var obj = {a:1};
function fn(){
    this.a = 2;
    return obj;
}
var test = new fn();
console.log(test);//{a:1}
```
## 【4】间接模式调用
javascript中的函数也是对象，函数对象也可以包含方法。call()和apply()方法可以用来间接调用函数。
这两个方法都允许显示指定调用所需的this值，也就是说，任何函数可以作为任何对象的方法来调用，哪怕这个函数不是那个对象的方法，两个方法都可以指定调用的实参（被传入到调用call()和apply()的函数中）。call()方法使用它自有的实参列表作为函数的实参，apply()方法则要求以数组的形式传入参数。
```
var obj = {};
   function sum(x,y) {
       return x+y;
   }
   console.log(sum.call(obj,1,2));
   console.log(sum.apply(obj,[1,2]));
```
自己的理解：其实就是把函数放到指定的对象中运行。而且该对象中必定含有函数要用到的元素。

转载自：小火柴的蓝色理想
<http://www.cnblogs.com/xiaohuochai/p/5613593.html>
在阅读老师的博客时，我把它当做看书来对待，所以我的博客大部分来自老师的原话以及实例，再加上自已的一些理解，并且做了适当的标注，还有对老师的代码进行了一些测试。