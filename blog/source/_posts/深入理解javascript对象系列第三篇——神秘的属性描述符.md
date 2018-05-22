---
title: 深入理解javascript对象系列第三篇——神秘的属性描述符
date: 2018-05-22 09:00:35
tags: 随笔
toc: true
---

# 前面的话
对于操作系统中的文件，我们可以轻易的将其设置为只读、隐藏、系统文件、普通文件。对于对象来说，属性描述符提供了类似的功能，**用来描述对象中属性的值，是否可以配置，是否可以修改以及是否可枚举。**

# 一、属性描述符类型：
**对象的属性描述符的类型分为两种：数据属性和访问器属性。**
## 1、数据属性
数据属性（data property）包含一个数据值的位置，在这个位置可以读取和写入值。数据属性有4个特性。
【1】：configurable（可配置性）
可配置性决定是否可以使用delete删除属性，以及是否可以修改属性描述符的特性，默认为true。
【2】：enumerable（可枚举性）
可枚举性决定属性是否出现在对象的属性枚举中，比如是否可以通过for-in循环遍历并返回该属性，默认值为true。
【3】：writable（可写性）
可写性决定是否可以修改属性的值，默认为true。
【4】：value（属性值）
属性值包含这个属性的数据值，读取属性值的时候，从这个位置读；写入属性值的时候，把新值保存在这个位置。默认值为undefined。

<!-- more -->

## 2、访问器属性
对象属性是名字、值和一组属性描述符构成的。而属性值可以用一个或两个方法代替，这两个方法是getter和setter而这种属性类型叫访问器属性（accessor property）。

【1】：configurable（可配置性）
可配置性决定是否可以使用delete删除属性，以及是否可以修改属性描述符的特性，默认为true。
【2】：enumerable（可枚举性）
可枚举性决定属性是否出现在对象的属性枚举中，比如是否可以通过for-in循环遍历并返回该属性，默认值为true。
【3】：getter
在读取属性时调用的函数。默认值为undefined。
【4】：setter
在写入属性值时调用的函数，默认值时undefined。

和数据属性不同，访问器属性不具有可写性(Writable)。如果属性同时具有getter和setter方法，那么它是一个读/写属性。如果它只有getter方法，那么它是一个只读属性。如果它只有setter方法，那么它是一个只写属性。读取、只写属性（意味着没有写入值）总是返回undefined。

# 二、描述符详解
## 1、可写性（writable）：决定是否可以修改属性的值，默认是true。
```
var o = {a:1};
o.a = 2;
console.log(o.a);//2
```
设置了writable后，赋值语句会失效。
```
var o = {a:1};
Object.defineProperty(o,'a',{
    writable:false
});
console.log(o.a);//1
//由于设置了writable为false，所以o.a=2这个语句会静默失效
o.a = 2;
console.log(o.a);//1
Object.defineProperty(o,'a',{
    writable:true
});
//由于writable设置为true，所以o.a可以被修改为2
o.a = 2;
console.log(o.a);//2
```

在严格模式下通过赋值语句为writable为false的属性赋值，会提示类型错误TypeError。
```
'use strict';
var o = {a:1};
Object.defineProperty(o,'a',{
    writable:false
});
//Uncaught TypeError: Cannot assign to read only property 'a' of object '#<Object>'
o.a = 2;
```
[注意点]：设置writable:false后，通过Object.defineProperty()方法改变属性value的值不会受影响，**因为这也意味着在重置writable的属性值为false（目前对这句话不是很理解）**
```
var o = {a:1};
Object.defineProperty(o,'a',{
    writable:false
});
console.log(o.a);//1
Object.defineProperty(o,'a',{
    value:2
});
console.log(o.a);//2
```

