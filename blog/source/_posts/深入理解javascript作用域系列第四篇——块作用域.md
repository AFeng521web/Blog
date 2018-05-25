---
title: 深入理解javascript作用域系列第四篇——块作用域
date: 2018-05-25 10:44:10
tags: 随笔
toc: true
---

# 前面的话
**尽管函数的作用域是最常见的作用域单元，也是现行大多数javascript最普遍的设计方法，但在其他类型的作用域单元也是存在的，并且通过使用其他类型的作用域单元甚至可以实现维护起来更加优秀，简洁的代码，比如块作用域。随着ES6的推广，块作用域也将用得越来越广泛。**

# 一、let
```
for (var i= 0; i<10; i++) {
    console.log(i);
}
```
上面这段代码时很熟悉的循环代码，通常只是因为想在for循环内部的上下文中使用变量i。但是实际上i可以在全局作用域中访问到，因次，污染了整个作用域。

<!-- more -->

```
for (var i= 0; i<10; i++) {
     console.log(i);
}
console.log(i);//10
```
ES6改变了这个现状，引入了关键字let，提供了除var以外的另一种变量声明的方式。let关键字可以将变量绑定到所在的任意作用域（通常是{...}内部），实现块作用域。
```
{
  let i = 1;  
};
console.log(i);//ReferenceError: i is not defined
```
块作用域实际上可以代替立即执行的匿名函数。（IIFE）
```
(function(){
  var i = 1;  
})();
console.log(i);//ReferenceError: i is not defined
```
如果在文章的最开始那段for循环的代码中的变量i用let声明，将会避免作用域污染问题。
```
for (let i= 0; i<10; i++) {
     console.log(i);
}
console.log(i);////ReferenceError: i is not defined
```
**for循环头部的let不仅将i绑定到了for循环的块中，事实上它将其重新绑定到了循环的每一次迭代中，确保使用上一个循环迭代结束时值重新进行赋值。（这一段话不是很理解）**
```
//与上一段代码等价
{
    let j;
    for (j=0; j<10; j++) {
        let i = j; //每个迭代重新绑定
        console.log( i );
    }
}
```
## 1、循环
下面的代码中，由于闭包只能取得包含函数中的任何变量的最后一个值，所以控制台输出5，而不是0。
```
var a = [];
for(var i = 0; i < 5; i++){
    a[i] = function(){
        return i;
    }
}
console.log(a[0]());//5
```
**我的理解之所以会输出5，是因为for循环为每个数组元素绑定函数时，仅仅只是对其进行绑定了，而不会调用。所以在编译阶段函数体内的```i```值没有被赋予任何值，只是在函数调用阶段，这个i才会进行RHS查询，进而搜索作用域链，而此时i在全局中的值是5。所以无论调用哪一个数组元素的函数都返回5。**
```
 var a = [];
   for(var i = 0; i < 5; i++){
       a[i] = function(){
           return i;
       }
   }
   console.log(a[0]());//5
   console.log(a[1]());//5
   console.log(a[2]());//5
   console.log(a[3]());//5
   console.log(a[4]());//5
```
当然，可以通过函数传参的形式，来保存每次循环的值。
```
var a = [];
for(var i = 0; i < 5; i++){
    a[i] = (function(j){
        return function(){
            return j; 
        }
    })(i);
    //通过函数传参，并且绑定时通过立即执行函数，把绑定的函数执行了。
    //所以调用时，返回的j就是对应传入的那个i.
}
console.log(a[0]());//0
```
**而使用let则更方便，由于let循环有一个重新赋值的过程，相当于保存了每一次循环的值。（不是很理解这一点的用法）**
```
var a = [];
for(let i = 0; i < 5; i++){
    a[i] = function(){
        return i;
    }
}
console.log(a[0]());//0
```
## 2、重复声明
let不允许在相同作用域内，重复声明同一个变量。
```
{
  let a = 10;
  var a = 1;//SyntaxError: Unexpected identifier
}
```
## 3、提升
使用let进行的声明不会在块作用域中进行提升。
```
{
  console.log(i);//ReferenceError: i is not defined
  let i = 1;  
};
```

# 二、const
除了let以外，ES6还引入了const，同样可以用来创建块级作用域变量。但其值是固定的（常量）。之后任何试图修改值的操作都会引起错误。
```
if (true) {
    var a = 2;
    const b = 3; 
    a = 3; 
    b = 4;// TypeError: Assignment to constant variable（常量）
}
console.log( a ); // 3
console.log( b ); // ReferenceError: b is not defined（块作用域变量）
```
const声明的常量，也与let一样不可重复声明。
```
const message = "Goodbye!";
const message = "Goodbye!";//SyntaxError: Identifier 'message' has already been declared
```

# 三、try
try-catch语句的一个常见用途是创建块级作用域，其中声明的变量仅仅在catch内部有效。
```
{
    let a = 2;
    console.log(a); // 2
}
console.log(a); //ReferenceError: a is not defined
```
在ES6之前的环境中，可以使用try-catch语句达到上面代码的类似效果。
```
try{
    throw 2;
}catch(a){
    console.log( a ); // 2
}
console.log( a ); //ReferenceError: a is not defined
```
```
//或者
try{
    throw undefined;
}catch(a){
    a = 2; //对a进行赋值，仅在catch内部有效。
    console.log( a ); // 2
}
console.log( a ); //ReferenceError: a is not defined
```

### 转载自：小火柴的蓝色理想
<http://www.cnblogs.com/xiaohuochai/p/5613593.html>
在阅读老师的博客时，我把它当做看书来对待，所以我的博客大部分来自老师的原话以及实例，再加上自已的一些理解，并且做了适当的标注，还有对老师的代码进行了一些测试。
