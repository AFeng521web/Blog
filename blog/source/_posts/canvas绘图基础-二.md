---
title: canvas绘图基础(二)
date: 2018-05-10 20:00:21
tags: 随笔
---

## 一、星空绘制
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
        context.fillStyle = "black";
        context.fillRect(0,0,canvas.width,canvas.height);
        //绘制多个不同的星星
        for(var i=0; i<200; i++) {
            var R = Math.random()*10+10;
            var x = Math.random()*canvas.width;
            var y = Math.random()*canvas.height;
            var a = Math.random()*360;
            drawStar(context,R/2,R,x,y,a);
        }
    };
    //星空绘制
    function drawStar(cxt,r,R,x,y,rot) {
        cxt.beginPath();
        for(var i=0; i<5; i++) {
            cxt.lineTo(Math.cos((18+i*72-rot)/180*Math.PI)*R+x,
                -Math.sin((18+i*72-rot)/180*Math.PI)*R+y);
            cxt.lineTo(Math.cos((54+i*72-rot)/180*Math.PI)*r+x,
                -Math.sin((54+i*72-rot)/180*Math.PI)*r+y);
        }
        cxt.closePath();
        cxt.fillStyle = "#fb3";
        cxt.strokeStyle = "#fd5";
        cxt.lineWidth = 3;
        cxt.lineJoin = "round";
        cxt.fill();
        cxt.stroke();
    }
</script>
</body>
</html>
```
<!-- more -->

## 二、图形变换
图形变换是图形学的一个重要领域，在图形学中任何图形的绘制，是先绘制它的基本轮廓，之后再用图形变换的形式将其绘制成所需的大小。
translate(x,y);
translate 方法接受两个参数,x 是左右偏移量，y 是上下偏移量。它用来移动 canvas 和它的原点到一个不同的位置。
rotate(angle);
这个方法只接受一个参数：旋转的角度(angle)，它是顺时针方向的，以弧度为单位的值。它用于以原点为中心旋转 canvas。
scale(x, y)
scale 方法接受两个参数。x,y 分别是横轴和纵轴的缩放因子，它们都必须是正值。值比 1.0 小表示缩小，比 1.0 大则表示放大，值为 1.0 时什么效果都没有。
用它来增减图形在 canvas 中的像素数目，对形状，位图进行缩小或者放大。
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<canvas id="canvas" style="border: 1px solid #000; display: block; margin: 50px auto">
    您的浏览器不支持canvas!
</canvas>
<script type="text/javascript">
    var canvas = document.getElementById("canvas");
    canvas.width = 800;
    canvas.height = 800;
    var context = canvas.getContext("2d");
    
    //问题；会出现translate的叠加
    context.fillStyle = "red";
    context.translate(100,100);
    context.fillRect(0,0,400,400);
    context.fillStyle = "green";
    context.translate(300,300);
    context.fillRect(0,0,400,400);

    //解决方式1
    context.fillStyle = "red";
    context.translate(100,100);
    context.fillRect(0,0,400,400);
    context.translate(-100,-100); //在加的基础上减回去
    context.fillStyle = "green";
    context.translate(300,300);
    context.fillRect(0,0,400,400);

    //解决办法2
    context.save();
    context.fillStyle = "red";
    context.translate(100,100);
    context.fillRect(0,0,400,400);
    context.restore();
    context.save();
    context.fillStyle = "green";
    context.translate(300,300);
    context.fillRect(0,0,400,400);
    context.restore();
</script>
</body>
</html>
```
### 注意点
在canvas中进行图形变换时，会产生叠加。用context.save()解决。context.save()是一种保存当前图像所有状态的方法。

用scale(x,y)进行缩放时，会将图形的大小、边框以及位置都进行缩放，
同样用context.save()解决。

context.restore()返回在save这个时刻context的所有状态

