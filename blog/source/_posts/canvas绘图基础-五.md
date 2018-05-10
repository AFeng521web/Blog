---
title: canvas绘图基础(五)
date: 2018-05-10 20:20:07
tags: 随笔
---

## 一、阴影
context.shadowColor="gray";//指定阴影的颜色
context.shadowOffsetX=-20;//指定阴影的位移值
context.shadowOffsetY=-20;//指定阴影的位移值
context.shadowBlur=5;//描述阴影模糊的程度（值越大越模糊）
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<canvas id="canvas" style="border: 1px solid #000; display: block; margin:50px auto">
    您的浏览器不支持canvas画布！
</canvas>
<script type="text/javascript">
    window.onload = function () {
        var canvas = document.getElementById("canvas");
        canvas.width = 1024;
        canvas.height = 800;
        var context = canvas.getContext("2d");

        context.fillStyle = "#058";
        context.shadowColor = "gray";
        context.shadowOffsetX = 15;
        context.shadowOffsetY = 15;
        //负值时阴影方向相反
        context.shadowOffsetX = -15;
        context.shadowOffsetY = -15;
        context.shadowBlur = 15;//值越大越模糊
        context.fillRect(200,200,400,400);
    }
</script>
</body>
</html>
```
<!-- more -->

## 二、global属性
### globalAlpha
context.globalAlpha=1;默认值，在这种情况下默认绘制的图形都不具有透明度，即后绘制的图形会盖住之前绘制的图形。
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<canvas id="canvas" style="border: 1px solid #000; display: block; margin: 50px auto">

</canvas>
<script type="text/javascript">
    window.onload = function () {
        var canvas = document.getElementById("canvas");
        canvas.width = 800;
        canvas.height = 800;
        var context = canvas.getContext("2d");

        for(var i=0; i<100; i++) {
            var R = Math.floor(Math.random()*255);
            var G = Math.floor(Math.random()*255);
            var B = Math.floor(Math.random()*255);
            context.fillStyle = "rgb("+R+","+G+","+B+")";
            context.beginPath();
            context.arc(Math.random()*canvas.width,Math.random()*canvas.height,
                Math.random()*60, 0,Math.PI*2);
            context.fill();
        }
    }
</script>
</body>
</html>
```
### globalCompositeOperation（描述绘制的图形在重叠时所产生的效果）
context.globalCompositeOperation="source-over";//后绘制的图形会盖住之前绘制的图形。
context.globalCompositeOperation="destination-over";//之前绘制的图形会盖住之后绘制的图形。
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<canvas id="canvas" style="border:solid #000; display: block; margin: 50px auto">

</canvas>
<script type="text/javascript">
    window.onload = function () {
        var canvas = document.getElementById("canvas");
        canvas.width = 800;
        canvas.height = 800;
        var context = canvas.getContext("2d");
        context.fillStyle = "blue";
        context.fillRect(100,200,400,400);

        context.globalCompositeOperation = "source-over";
        context.globalCompositeOperation = "destination-over";

        context.fillStyle = "red";
        context.beginPath();
        context.moveTo(400,300);
        context.lineTo(650,700);
        context.lineTo(150,700);
        context.closePath();
        context.fill();

    }
