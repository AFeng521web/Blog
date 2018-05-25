---
title: 深入理解javascript作用域系列第五篇——一张图理解执行环境和作用域
date: 2018-05-25 10:44:43
tags: 随笔
toc: true
---

# 前面的话
对于执行环境（execution contexr）和作用域（scope）并不容易区分，甚至很多人认为它们就是一回事，只是高程和犀牛书关于作用域的两种不同翻译而已。但实际上，它们并不相同，却相互纠缠在一起。

# 一、概念
**作用域：作用域是一套规则，用于确定在何处以及如何查找标识符。关于LHS查询和RHS查询详见作用域系列第一篇内部原理。**

**作用域分为词法作用域和动态作用域。javascript使用词法作用域，简单的说，词法作用域就是定义在词法阶段的作用域，是由写代码时将变量和函数写在哪里来决定的。于是词法作用域也可以描述为程序源代码中定义变量和函数的区域。**

**作用域可以分为全局作用域和函数作用域，函数作用域可以互相嵌套。**

<!-- more -->

在下面的例子中，存在着全局作用域，fn作用域和bar作用域，它们相互嵌套。
![这里写图片描述](https://img-blog.csdn.net/20180505215951646?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzk3MjcyMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

【作用域链和自由变量】
各个作用域的嵌套关系组成了一条作用域链，例子中bar函数保存的作用域链是bar--> fn --> 全局，fn函数保存的作用域链是fn --> 全局。

使用作用域链主要是进行表示符的查询，标识符解析就是沿着作用域链一级一级地搜素标识符的过程，而作用域链就是要保证对变量和函数的有序访问。

【1】：如果自身作用域中声明了该变量，则无需使用作用域链。
在下面的例子中，如果要在bar函数中查询变量a，则直接使用LHS查询（找到变量容器，并赋值），赋值为100即可。

```
var a = 1;
var b = 2;
function fn(x){
    var a = 10;
    function bar(x){
        var a = 100;
        b = x + a;
        return b;
    }
    bar(20);
    bar(200);
}
fn(0);
```
【2】：如果自身作用域中未声明该变量，那么需要使用作用域链进行查找。

这时，就引入了另一个概念——自由变量。在当前作用域中存在但未在当前作用域中声明的变量叫作自由变量。
在下面的例子中，如果要在bar函数中查询变量b，由于b并没有在当前作用域中声明，所以b是自由变量。bar函数的作用域链是bar --> fn --> 全局。到上一级fn作用域中查找b没有找到，继续到再上一级全局作用域中查找b，就找到了b。
```
var a = 1;
var b = 2;
function fn(x){
    var a = 10;
    function bar(x){
        var a = 100;
        b = x + a;
        return b;
    }
    bar(20);
    bar(200);
}
fn(0);
```
【注意】：如果表示符没有找到，则需要分为RHS和LHS查询进行分析，若进行的是LHS查询，则在全局环境中声明该变量，若是严格模式下的LHS查询，则抛出ReferenceError(引用错误)异常；若进行的是RHS查询，则抛出ReferenceError(引用错误)异常。

【执行环境】：执行环境（execution context）,有时也称为执行上下文，执行上下文环境或者环境，定义了变量或函数有权访问的其他数据。每个执行环境都有一个与之关联的变量对象（variable object）,环境中定义的所有变量和函数都保存在这个对象中。

一定要区分执行环境和变量对象。执行环境会随着函数的调用和返回，不断的重建和销毁。但变量对象在有变量引用（如闭包）的情况下，将留在内存空间中不被销毁。

![这里写图片描述](https://img-blog.csdn.net/20180505225651483?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzk3MjcyMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

这是这个例子中的代码执行到第15行时fn(0)函数时的执行环境，执行环境里的变量对象保存了fn()函数作用域内所有的变量和函数的值。

![这里写图片描述](https://img-blog.csdn.net/2018050523000323?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzk3MjcyMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

【执行流】：代码的执行顺序叫做执行流，程序源代码并不是按照代码的书写顺序一行一行往下执行的，而是和函数的调用顺序有关。

例子中的执行流是第1行 -> 第2行 -> 第4行 -> 第15行 -> 第5行 -> 第7行 -> 第12行 -> 第8行 -> 第9行 -> 第10行 -> 第11行 -> 第13行 -> 第8行 -> 第9行 -> 第10行 -> 第11行 -> 第14行。

![这里写图片描述](https://img-blog.csdn.net/20180505230213854?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzk3MjcyMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

【注意点】：在程序代码执行之前存在着“编译”和“声明提升”的过程。本例中假设代码是已经经过声明提升过程之后的代码。

【执行环境栈】：执行环境栈类似于作用域链，有序的保存着当前程序中存在的执行环境。当执行流进入一个函数时，函数的环境就会被推入一个环境栈中，而在函数执行完之后，栈将其弹出，把控制权返回给之前的执行环境。javascript程序中的执行流正是由这个机制控制着。

在例子中，当执行流进入bar(20)函数时，当前程序的执行环境栈如下图所示。其中黄色的bar(20)执行环境表示当前程序正处在此执行环境中。
![这里写图片描述](https://img-blog.csdn.net/20180505231206161?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzk3MjcyMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

当bar(20)函数执行完成后，当前程序的执行环境栈如下所示，bar(20)函数的执行环境被销毁，等待垃圾回收，控制权交还给黄色背景的fn(0)执行环境。

![这里写图片描述](https://img-blog.csdn.net/20180505231705146?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzk3MjcyMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

# 二、整个过程的说明
下面按照代码执行流的顺序对该图示代码进行详细说明。
![这里写图片描述](https://img-blog.csdn.net/20180505231908451?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzk3MjcyMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

【1】：代码执行流进入全局执行环境，并对全局执行环境中的代码进行声明提升。
![这里写图片描述](https://img-blog.csdn.net/20180505232043760?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzk3MjcyMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

【2】：执行流执行第一行代码 var a = 1;对a进行LHS查询，给a赋值1；执行流执行第二行代码var b = 2；对b进行LHS查询，给b赋值2。
![这里写图片描述](https://img-blog.csdn.net/20180505232347269?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzk3MjcyMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

【3】：执行流执行第15行代码fn(0)函数，此时执行流进入fn(0)函数执行环境中,对该执行环境中的代码进行声明提升过程，并将实参0赋值给形参x。此时执行环境栈中存在两个执行环境，fn(0)函数为当前执行流所在的执行环境。
![这里写图片描述](https://img-blog.csdn.net/20180505232759946?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzk3MjcyMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

【4】执行流执行第5行代码 var a = 10；对a进行LHS查询，给a赋值10。
![这里写图片描述](https://img-blog.csdn.net/20180505232938263?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzk3MjcyMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

【5】：执行流执行第12行代码bar(20)；调用bar(20)函数，此时执行流进入bar(20)函数执行环境中，对该执行环境中的代码进行声明提升过程，并将实参20赋值给形参x。此时执行环境栈中存在三个执行环境，bar(20)函数为当前执行流所在的执行环境。

在声明提升的过程中，由于b是个自由变量，需要通过bar()函数的作用域链bar() --> fn()--> 全局作用域进行查找，最终在全局作用域中也就是代码第2行找到var b = 2;，然后在全局执行环境中找到b的值是2，所以给b赋值2。

![这里写图片描述](https://img-blog.csdn.net/20180505233605806?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzk3MjcyMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

【6】：执行流执行第8行代码var a = 100，给a赋值100；执行流执行第9行b = x + a；对x进行RHS查询，找到x的值是20，对a进行RHS查询，找到a的值是100，所以通过计算b的值是120，给b赋值120；执行第10行代码return b;，对b进行RHS查询，找到b的值是120，所以函数返回值为120。
![这里写图片描述](https://img-blog.csdn.net/20180505233838186?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzk3MjcyMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

【7】：执行流执行完第10行代码后，bar(20)的执行环境被弹出执行环境栈，并被销毁，等待垃圾回收，控制权交还给fn(0)函数的执行环境。

![这里写图片描述](https://img-blog.csdn.net/20180505234017397?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzk3MjcyMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

【8】：bar(200)的调用过程与bar(20)一样，这里不再给出。

【9】：执行流执行到第14行，fn(0)的执行环境被弹出执行环境栈，并被销毁，等待垃圾回收，控制权交还给全局执行环境。

![这里写图片描述](https://img-blog.csdn.net/20180505235156802?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzk3MjcyMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

【10】：当页面关闭时，全局执行环境被销毁，页面再无执行环境。

![这里写图片描述](https://img-blog.csdn.net/20180505235325144?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzk3MjcyMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

# 三、总结
## 1、javascript使用的是词法作用域，对于函数来说，词法作用域在函数定义时就已经确定了，与函数是否被调用无关。通过作用域，可以知道作用域范围内的变量和函数有哪些，却不知道变量的值是什么，所以作用域是静态的。

[注意]通过eval()函数和with语句可以对作用域进行动态修改

## 2、对于函数来说，执行环境是在函数调用时确定（创建）的，执行环境（中的变量对象）包含作用域内所有变量和函数的值，在同一作用域下，不同的调用（如传递不同的参数）会产生不同的执行环境。从而产生不同的变量的值，所以执行环境是动态的。

### 转载自：小火柴的蓝色理想
<http://www.cnblogs.com/xiaohuochai/p/5613593.html>
在阅读老师的博客时，我把它当做看书来对待，所以我的博客大部分来自老师的原话以及实例，再加上自已的一些理解，并且做了适当的标注，还有对老师的代码进行了一些测试。