## 三、变换矩阵
![这里写图片描述](http://img.blog.csdn.net/20171124134132198?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2VpeGluXzM3OTcyNzIz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 设置变换矩阵：transform(a,b,c,d,e,f);
transform有级联效果，就是设置的transform( )会叠加，可以通过setTransform( )结束所有的效果，开始一个全新的transform()。
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
        //设置变换矩阵：transform(a,b,c,d,e,f)
        var canvas = document.getElementById("canvas");
        canvas.width = 800;
        canvas.height = 800;
        var context = canvas.getContext("2d");

        context.fillStyle = "red";
        context.strokeStyle = "#058";
        context.lineWidth = "5";

        context.save();
        context.transform(1,0,0,1,0,0);
        context.transform(2,0.2,0.1,1.5,50,100);
        context.fillRect(50,50,300,300);
        context.strokeRect(50,50,300,300);
        context.restore();
    }
</script>
</body>
</html>
```
## 四、线性渐变
线性渐变（定义在两点之间）
分两步进行设置：
第一步：确定渐变方向
var grd = context.context.createLinearGradient(xstart,ystart,xend,yend);
第二步：设置关键颜色位置和渐变色
grd.addColorStop(stop,color);
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
        //两个颜色的渐变
        var linearGrad = context.createLinearGradient(0,0,800,800);
        //渐变方向
        linearGrad.addColorStop(0.0,"#fff");
        linearGrad.addColorStop(1.0,"#000");
        context.fillStyle = linearGrad;
        context.fillRect(0,0,800,800);

        //多个颜色渐变
        var linearGrad = context.createLinearGradient(0,0,800,0);//水平渐变方向
        linearGrad.addColorStop(0.0,"white");
        linearGrad.addColorStop(0.25,"yellow");
        linearGrad.addColorStop(0.5,"green");
        linearGrad.addColorStop(0.75,"blue");
        linearGrad.addColorStop(1.0,"black");
        context.fillStyle = linearGrad;
        context.fillRect(0,0,800,800);
    }
</script>
</body>
</html>
```
##五、径向渐变
径向渐变（放射状渐变，定义在两个同心圆上）分两步进行设置：
第一步：确定渐变的圆心，半径
var grd=context.createRadialGradient(x0,y0,r0,x1,y1,r1);

第二步：设置关键颜色位置和渐变色
grd.addColorStop(stop,color);
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
        
        var radiaGrad = context.createRadialGradient(400,400,0,400,400,500);
        radiaGrad.addColorStop(0,"white");
        radiaGrad.addColorStop(0.25,"yellow");
        radiaGrad.addColorStop(0.5,"green");
        radiaGrad.addColorStop(0.75,"blue");
        radiaGrad.addColorStop(1.0,"black");

        context.fillStyle = radiaGrad;
        context.fillRect(0,0,800,800);
    }
</script>
</body>
```
## 六、createPattern()
createPattern() 方法在指定的方向内重复指定的元素。元素可以是图片、视频，或者其他 canvas元素。被重复的元素可用于绘制/填充矩形、圆形或线条等等。

语法：context.createPattern(image,"repeat|repeat-x|repeat-y|no-repeat");

使用图片填充：context.createPattern(image,repeat-style);
使用画布填充：context.createPattern（canvas,repeat-style);
使用视频填充：context.createPattern（video,repeat-style);
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<canvas id="canvas" style="border: 1px solid #000; display: block; margin: 50px auto">
    您的浏览器不支持cnavas画布！
</canvas>
<script type="text/javascript">
    window.onload = function () {
        var canvas = document.getElementById("canvas");
        canvas.width = 800;
        canvas.height = 800;
        var context = canvas.getContext("2d");

        var backgroundImage = new Image();
        backgroundImage.scr = "img/img.jpg";
        backgroundImage.onload = function () {
            var pattern = context.createPattern(backgroundImage,"repeat");
            context.fillStyle = "pattern";
            context.fillRect(0,0,800,800);
        }
    }

</script>
</body>
</html>
```
### 本教程主要是根据慕课网刘宇波老师的canvas绘图详解视频教程总结而来。在此特别感谢刘老师的无私奉献。

### 课程链接

<https://www.imooc.com/learn/185>