</script>
</body>
</html>
```
## 三、clip和剪辑区域
context.clip();此函数与路径规划函数一起使用，例如：lineTo、arc、贝塞尔曲线等等
此函数的意图是使用刚才设置的路径，把他剪切为当前的绘制环境，也就是说在之前，我们的绘制环境是整个canvas画布，使用clip()后，我们的绘制环境变为刚设置的路径的封闭部分。
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<canvas id="canvas" style="border: 1px solid #000; display: block; margin: 50px auto">

</canvas>
<script type="text/javascript">
    window.onload = function () {
        var canvas = document.getElementById("canvas");
        canvas.width = 800;
        canvas.height = 800;
        var context = canvas.getContext("2d");
        
        context.beginPath();
        context.fillStyle = "black";
        context.fillRect(0,0,canvas.width,canvas.height);

        context.beginPath();
        context.arc(400,400,150,0,Math.PI*2);
        context.fillStyle = "#fff";
        context.fill();
        context.clip();

        context.font = "bold 80px Arial";
        context.textAlign = "center";
        context.textBaseline = "middle";
        context.fillStyle = "#058";
        context.fillText("canvas",canvas.width/2,canvas.height/2);
    }
</script>
</body>
</html>
```
![这里写图片描述](http://img.blog.csdn.net/20171124172803087?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2VpeGluXzM3OTcyNzIz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## 四、路径方向和剪纸效果
### 非零环绕原则
用于判断射线在多边形的外面还是里面。
![这里写图片描述](http://img.blog.csdn.net/20171124173824709?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2VpeGluXzM3OTcyNzIz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

判断依据：
为零：在外面，不染色。
不为零：在里面，要染色。

一个最简单的例子
![这里写图片描述](http://img.blog.csdn.net/20171124174055284?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2VpeGluXzM3OTcyNzIz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

在环内取一点，很明显为0，所以在外面，不用染色。
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<canvas id="canvas" style="border: 1px solid #000; display: block;margin: 50px auto">
    您的浏览器不支持canvas画布！
</canvas>
<script type="text/javascript">
    window.onload = function () {
        var canvas = document.getElementById("canvas");
        canvas.width = 800;
        canvas.height = 800;
        var context =  canvas.getContext("2d");

        //绘制圆形
        context.beginPath();
        context.arc(400,400,300,0,Math.PI*2,false);
        context.arc(400,400,150,0,Math.PI*2,true);
        context.closePath();

        context.fillStyle = "#058";
        context.shadowColor = "gray";
        context.shadowOffsetX = 10;
        context.shadowOffsetY = 10;
        context.shadowBlur = 10;
        context.fill();
    }
</script>
</body>
</html>
```
### 剪纸效果
通过非零环绕原则，主要确定那个区域是否染色。
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<canvas id="canvas" style="border: 1px solid #000; display: block; margin: 50px auto">
    您的浏览器不支持canvas画布！
</canvas>
<script type="text/javascript">
    window.onload = function () {
        var canvas = document.getElementById("canvas");
        canvas.width = 800;
        canvas.height = 800;
        var context = canvas.getContext("2d");

        //绘制
        context.beginPath();
        context.rect(100,100,600,600);
        pathRect(context,200,200,400,200);
        pathTriangle(context,300,450,150,650,450,650);
        context.arc(550,550,100,0,Math.PI*2,true);
        context.closePath();

        context.fillStyle = "#058";
        context.shadowColor = "gray";
        context.shadowOffsetX = 10;
        context.shadowOffsetY = 10;
        context.shadowBlur = 10;
        context.fill();
    };

    function pathRect(cxt,x,y,width,height) {
        cxt.moveTo(x,y);
        cxt.lineTo(x,y+height);
        cxt.lineTo(x+width,y+height);
        cxt.lineTo(x+width,y);
        cxt.lineTo(x,y);
    }

    function pathTriangle(cxt,x1,y1,x2,y2,x3,y3) {
        cxt.moveTo(x1,y1);
        cxt.lineTo(x2,y2);
        cxt.lineTo(x3,y3);
        cxt.lineTo(x1,y1);
    }
</script>
</body>
</html>
```
![这里写图片描述](http://img.blog.csdn.net/20171124174529445?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2VpeGluXzM3OTcyNzIz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## 五、绘制椭圆
 ctx.ellipse(x, y, radiusX, radiusY, rotation,startAngle, endAngle,      anticlockwise);
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<canvas id="canvas" style="border: 1px solid #000; display: block; margin: 50px auto">
    您的浏览器不支持canvas画布！
</canvas>
<script type="text/javascript">
    window.onload = function () {
        var canvas = document.getElementById("canvas");
        canvas.width = 800;
        canvas.height = 800;

        var context = canvas.getContext("2d");
        //浏览器支持ellipse时绘制椭圆
        //void ctx.ellipse(x, y, radiusX, radiusY, rotation,
         startAngle, endAngle, anticlockwise);
        if(context.ellipse) {
            context.beginPath();
            context.ellipse(400,400,300,200,0,0,Math.PI*2);
            context.stroke();
        }
        else {
            alert("no ellipse");
        }
    }
</script>
</body>
</html>
```
## 六、使用canvas交互
isPointInPath(x,y)是 Canvas 2D API 用于判断在当前路径中是否包含检测点的方法。
返回值：一个Boolean值，当检测点包含在当前或指定的路径内，返回 true；否则返回 false。
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<canvas id="canvas" style="border: 1px solid #000; display: block; margin: 50px auto">
    您的浏览器不支持canvas画布！
</canvas>
<script type="text/javascript">
    var balls = [];
    var canvas = document.getElementById("canvas");
    var context = canvas.getContext("2d");
    window.onload = function () {
        canvas.width = 800;
        canvas.height = 800;
        for(var i=0; i<10; i++) {
            var aBall  = {
                x : Math.random()*canvas.width,
                y : Math.random()*canvas.height,
                r : Math.random()*50+20
            };
            balls[i] = aBall;
        }
        draw();
        canvas.addEventListener("mouseup",detect);
        //用canvas的addEventListener方法创建事件
    };
    function draw() {
        //一次for循环遍历balls数组
        for(var i=0; i<balls.length; i++) {
            context.beginPath();
            context.arc(balls[i].x,balls[i].y,balls[i].r,0,Math.PI*2);
            context.fillStyle = "#058";
            context.fill()
        }
    }
    
    function detect() {
        //isPointInPath 是canvas中内置的点击检测函数
        //context.isPointInPath(x,y)看传入的点(x,y)是否在当前规划的路径内。
        //下边的例子是绘制10个小球，当点击小球时它的颜色由蓝色变为红色，示例代码如下：
        //获得鼠标点击在canvas中位置的方法
        var x = event.clientX - canvas.getBoundingClientRect().left;
        //鼠标坐标基于Web文档的横向距离减去canvas画布离整个文档左侧的距离
        var y = event.clientY - canvas.getBoundingClientRect().top;

        for(var i=0; i<balls.length; i++) {
            context.beginPath();
            context.arc(balls[i].x,balls[i].y,balls[i].r,0,Math.PI*2);
            if(context.isPointInPath(x,y)) {
                context.fillStyle = "red";
                context.fill();
            }
        }
    }
</script>
</body>
</html>
```
## 七、扩展canvas中的context
设计自己的API，在context中加入新的方法，方法名为 fillStar。
调用对象的原型，对这个原型进行修改（函数当做对象来处理）
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<canvas id="canvas" style="border: 1px solid #000; display: block; margin: 50px auto">
    您的浏览器不支持canvas画布！
</canvas>
<script type="text/javascript">
    var canvas = document.getElementById("canvas");
    var context = canvas.getContext("2d");
    
    CanvasRenderingContext2D.prototype.fillStar = function (r,R,x,y,rot) {
        this.beginPath();
        for(var i=0; i<5; i++) {
            this.lineTo(Math.cos((18+i*72-rot)/180*Math.PI)*R+x,
                -Math.sin((18+i*72-rot)/180*Math.PI)*R+y);
            this.lineTo(Math.cos((54+i*72-rot)/180*Math.PI)*r+x,
                -Math.sin((54+i*72-rot)/180*Math.PI)*r+y);
        }
        this.closePath();
        this.fill();
    };
    window.onload = function () {
        canvas.width = 800;
        canvas.height = 800;
        context.fillStyle = "#058";
        context.fillStar(150,300,400,400,0);
    }
</script>
</body>
</html>
```
###本教程主要是根据慕课网刘宇波老师的canvas绘图详解视频教程总结而来。在此特别感谢刘老师的无私奉献。

###课程链接

<https://www.imooc.com/learn/185>