## 2、可配置性（configurable）:可配置性决定是否可以使用delete删除属性，以及是否可以修改属性描述符的特性，默认值为true。
【1】：设置configurable:false后，无法使用delete删除属性。
```
var o = {a:1};
Object.defineProperty(o,'a',{
    configurable:false
});
delete o.a;//false
console.log(o.a);//1
```
在严格模式下删除configurable:false的属性，会提示类型错误的TypeError。
```
'use strict';
var o = {a:1};
Object.defineProperty(o,'a',{
    configurable:false
});
//Uncaught TypeError: Cannot delete property 'a' of #<Object>
delete o.a;
```
[注意点]：使用var命令声明变量时，变量的configurable为false。
```
var a = 1;
//{value: 1, writable: true, enumerable: true, configurable: false}
Object.getOwnPropertyDescriptor(this,'a');
```

一般的，设置configurable:false后，将无法再使用defineProperty()方法来修改属性描述符。
```
var o = {a:1};
Object.defineProperty(o,'a',{
    configurable:false
});
//Uncaught TypeError: Cannot redefine property: a
Object.defineProperty(o,'a',{
    configurable:true
});
```
有一个例外，设置Configurable:false后，只允许writable的状态从true变为false。
```
var o = {a:1};
Object.defineProperty(o,'a',{
    configurable:false,
    writable:true
});
o.a = 2;
console.log(o.a);//2
Object.defineProperty(o,'a',{
    writable:false
});
//由于writable:false生效，对象a的o属性无法修改值，所以o.a=3的赋值语句静默失败
o.a = 3;
console.log(o.a);//2
```
## 3、可枚举性（enumerable）:可枚举性决定属性是否出现在对象的枚举属性中，具体来说就是for-in循环、Object.keys()方法、JSON.stringify()方法是否会取到该属性。

**用户定义的普通属性默认是可枚举的，而原生继承的属性默认是不可枚举的。**
```
//由于原生继承的属性默认不可枚举，所以只取得自定义的属性a:1
var o = {a:1};
for(var i in o){
    console.log(o[i]);//1
}
```
```
//由于enumerable被设置为false，在for-in循环中a属性无法被枚举出来
var o = {a:1};
Object.defineProperty(o,'a',{enumerable:false});
for(var i in o){
    console.log(o[i]);//undefined
}
```
ropertyIsEnumerable()：用于判断对象的属性是否可枚举。
```
var o = {a:1};
console.log(o.propertyIsEnumerable('a'));//true
Object.defineProperty(o,'a',{enumerable:false});
console.log(o.propertyIsEnumerable('a'));//false
```
## 4、get和set
get是一个隐藏的函数，在获取属性值时调用。set也是一个隐藏函数，在设置值时调用，它们的默认值都是undefined。**Object.definedProperty()中的get和set对应于对象字面量中get和set方法，[注意]getter和setter取代了数据属性中的value和writable属性。（这句不是很懂）**
【1】：给只设置get方法，没有设置set方法的对象赋值会静默失败，在严格模式下则报错。
```
var o = {
    get a(){
        return 2;
    }
}    
console.log(o.a);//2
//由于没有设置set方法，所以o.a=3的赋值语句会静默失败
o.a = 3;
console.log(o.a);//2
```
```
Object.defineProperty(o,'a',{
    get: function(){
        return 2;
    }
})
console.log(o.a);//2
//由于没有设置set方法，所以o.a=3的赋值语句会静默失败
o.a = 3;
console.log(o.a);//2
```
在严格模式下，给没有设置set方法的访问器属性赋值会报错。
```
'use strict';
var o = {
    get a(){
        return 2;
    }
}    
console.log(o.a);//2
//由于没有设置set方法，所以o.a=3的赋值语句会报错
//Uncaught TypeError: Cannot set property a of #<Object> which has only a getter
o.a = 3;
```
```
'use strict';
Object.defineProperty(o,'a',{
    get: function(){
        return 2;
    }
})
console.log(o.a);//2
//由于没有设置set方法，所以o.a=3的赋值语句会报错
//Uncaught TypeError: Cannot set property a of #<Object> which has only a getter
o.a = 3;
```
【2】：只设置set方法，而不设置get方法，则对象的属性值为undefined。
```
var o = {
    set a(val){
        return 2;
    }
}    
o.a = 1;
console.log(o.a);//undefined
```
```
Object.defineProperty(o,'a',{
    set: function(){
        return 2;
    }
})
o.a = 1;
console.log(o.a);//undefined
```
【3】：一般地，get和set是成对出现的。
```
var o ={
    get a(){
        return this._a;
    },
    set a(val){
        this._a = val*2;
    }
}
o.a = 1;
console.log(o.a);//2
```
```
Object.defineProperty(o,'a',{
    get: function(){
        return this._a;
    },
    set :function(val){
        this._a = val*2;
    }
})
o.a = 1;
console.log(o.a);//2
```
# 三、描述符的方法
**前面介绍了属性描述符，要想设置它们，就需要用到描述符方法。描述符方法总共有以下4个：**
## 1、Object.getOwnPropertyDescriptor(obj,name)：用于查询一个属性的描述符，并以对象的形式返回。

