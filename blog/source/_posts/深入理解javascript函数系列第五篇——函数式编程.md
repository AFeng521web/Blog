---
title: 深入理解javascript函数系列第五篇——函数式编程
date: 2018-05-15 16:52:25
tags: 随笔
--- 

# 一、定义
简单说，“**函数式编程”是一种“编程范式”（programming paradigm），也就是如何编写程序的方法论。**

它属于“结构化编程”的一种，主要思想就是把运算过程尽量写成一系列嵌套的函数调用。以函数作为主要载体的编程方式，用函数去拆解，抽象一般的表达式。

<!-- more -->

## 举例说明函数式编程
假如现在有这样一个数学表达式。
```
(1 + 2) * 3 - 4
``` 
传统的过程式编程，会这样写
```
　var a = 1 + 2;

　var b = a * 3;

　var c = b - 4;
```
函数式编程要求使用函数，我们可以把运算过程定义为不同的函数，然后写成下面这样：
```
var result = subtract(multiply(add(1,2), 3), 4);
```
这就是函数式编程，**把一个函数的调用结果当做另一个函数的参数进行传递。**
比如把add()函数的调用结果当做multiply()函数的参数。

在来看这样一个例子：
需求：要求把数组中每个单词的**首字母该为大写**

```
 //一般写法
var arr = ['apple', 'pen', 'apple-pen'];
    for(var i in arr) {
        var c = arr[i][0];
        arr[i] = c.toUpperCase() + arr[i].slice(1);
        //slice()用于提取一个字符串的一部分，并返回一个新的字符。
    }
    console.log(arr); //["Apple", "Pen", "Apple-pen"]

//函数式编程
function upperFirst(word) {
        return word[0].toUpperCase() + word.slice(1);
    }

function wordToUpperCase(arr) {
        return arr.map(upperFirst)
    }

console.log(wordToUpperCase(['apple', 'pen', 'apple-pen'])); //["Apple", "Pen", "Apple-pen"]

//上述代码的实质
function wordToUpperCase(arr) {
       return arr.map(function upperFirst(word) {
           //这里的word其实代表的是arr中的每一项
           return word[0].toUpperCase() + word.slice(1);
       })
   }

   console.log(wordToUpperCase(['apple', 'pen', 'apple-pen'])); //["Apple", "Pen", "Apple-pen"]
```
上述的函数式编程，它利用了函数的封装性功能将功能做拆解（粒度不唯一），并封装为功能不同的函数，而再利用组合调用方式来达到目的，这样做的好处使得表意清晰，易于维护，复用以及更好的扩展。其次是利用高阶函数，Array.map 代替 for…of 做数组遍历，减少了中间变量和操作。

# 二、函数式编程的特点
1、函数是“第一等公民”。

所谓“第一等公民”，指定就是函数与其它数据类型一样，处于平等地位，可以赋值给其它变量，也可以作为参数，传入另一个函数，或者作为别的函数的返回值返回。
举例来说：下面代码中的print变量就是一个函数，可以作为另一个函数的参数。
```
var print = function(i){ console.log(i);};

[1,2,3].forEach(print);
```
2、只使用“表达式”，不使用“语句”。

"表达式"（expression）是一个单纯的运算过程，总是有返回值；"语句"（statement）是执行某种操作，没有返回值。函数式编程要求，只使用表达式，不使用语句。也就是说，每一步都是单纯的运算，而且都有返回值。**既然每一步都有返回值，我们就可以在这个返回值的基础上进行一系列的操作。**
原因是函数式编程的开发动机，一开始就是为了处理运算（computation），不考虑系统的读写（I/O）。"语句"属于对系统的读写操作，所以就被排斥在外。
当然，实际应用中，不做I/O是不可能的。因此，编程过程中，函数式编程只要求把I/O限制到最小，不要有不必要的读写行为，保持计算过程的单纯性。

3、没有“副作用”

所谓“副作用”，指的是函数内部与函数外部的互动（最典型的情况，就是函数内的某些操作修改全局变量的值），产生运算以外的其它结果。

例如：
```
//或许函数的初衷是执行某些操作，但是无意间修改了全局变量。
var a = 1;
   function effect() {
       this.a = 5;
   }
   effect();
```
函数式编程强调没有“副作用”，意味着函数要保持独立，所有的功能就是返回一个新的值，没有其它行为，尤其不得修改外部变量的值。

4、不修改状态

上一点已经提到，函数式编程只是返回新的值，不修改系统变量。因此，不修改变量，也是它的一个重要特点。
其它类型的语言中，变量往往用来保存变量的“状态”，不修改变量，**即就是状态不能保存在变量中。函数式编程使用参数来保存状态**，最好的例子就是递归。
```
//需求：对一个字符串进行逆序输出
function reverse(string) {
      if(string.length == 0) {
          return string;
      } else {
          return reverse(string.substring(1,string.length)) + string.substring(0,1);
      }
  }

console.log(reverse("AFeng")); //gneFA
```
5、引用透明

