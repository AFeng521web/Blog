---
title: 深入理解javascript对象系列第二篇——属性操作
date: 2018-05-22 08:59:32
tags: 随笔
toc: true
---

# 前面的话
对于对象来说，属性操作是绕不开的话题，类似于“增删改查”的基本操作，**属性操作分为属性查询、属性设置、属性删除，还有属性继承。**

# 一、属性查询
**属性查询一般有两种方法，包括点运算符和方括号运算符。**

```
var o = {
  p: 'Hello World'
};
o.p // "Hello World"
o['p'] // "Hello World"
```
[注意点]：变量中可以存在中文，因为中文相当于字符，与英文字符同样对待，因此可以写成person.年龄 或person[年龄]；

```
var person = {
    年龄 : 1
}
person.年龄;//1
person['年龄'];//1
```
<!-- more -->

## 1、点运算符
点运算符是很多面向对象语言的通用语法，由于其比较简单，所以较方括号运算符相比，更常用。

由于javascript是弱类型语言，在任何对象中都可以创建任意数量的属性。但当通过点运算符(.)访问对象的属性时，属性名用一个标识符来表示，标识符要符合变量命名规则。标识符必须直接出现在javascript程序中，它们不是数据类型，因此程序无法修改它们。
```
var o = {
    a:1,
    1:2
};
console.log(o.a);//1
//由于变量不可以以数字开头，所以o.1报错
console.log(o.1);//Uncaught SyntaxError: missing ) after argument list
```

## 2、方括号运算符
当通过方括号运算符（[]）来访问对象的属性时，属性名是通过字符串来表示，字符串是javascript的数据类型，在程序运行中可以修改和创建它们。

### 使用方括号运算符的两个优点：
【1】：可以通过变量来访问属性
```
var a = 1;
var o = {
    1: 10
}
o[a];//10 //访问时先向上搜索变量，在通过变量的值查询对象的属性
```
【2】：属性名称可以是javascript无效的标识符。
```
var myObject = {
    123:'zero',
    class:'foo'
};
console.log(myObject['123'],myObject['class']);//'zero' 'foo'
console.log(myObject.123); //报错
```
方括号中的值若是非字符串类型会使用String()隐式转换成字符串再输出；如果是字符串类型，若有引号则原值输出，否则会被识别为变量，若变量未定义，则报错。
```
var person = {};
person[0];  //[]中的数字不会报错，而是自动转换成字符串
person[a];  //[]中符合变量命名规则的元素会被当成变量，变量未被定义，而报错
person['']; //[]中的空字符串不会报错，是实际存在的且可以调用，但不会在控制台右侧的集合中显示

person[undefined];//不会报错，而是自动转换成字符串
person[null];//不会报错，而是自动转换成字符串
person[true];//不会报错，而是自动转换成字符串
person[false];//不会报错，而是自动转换成字符串
```
### 可计算属性名
在方括号运算符内部可以使用表达式
```
var a = 1;
var person = {
    3: 'abc'
};
person[a + 2];//'abc' //使用计算后的属性名进行查询对象，这里还存在隐式转换为字符串。
```
但是如果要在对象字面量内部对属性名使用表达式，则需要使用ES6的可计算属性名。
```
var a = 1;
//Uncaught SyntaxError: Unexpected token +
var person = {
    a + 3: 'abc'
};
```
ES6中增加了可计算属性名，可以在文字中使用[ ]包裹一个表达式当做属性名。
```
var a = 1;
var person = {
    [a + 3]: 'abc'
};
person[4];//'abc'
```