查询obj.a属性时，可配置性、可枚举性、可写性都是默认的true，而value是a的属性值1
查询obj.b属性时，因为obj.b属性不存在，该方法返回undefined
```
var obj = {a:1};
//因为是以对象字面量的形式定义的，所以未配置的属性描述符都是true.
//Object {value: 1, writable: true, enumerable: true, configurable: true}
console.log(Object.getOwnPropertyDescriptor(obj,'a'));
//undefined
console.log(Object.getOwnPropertyDescriptor(obj,'b'));
```
## 2、：Object.defineProperty(obj,name,desc)：用于创建或配置对象的一个属性描述符，返回配置后的对象。
使用该方法创建或配置对象属性的描述符时，如果不针对该属性进行描述符的配置，则该项描述符默认为false。
```
var obj = {};
//{a:1}
console.log(Object.defineProperty(obj,'a',{
        value:1,
        writable: true
    }));

//由于没有配置enumerable和configurable，所以它们的值为false
//{value: 1, writable: true, enumerable: false, configurable: false}
console.log(Object.getOwnPropertyDescriptor(obj,'a'));
```
## 3、Object.defineProperty(o,desc)：用于创建或配置对象的多个属性的描述符，然后返回配置后的对象。
```
  var obj = {
        a:1
    };
    
    console.log(Object.defineProperties(obj,{
        a:{writable:false},
        b:{value:2}
    })); //{a: 1, b: 2}

    console.log(Object.getOwnPropertyDescriptor(obj,"a"));
    //{value: 1, writable: false, enumerable: true, configurable: true}
   //由于这个属性是直接在程序中定义的，所以当不去配置其余属性描述符是默认为true
    console.log(Object.getOwnPropertyDescriptor(obj,"b"));
    //由于这个属性是以属性描述符的形式定义的，所以当不去人为的配置的时候，默认都是false.
    //{value: 2, writable: false, enumerable: false, configurable: false}
```
## 4、：Object.create(proto,desc)使用指定的原型和属性来创建一个对象。
```
 var o = Object.create(Object.prototype,{
        a: {
            writable :false, value: 1, enumerable: true
        }
    });

    console.log(Object.getOwnPropertyDescriptor(o,"a"));
    //{value: 1, writable: false, enumerable: true, configurable: false}
    //由于这个属性是以属性描述符的形式定义的，所以当不去人为的配置的时候，默认都是false.
```

# 四、对象状态
属性描述符只能用来控制对象中的一个属性的状态。而如果要控制对象的状态，则要用下面的6中方法。