引用透明：指的是函数的运行不依赖于外部的变量或“状态”，只依赖于输入的参数，任何时候只要函数的参数相同，引用函数所得到的返回值总是相同的。
在其它类型的语言中，返回值往往与系统的状态有关，不同的状态之下，返回值是一样的，这就叫“引用不透明”。

# 三、如何解决函数式编程多层嵌套问题——链式优化
```
  //优化写法
  var utils = {
        chain(a) {
            this.temp = a;
            //chain函数为方法调用模式，
            // 所以这里的this指的是utils这个对象
            return this;
            //每一次都把这个对象返回，
            // 是为了下一次继续以这个对象为母体，进行链式操作
        },
        sum(b) {
            this.temp += b;
            return this;
        },
        sub(b) {
            this.temp -= b;
            return this;
        },
        value() {
            var finalValue = this.temp;
            this.temp = undefined;
            return finalValue;
        }
    };

    console.log(utils.chain(1).sum(3).value());
```
这样改写后，结构会整体变得比较清晰，而且链的每一环在做什么操作从函数名就可以直接看出。

# 四、函数式编程的意义
1、代码简洁，开发快速。
函数式编程大量使用函数，减少了代码的重复，因此程序比较短，开发速度较快。
如果程序员每天所写的代码行数基本相同，这就意味着，"C语言需要一年时间完成开发某个功能，Lisp语言只需要不到三星期。反过来说，如果某个新功能，Lisp语言完成开发需要三个月，C语言需要写五年。

2、接近自然语言，易于理解。
函数式编程的自由度很高，可以写出很接近自然语言的代码。

3、更方便的代码管理。
函数式编程不依赖、也不会改变外界的状态，只要给定输入参数，返回的结果必定相同。因此，每一个函数都可以被看做独立单元，很有利于进行单元测试（unit testing）和除错（debugging），以及模块化组合。

4、易于“并发编程”。
函数式编程不需要考虑"死锁"（deadlock），因为它不修改变量，所以根本不存在"锁"线程的问题。不必担心一个线程的数据，被另一个线程修改，所以可以很放心地把工作分摊到多个线程，部署"并发编程"（concurrency）。

5、代码的热升级。
函数式编程没有副作用，只要保证接口不变，内部实现是外部无关的。所以，可以在运行状态下直接升级代码，不需要重启，也不需要停机。

# 五、常见函数式编程模型
## 闭包（Closure）
可以保留局部变量不被释放的代码块，被称为一个闭包。
如何创建一个闭包
```
    //创建一个闭包
    function makeCounter() {
        var k = 0;

        return function() {
            return ++k;
        };
    }

    var counter = makeCounter();
    console.log(counter());
    console.log(counter());
```
调用makeCounnter()函数，在返回的函数中，对局部变量k，进行了引用，**导致局部变量无法在外层函数执行结束后 ，被系统回收掉，从而产生了闭包。**而这个闭包的作用就是“**保留住”局部变量。使得内层函数调用时，可以重复使用该变量。**而不同于全局变量，该变量只能在函数内部被引用。
换句话说：**闭包其实就是创造出了一些函数私有的“持久化变量”**。

## 闭包的创造条件

 - 存在内、外两层函数。
 - 内层函数对外层函数的局部变量进行了引用。

## 闭包的用途
闭包的主要用途就是可以定义一些作用域局限的持久化变量，这些变量可以用来做缓存或者计算的中间量等等。
```
//利用闭包创建一个缓存容器
    var cache = (function() {
        var store = {};

        return {
            get(key) {
                return store[key];
            },
            set(key,value) {
                store[key] = value;
            }
        }
   }());
   //这里定义的是一个IIFE函数，所以现在cache实际上存储的是拥有set和get方法的这个对象。

   cache.set("AFeng",33);
   console.log(cache.get("AFeng"));
```
上面例子是一个简单的缓存工具的实现，匿名函数创造了一个闭包，使得 store 对象 ，一直可以被引用，不会被回收。
## 闭包的弊端
持久化变量不会被正常释放，持续占用内存空间，很容易造成内存空间的浪费，所以一般需要额外手动的清理机制。

还有其它的函数式编程模型，但现在看的不是很懂。以后再添加。

# 总结：javascript函数式编程并不只是高阶函数算函数式编程，其它诸如普通函数的组合调用、链式结构，都是函数式编程的范畴。核心是它们都是以函数为主要载体的编程方式。



####参考资料
淘宝前端团队：<http://taobaofed.org/blog/2017/03/16/javascript-functional-programing/>
阮一峰老师教程：<http://www.ruanyifeng.com/blog/2012/04/functional_programming.html>