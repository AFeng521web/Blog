---
title: 深入理解javascript对象系列第四篇——对象拷贝
date: 2018-05-22 09:01:04
tags: 随笔
toc: true
---

# 前面的话
**对象拷贝分为浅拷贝（shallow）和深拷贝（deep）两种。浅拷贝只复制一层对象的属性，并不会进行递归复制，*而javascript存储对象时都只是存储对象的地址。* 所以浅拷贝会导致对象中的子对象指向同一块内存地址（修改一个对象会影响另一个）。而深拷贝则不同，它不仅将原对象的各个属性逐个复制出去。而且将原对象上各个属性所包含的对象也依次采用深拷贝的方法递归复制到新对象上，拷贝了所有层级。**

# 一、浅拷贝
## 一、简单拷贝
新建一个空对象，利用```for-in```循环，将对象的所有属性复制到新创建的空对象中。
```
function simpleClone1(obj) {
        if(typeof obj != "object") {
            //如果传的不是一个对象，则直接退出
            return false;
        }

        var cloneObj = {};
        for(var i in obj)  {
            cloneObj[i] = obj[i];
        }
        return cloneObj;
    }

   var obj1={a:1,b:2,c:[1,2,3]};
   var obj2=simpleClone1(obj1);
   console.log(obj1.c); //[1,2,3]
   console.log(obj2.c); //[1,2,3]
   obj2.c.push(4);
   console.log(obj2.c); //[1,2,3,4]
   console.log(obj1.c); //[1,2,3,4]
```

<!-- more -->

## 二、使用属性描述符
通过对象的原型，建立一个空的实例对象。通过forEach语句，获取到对象的所有属性的属性描述符，将其作为参数，设置到新建的空实例对象中。
```
function simpleClone2(origObj) {
        //利用传入对象的原型创建一个空对象，这个新对象和原对象指向同一个原型
        var copy = Object.create(Object.getPrototypeOf(origObj));
        //遍历原始对象的所有属性名
        Object.getOwnPropertyNames(origObj).forEach(function(propKey) {
            //得到原始对象属性的属性描述符
            var desc = Object.getOwnPropertyDescriptor(origObj,propKey);
            //把这些属性和对应的属性描述符重新定义到待赋值的空对象上
            Object.defineProperty(copy,propKey,desc);
        });
        return copy;
    }

   var obj1={a:1,b:2,c:[1,2,3]};
   var obj2=simpleClone1(obj1);
   console.log(obj1.c); //[1,2,3]
   console.log(obj2.c); //[1,2,3]
   obj2.c.push(4);
   console.log(obj2.c); //[1,2,3,4]
   console.log(obj1.c); //[1,2,3,4]
```
## 三、使用jquery的extend()方法。
```
var obj1={a:1,b:2,c:[1,2,3]};
var obj2=$.extend({},obj1);
console.log(obj1.c); //[1,2,3]
console.log(obj2.c); //[1,2,3]
obj2.c.push(4);
console.log(obj2.c); //[1,2,3,4]
console.log(obj1.c); //[1,2,3,4]
```

# 二、深拷贝
## 1、遍历复制
复制对象的属性时，对其进行判断，如果是数组或对象，则再次调用拷贝函数；否则，直接复制对象属性。
```
function deepClone1(obj,cloneObj) {
        if(typeof obj != "object") {
             return false;
        }
        var cloneObj = cloneObj || {};

        for(var i in obj) {
            //如果是对象的子对象，则递归复制
            if(typeof obj[i] === "object") {
                //判断该构造函数(Array)的原型是否存在于该对象(obj[i])的原型链上
                cloneObj[i] = (obj[i] instanceof Array)? []:{};
                //递归复制
                arguments.callee(obj[i],cloneObj[i]);
            } else {
                cloneObj[i] = obj[i];
            }
        }
        return cloneObj;
    }

   var obj1={a:1,b:2,c:[1,2,3]};
   var obj2=deepClone1(obj1);
   console.log(obj1.c); //[1,2,3]
   console.log(obj2.c); //[1,2,3]
   obj2.c.push(4);
   console.log(obj2.c); //[1,2,3,4]
   console.log(obj1.c); //[1,2,3]
```

## 2、JSON
用JSON全局对象的parse和stringify方法来实现深复制算是一个简单讨巧的方法，它能正确处理的对象只有Number、String、Boolean、Array、扁平对象，即那些能够被json直接表示的数据结构。
```
function jsonClone(obj){
       return JSON.parse(JSON.stringify(obj));
       //把传入的对象初始化一个字符串，在以这个字符串为依据创建一个新的对象，
       // 之所以会实现深拷贝，是因为这个对象是以传入的原对象为基础的，
       // 它们在不同的内存空间中，所以操作一个不会影响另一个。
   }
   var obj1={a:1,b:2,c:[1,2,3]};
   var obj2=jsonClone(obj1);
   console.log(obj1.c); //[1,2,3]
   console.log(obj2.c); //[1,2,3]
   obj2.c.push(4);
   console.log(obj2.c); //[1,2,3,4]
   console.log(obj1.c); //[1,2,3]
```
## 3、使用jquery的extend()方法。
```
var obj1={a:1,b:2,c:[1,2,3]};
var obj2=$.extend(true,{},obj1);
console.log(obj1.c); //[1,2,3]
console.log(obj2.c); //[1,2,3]
obj2.c.push(4);
console.log(obj2.c); //[1,2,3,4]
console.log(obj1.c); //[1,2,3]
```

转载自：小火柴的蓝色理想 
http://www.cnblogs.com/xiaohuochai/p/5613593.html 
在阅读老师的博客时，我把它当做看书来对待，所以我的博客大部分来自老师的原话以及实例，再加上自已的一些理解，并且做了适当的标注，还有对老师的代码进行了一些测试。