## 1、Object.preventExtensions()【禁止扩展】：使一个对象无法再添加新的属性，并返回当前对象。
## 2、Object.isExtensible()【测试是否可以扩展】：用来检测该对象是否可以扩展。
```
var o = {a:1};
console.log(Object.isExtensible(o));//true
o.b = 2;
console.log(o);//{a: 1, b: 2}
console.log(Object.preventExtensions(o));//{a: 1, b: 2}
//禁止扩展，并且返回这个对象。
//由于对象o禁止扩展，所以该赋值语句静默失败
o.c = 3;
console.log(Object.isExtensible(o));//false
console.log(o);//{a: 1, b: 2}
```
在严格模式下，给禁止扩展的对象添加属性会报TypeError错误。
```
'use strict';
var o = {a:1};
console.log(Object.preventExtensions(o));//{a:1}
//Uncaught TypeError: Can't add property c, object is not extensible
o.c = 3;
```
Object.preventExtensions()方法并不改变对象中属性的描述符状态。
```
var o = {a:1};
//{value: 1, writable: true, enumerable: true, configurable: true}
console.log(Object.getOwnPropertyDescriptor(o,'a'));
Object.preventExtensions(o);
//{value: 1, writable: true, enumerable: true, configurable: true}
console.log(Object.getOwnPropertyDescriptor(o,'a'));
```
## 3、Object.seal()【对象封印】
对象封印又叫对象密封，使一个对象不可扩展并且所有属性不可配置（configurable:false）,并返回当前这个对象。
## 4、Object.isSealed()【测试封印】：用来检测该对象是否被封印。
```
var o = {a:1,b:2};
console.log(Object.isSealed(o));//false
console.log(Object.seal(o));//{a:1,b:2}
console.log(Object.isSealed(o));//true
console.log(delete o.b);//false
o.c = 3;
console.log(o);//{a:1,b:2}
```
在严格模式下，删除旧属性或添加新属性都会报错。
```
'use strict';
var o = {a:1,b:2};
console.log(Object.seal(o));//{a:1,b:2}
//Uncaught TypeError: Cannot delete property 'b' of #<Object>
delete o.b;
```
**这个方法实际上会在现有对象上调用Object.preventExtensions()方法，并把所有现有属性的configurable描述符设置为false。**
```
var o = {a:1,b:2};
//{value: 1, writable: true, enumerable: true, configurable: true}
console.log(Object.getOwnPropertyDescriptor(o,'a'));
console.log(Object.seal(o));//{a:1,b:2}
//{value: 1, writable: true, enumerable: true, configurable: false}
console.log(Object.getOwnPropertyDescriptor(o,'a'));
```
## 5、Object.freeze()【对象冻结】：使一个对象不可扩展、不可配置、也不可改写、变成一个仅仅可以枚举的只读常量，并返回当前对象。
## 6、Object.isFrozen()【监测是否被冻结】
```
var o = {a:1,b:2};
console.log(Object.isFrozen(o));//false
console.log(Object.freeze(o));//{a:1,b:2}
console.log(Object.isFrozen(o));//true
o.a = 3;
console.log(o);//{a:1,b:2}
```
在严格模式下，删除旧属性，添加新属性，更改现有属性都会报错。
```
'use strict';
var o = {a:1,b:2};
console.log(Object.freeze(o));//{a:1,b:2}
//Uncaught TypeError: Cannot assign to read only property 'a' of object '#<Object>'
o.a = 3;//更改现有属性
```
这个方法实际上会在现有对象上调用Object.seal()方法，并把所有现有属性的writable描述符设置为false。
```
var o = {a:1};
//{value: 1, writable: true, enumerable: true, configurable: true}
console.log(Object.getOwnPropertyDescriptor(o,'a'));
console.log(Object.freeze(o));//{a:1}
//{value: 1, writable: false, enumerable: true, configurable: false}
console.log(Object.getOwnPropertyDescriptor(o,'a'));
//不可扩展，不可配置，也不可改写。
```

转载自：小火柴的蓝色理想 
http://www.cnblogs.com/xiaohuochai/p/5613593.html 
在阅读老师的博客时，我把它当做看书来对待，所以我的博客大部分来自老师的原话以及实例，再加上自已的一些理解，并且做了适当的标注，还有对老师的代码进行了一些测试。

文章标签： 深入理解javascript函数系列第六篇——高阶函数