### 属性查询错误
【1】：查询一个不存在的属性不会报错，而是返回undefined。
```
var person = {};
console.log(person.a);//undefined
```
【2】：如果对象不存在，试图查询这个不存在的对象的属性会报错。
```
console.log(person.a);//Uncaught ReferenceError: person is not defined
```
可以利用这一点，来检查一个全局变量是否被声明。
```
// 检查a变量是否被声明
if (a) {...} // 报错

//所有全局变量都是window对象的属性。window.a的含义就是读取window对象的a属性，如果该属性不存在，就返回undefined，并不会报错
if (window.a) {...} // 不报错
```
# 二、属性设置
属性设置又称为属性赋值，与属性查询相同，具有点运算符和方括号运算符这两种方法。
```
o.p = 'abc';
o['p'] = 'abc';
```
在给对象设置属性之前，一般要先检测对象是否存在。
```
var len = undefined;
if(book){
    if(book.subtitle){
        len = book.subtitle.length;
    }
}

//简化代码
var len = book && book.subtitle && book.subtitle.length;
```
null和undefined不是对象，给它们设置属性会报错。
```
null.a = 1;//Uncaught TypeError: Cannot set property 'a' of null
undefined.a = 1;//Uncaught TypeError: Cannot set property 'a' of undefined
```
由于string、number和boolean有对应的包装对象，所以给它们设置属性不会报错。
```
'abc'.a = 1;//1
(1).a = 1;//1
true.a = 1;//1
```
# 三、属性删除
使用delete运算符可以删除对象的属性（包括数组元素）
```
var o = {
    a : 1
};
console.log(o.a);//1
console.log('a' in o);//true
console.log(delete o.a);//true
console.log(o.a);//undefined
console.log('a' in o);//false
```
[注意点]：给对象属性设置null或undefined，并没有删除属性。
```
var o = {
    a : 1
};
o.a = undefined;
console.log(o.a);//undefined
console.log('a' in o);//true
console.log(delete o.a);//true
console.log(o.a);//undefined
console.log('a' in o);//false
```
使用delete删除数组元素时，不会改变数组长度。
```
var a = [1,2,3];
delete a[2];
2 in a;//false
a.length;//3
```
delete运算符只能删除自身属性，不能删除继承属性（要删除继承属性必须从定义这个属性的原型对象上删除它。而且这会影响到所有继承自这个原型的对象）
```
var o  = {
    a:1
}
var obj = Object.create(o);
obj.a = 2;

console.log(obj.a);//2
console.log(delete obj.a);//true
console.log(obj.a);//1
//删除不了继承的属性，但为什么会返回true.
console.log(delete obj.a);//true
console.log(obj.a);//1
```
### 返回值
delete操作符的返回值是个布尔值true或false。
【1】：当使用delete操作符删除对象属性或者数组元素删除成功时，返回true。
```
var o = {a:1};
var arr = [1];
console.log(delete o.a);//true
console.log(delete arr[0]);//true
```
【2】：当使用delete操作符删除不存在的属性或者非左值时，返回true。
```
var o = {};
console.log(delete o.a);//true
console.log(delete 1);//true
console.log(delete {});//true
```
【3】：当使用delete操作符删除变量时，返回false，严格模式下会抛出ReferenceError错误。
```
var a = 1;
console.log(delete a);//false
console.log(a);//1

'use strict';
var a = 1;
//Uncaught SyntaxError: Delete of an unqualified identifier in strict mode
console.log(delete a);
```
【4】：当使用delete操作符删除不可配置的属性时，返回false，严格模式下会抛出TypeError错误。
```
var obj = {};
//defineProperty定义对象的属性
Object.defineProperty(obj,'a',{configurable:false});
console.log(delete obj.a);//false

'use strict';
var obj = {};
Object.defineProperty(obj,'a',{configurable:false});
//Uncaught TypeError: Cannot delete property 'a' of #<Object>
console.log(delete obj.a);
```
# 四、属性继承
每一个javascript对象都和另一个对象相关联，“另一个对象”就会我们熟知的原型对象，每一对象都会从原型继承属性。所有通过对象字面量创建的对象都具有同一个原型对象，并可以通过Object.prototype获得对原型对象的引用。
```
var obj = {};
console.log(obj.__proto__ === Object.prototype);//true
```

[注意点]：Object.prototype的原型对象是null，所以它不继承任何属性。
```
console.log(Object.prototype.__proto__ === null);//true
```
对象本身具有的属性叫自有属性(own property)，从原型对象继承而来的属性叫继承属性。
```
var o = {a:1};
var obj = Object.create(o);
obj.b = 2;
//继承自原型对象o的属性a
console.log(obj.a);//1
//自有属性b
console.log(obj.b);//2
```
in操作符：可以判断属性是否在某个对象上，但无法区分是自有属性还是继承属性。（就是说可以查询继承属性）
```
var o = {a:1};
var obj = Object.create(o);
obj.b = 2;
console.log('a' in obj);//true
console.log('b' in obj);//true
console.log('b' in o);//false
```
for-in：通过for-in循环可以遍历出该对象中所有可枚举（Enumerable == true的）属性。
```
var o = {a:1};
var obj = Object.create(o);
obj.b = 2;
for(var i in obj){
    console.log(obj[i]);//2 1
}
```
hasOwnProperty()：通过hasOwnProperty()方法可以确定该属性是自有属性还是继承属性。
```
var o = {a:1};
var obj = Object.create(o);
obj.b = 2;
console.log(obj.hasOwnProperty('a'));//false
console.log(obj.hasOwnProperty('b'));//true
```
Object.keys()：Object.keys()方法返回所有可枚举（Enumerable ： true的）的自有属性,并且存储在一个数组中。
```
var o = {a:1};
   var obj = Object.create(o,{
       c:{value:3, enumerable:false}
   });
   obj.b = 2;
   console.log(Object.keys(obj));//['b']
```
Object.getOwnPropertyNames()：与Object.keys()方法不同，Object.getOwnPropertyNames()方法返回所有自有属性(包括不可枚举的属性)，并且存储在一个数组中。

```
var o = {a:1};
   var obj = Object.create(o,{
       c:{value:3,enumerable: false}
   });
   obj.b = 2;
   console.log(Object.getOwnPropertyNames(obj));//['c','b']
```

转载自：小火柴的蓝色理想
<http://www.cnblogs.com/xiaohuochai/p/5613593.html>
在阅读老师的博客时，我把它当做看书来对待，所以我的博客大部分来自老师的原话以及实例，再加上自已的一些理解，并且做了适当的标注，还有对老师的代码进行了一些测试。