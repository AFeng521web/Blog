---
title: canvas绘制基础(三)
date: 2018-05-10 20:16:09
tags: 随笔
---

## 一、曲线的绘制
context.arc(centerx,centery,radius,startingAngle,endingAngle,anticlockwise=false);
centerx, centery,代表绘制的中心点坐标，radius代表曲线半径，
startingAngle, endingAngle,分别代表绘制的起始位置和结束位置，
anticlockwise=false代表按顺时针绘制（不设置时默认为顺时针绘制）

<!-- more -->

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title></title>
</head>
<body>
<canvas id="canvas" style="border: 1px solid #000; display: block; margin: 50px auto">
    您的浏览器不支持canvas画布！
</canvas>
<script type="text/javascript">
     window.onload = function () {
         var canvas = document.getElementById("canvas");
         canvas.width = 1024;
         canvas.height = 768;
         var context = canvas.getContext("2d");

         //绘制多段弧线
         context.lineWidth = 5;
         context.strokeStyle = "#058";
         for(var i=0; i<10; i++) {
             context.beginPath();
             context.arc(50+i*100,60,40,0,2*Math.PI*(i+1)/10);
             context.closePath();
             context.stroke();
         }
         for(var j=0; j<10; j++) {
             context.beginPath();
             context.arc(50+j*100,150,40,0,2*Math.PI*(j+1)/10);

             context.stroke();
         }
         for(var i=0; i<10; i++) {
             context.beginPath();
             context.arc(50+i*100,300,40,0,2*Math.PI*(i+1)/10,true);
             context.closePath();
             context.stroke();
         }
         for(var j=0; j<10; j++) {
             context.beginPath();
             context.arc(50+j*100,420,40,0,2*Math.PI*(j+1)/10,true);

             context.stroke();
         }
         //填充
         context.fillStyle = "#058";
         for(var i=0; i<10; i++) {
             context.beginPath();
             context.arc(50+i*100,540,40,0,2*Math.PI*(i+1)/10,true);
             context.closePath();
             context.stroke();
             context.fill();
         }
     }
</script>
</body>
</html>
```
## 二、绘制圆角矩形
![这里写图片描述](http://img.blog.csdn.net/20171124143855698?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2VpeGluXzM3OTcyNzIz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
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
        canvas.width = 800;
        canvas.height = 800;
        var context = canvas.getContext("2d");

        drawRoundRect(context,100,100,600,500,50);
        //绘制（x,y)代表左上角的位置 width,height代表矩形的宽高
    };
    
    function drawRoundRect(cxt,x,y,width,height,radius) {
        cxt.save();
        cxt.translate(x,y);
        pathRoundRect(cxt,width,height,radius);
        cxt.strokeStyle = "black";
        cxt.stroke();
        context.restore();
    }
    //路径部分
    function pathRoundRect(cxt,width,height,radius) {
        cxt.beginPath();
        cxt.arc(width-radius,height-radius,radius,0,Math.PI/2);
        cxt.lineTo(radius,height);
        cxt.arc(radius,height-radius,radius,Math.PI/2,Math.PI);
        cxt.lineTo(0,radius);
        cxt.arc(radius,radius,radius,Math.PI,Math.PI*3/2);
        cxt.lineTo(width-radius,0);
        cxt.arc(width-radius,radius,radius,Math.PI*3/2,Math.PI*2);
        cxt.closePath();
    }
</script>
</body>
</html>
```
## 三、绘制圆弧
context.moveTo(x0,y0);
context.arcTo(x1,y1,x2,y2,radius);
其中(x0,y0)为起始点并非是圆弧的开始的位置，(x1,y1)为控制点，
(x2,y2)为终止点，并非是圆弧结束的位置。也就是说圆弧的起点在以(x0,y0)(x1,y1)的直线上一点处，距离控制点radius。

![这里写图片描述](http://img.blog.csdn.net/20171124144247209?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2VpeGluXzM3OTcyNzIz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
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
        
        //arcTo();
        context.beginPath();
        context.moveTo(150,150);
        context.arcTo(650,150,650,650,300);
        context.lineWidth = 5;
        context.strokeStyle = "red";
        context.stroke();

        context.beginPath();
        context.moveTo(150,150);
        context.lineTo(650,150);
        context.lineTo(650,650);
        context.closePath();
        context.lineWidth = 2;
        context.strokeStyle = "gray";
        context.stroke();
    }
</script>
</body>
</html>
```
## 四、绘制弯月
![这里写图片描述](http://img.blog.csdn.net/20171124144933682?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2VpeGluXzM3OTcyNzIz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

由上图可知： R=(AC*AH)/HC；
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

        //开始绘制
        context.arc(400,400,300,0.5*Math.PI,1.5*Math.PI,true);
        context.moveTo(400,100);
        context.arcTo(1200,400,400,700,(400-100)*dis(400,100,1200,400)/(1200-400));
        context.stroke();
    };
    function dis(x1,y1,x2,y2) {
        return Math.sqrt((x1-x2)*(x1-x2)+(y2-y1)*(y2-y1));
    }
</script>
</body>
</html>
```
## 五、贝塞尔曲线
### 5.1、二次贝塞尔曲线
context.moveTo(x0,y0);
context.quadraticCurveTo(x1,y1,x2,y2);
其中(x0,y0)为曲线的起点，(x1,y1)为控制点，(x2,y2)为曲线的终点。
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
        var cancas = document.getElementById("canvas");
        canvas.width = 800;
        canvas.height = 800;
        var context = canvas.getContext("2d");

        context.lineStyle = 6;
        context.strokeStyle = "#333";
        context.beginPath();
        context.moveTo(100,250);
        context.quadraticCurveTo(250,100,400,250);
        context.stroke();

    }
</script>
</body>
</html>
```
交互链接
<http://www.j--d.com/bezier>


### 5.2、三次贝塞尔曲线
context.moveTo(x0,y0);
context.bezierCurveTo(x1,y1,x2,y2,x3,y3);
其中(x0,y0)为曲线的起点，(x1,y1),(x2,y2)为控制点，(x3,y3)为曲线的终点。

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<canvas id="canvas" style="border: 1px solid #000; display: block; margin:50px auto">
    您的浏览不支持canvas画布！
</canvas>
<script type="text/javascript">
    window.onload = function () {
        var canvas = document.getElementById("canvas");
        canvas.width = 800;
        canvas.height = 800;
        var context = canvas.getContext("2d");

        context.lineWidth = 6;
        context.strokeStyle = "#333";
        context.beginPath();
        context.moveTo(212,275);
        context.bezierCurveTo(150,100,350,100,276,282);
        context.stroke();
    }
</script>
</body>
</html>
```
交互链接
<http://www.j--d.com/bezier>

## 六、星空
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<canvas id="canvas" style="border: 1px solid #000; display: block; margin: 50px auto;">
    您的浏览器不支持canvas画布！
</canvas>
<script type="text/javascript">
    window.onload=function() {
        var canvas = document.getElementById('canvas');

        canvas.width = 1024;
        canvas.height = 800;

        var context = canvas.getContext('2d');
        var skyStyle = context.createRadialGradient(canvas.width/2,canvas.height,0,
            canvas.width/2,canvas.height,canvas.height);
        skyStyle.addColorStop(0.0,"#035");
        skyStyle.addColorStop(1.0,"black");
        context.fillStyle = skyStyle;
        context.fillRect(0,0,canvas.width,canvas.height);

        for(var i=0; i<200; i++) {
            var R = Math.random()*5+5;
            var x = Math.random()*canvas.width;
            var y = Math.random()*canvas.height*0.65;
            var a = Math.random()*360;
            drawStar(context,R,x,y,a);
        }
        fillMoon(context,2,900,200,50,30);
        drawLand(context);

    };

    //绘制星星
    function drawStar(cxt,R,x,y,rot){
        cxt.save();
        cxt.translate(x,y);
        cxt.rotate(rot/180*Math.PI);
        cxt.scale(R,R);
        starPath(cxt);//函数的作用是绘制标准的五角星的路径

        cxt.fillStyle = "#fb3";
        cxt.fill();
        cxt.restore();
    }

    function starPath(cxt) {
        cxt.beginPath();
        for(var i=0; i<5; i++) {
            cxt.lineTo(Math.cos((18+i*72)/180*Math.PI),-Math.sin((18+i*72)/180*Math.PI));
            cxt.lineTo(Math.cos((54+i*72)/180*Math.PI)*0.5,-Math.sin((54+i*72)/180*Math.PI)*0.5);
        }
        cxt.closePath();
    }

    //绘制月亮
    //d为控制点横坐标的值  x,y表示弯月的位置 R表示弯月的半径
    function fillMoon(cxt,d,x,y,R,rot,fillColor) {
        cxt.save();
        cxt.translate(x,y);
        cxt.rotate(rot*Math.PI/180);
        cxt.scale(R,R);
        pathMoon(cxt,d);//绘制月亮的轮廓
        cxt.fillStyle = fillColor || "#fb3";
        cxt.fill();
        cxt.restore();
    }
    
    function pathMoon(cxt,d) {
        cxt.beginPath();
        cxt.arc(0,0,1,0.5*Math.PI,1.5*Math.PI,true);
        cxt.moveTo(0,-1);
        cxt.arcTo(d,0,0,1,dis(0,-1,d,0)/d);
        cxt.closePath();
    }
    
    function dis(x1,y1,x2,y2) {
        return Math.sqrt((x1-x2)*(x1-x2)+(y2-y1)*(y2-y1));

    }
    
    function drawLand(cxt) {
        cxt.save();

        cxt.beginPath();
        cxt.moveTo(0,600);
        cxt.bezierCurveTo(540,400,660,800,1200,600);
        cxt.lineTo(1200,800);
        cxt.lineTo(0,800);
        cxt.closePath();

        var landStyle = cxt.createLinearGradient(0,800,0,0);
        landStyle.addColorStop(0.0,"#030");
        landStyle.addColorStop(1.0,"#580");
        cxt.fillStyle = landStyle;
        cxt.fill();
        cxt.restore();
    }
</script>
</body>
</html>
```
### 效果图
![这里写图片描述](http://img.blog.csdn.net/20171124150733234?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2VpeGluXzM3OTcyNzIz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 本教程主要是根据慕课网刘宇波老师的canvas绘图详解视频教程总结而来。在此特别感谢刘老师的无私奉献。

### 课程链接

<https://www.imooc.com/learn/185>