---
title: canvas绘图基础(一)
date: 2018-05-10 19:52:55
tags: 随笔
---

# canvas是一个可以使用脚本(通常为JavaScript)在其中绘制图形的 HTML 元素,同时canvas也是定义在浏览器上的画布，canvas不仅仅像p标签等是一个元素，更是一个编辑工具，是一套编程接口，它的出现已超过了Web基于文本的设计初衷。canvas可以设计出绚丽的动画，奇妙的游戏等，可以使我们的网页绚丽多彩！
## 一、canvas绘图环境的搭建
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <canvas id="canvas" style="border: 1px solid #000; display: block; margin:50px auto">
        当前浏览器不支持canvas，请更换浏览器再试！
    </canvas>
    <script type="text/javascript">
        window.onload = function () {
            var canvas = document.getElementById("canvas");

            canvas.width = 800;
            canvas.height = 800;

            var context = canvas.getContext("2d);
        }
    </script>
</body>
</html>
```
### 注意点
1. canvas当不设置尺寸时，默认的canvas宽为300px, 高为150px。
2. canvas是基于状态的绘制环境，不是基于对象的。canvas绘制的所有接口都是由context这个上下文环境所提供的。

<!-- more -->

## 二、绘制直线
context.moveTo(x,y);把路径移动到画布的指定点，不创建线条。
context.lineTo(x,y);添加一个新点，从上一个坐标点画到当前表示的坐标点。
context.lineWidth = 10;设置或者返回当前线条的宽度。
context.strokeStyle = "#058";设置或返回用于笔触的颜色、渐变或模式
context.stroke();绘制已定义的路径。在每一次进行具体的绘制的时候，不是简单的把上一段代码进行绘制，而是检查整个程序中的所有状态，基于这些状态完成一次绘制，也就是说当调用stroke()方法时，会将前面调用的stroke()方法设置的覆盖掉。
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<canvas id="canvas" style="border: 1px solid #000; display: block; margin:50px auto">
    您的浏览器不支持canvas！
</canvas>
<script type="text/javascript">
    window.onload = function () {
        var canvas = document.getElementById("canvas");
        canvas.width = 800;
        canvas.height = 800;
        var context = canvas.getContext("2d");
        //canvas是基于状态的绘制环境
        context.moveTo(100,100);
        context.lineTo(700,700); 
        context.lineWidth = 10;  
        context.strokeStyle = "#058";
        context.stroke();  
    }
</script>
</body>
</html>
```
## 三、多边形的封闭与填充
context.beginPath();声明从现在开始要进行一段全新的绘制，或者重置当前路径。
context.closePath();创建从当前点回到起始点的路径，使图形封闭。
context.fillStyle;设置或返回用于填充绘画的颜色，渐变或模式。
context.strokeStyle;设置或返回用于笔触的颜色，渐变或模式。
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<canvas id="canvas" style="border: 1px solid #000; display: block; margin:50px auto">
</canvas>
<script type="text/javascript">
    window.onload = function () {
        var canvas = document.getElementById("canvas");
        canvas.width = 800;
        canvas.height = 800;
        var context = canvas.getContext("2d");
        //封闭多边形的绘制与填充(箭头）
        context.beginPath();
        context.moveTo(100,350);
        context.lineTo(500,350);
        context.lineTo(500,200);
        context.lineTo(700,400);
        context.lineTo(500,600);
        context.lineTo(500,450);
        context.lineTo(100,450);
        context.closePath();
        context.lineWidth = 10;
        context.fillStyle = "red";
        context.strokeStyle = "#058";
        context.fill();  //以fillStyle定义的颜色进行填充
        context.stroke(); //以strokeStyle定义的颜色进行填充
    }
</script>
</body>
</html>
```
## 四、绘制矩形
context.rect(x,y,width,height);矩形路劲自动遍历。
context.fillRect(x,y,width,height);绘制带填充色的矩形，使用的是fillStyle的样式。
context.strokeRect(x,y,width,height);绘制带边框色的矩形，使用的是strokeStyle的样式。
上面提供的方法之中每一个都包含了相同的参数。x与y指定了在canvas画布上所绘制的矩形的左上角（相对于原点）的坐标。width和height设置矩形的尺寸。
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
    window.onload = function () {
        var canvas = document.getElementById("canvas");
        canvas.width = 800;
        canvas.height = 800;
        var context = canvas.getContext("2d");
        //矩形的覆盖和透明色
        drawRect(context,100,100,400,400,10,"#058","red");
 drawRect2(context,300,300,400,400,10,"#058","rgba(0,256,0,0.5");//不透明色的值为0.5
    };
    function drawRect(cxt,x,y,width,height,borderWidth,borderColor,fillColor) {
        cxt.beginPath();
        cxt.rect(x,y,width,height);
        cxt.closePath();
        cxt.lineWidth = borderWidth;
        cxt.fillStyle = fillColor;
        cxt.strokeStyle = borderColor;
        cxt.fill();
        cxt.stroke();
    }
    function drawRect2(cxt,x,y,width,height,borderWidth,borderColor,fillColor) {
        cxt.lineWidth = borderWidth;
        cxt.fillStyle = fillColor;
        cxt.strokeStyle = borderColor;
        cxt.fillRect(x,y,width,height);
        cxt.strokeRect(x,y,width,height);
    }
</script>
</body>
</html>
```
## 五、线条的属性
lineCap用于设置线条两端的形状，包含三个值：butt（默认值）、round（圆头）、square（方头），后面两个都会多出一个像素。

lineJoin用于设置线条与线条相交时呈现的状态。包含三个值：miter（默认值以尖角的形式呈现）、bevel（斜接的形式，就像用长的纸带折叠下来的形成的）、round（圆形光滑的过渡）。

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<canvas id="canvas" style="border: 1px solid #000; display: block; margin:50px auto">

</canvas>
<script type="text/javascript">
    window.onload = function () {
        //lineCap用于设置线条两端的形状。
        // 包含三个值：butt（默认值）、round（圆头）、square（方头）。
        //后面两个都会多出一个像素
        var canvas = document.getElementById("canvas");
        canvas.width = 800;
        canvas.height = 800;
        var context = canvas.getContext("2d");
        //开始绘制
        context.lineWidth = 10;
        context.strokeStyle = "#058";
        context.beginPath();
        context.moveTo(100,200);
        context.lineTo(700,200);
        context.lineCap = "butt";
        context.stroke();
        context.beginPath();
        context.moveTo(100,400);
        context.lineTo(700,400);
        context.lineCap = "round";
        context.stroke();
        context.beginPath();
        context.moveTo(100,600);
        context.lineTo(700,600);
        context.lineCap = "square";
        context.stroke();
        context.lineWidth = 1;
        context.strokeStyle = "#27a";
        context.moveTo(100,100);
        context.lineTo(100,700);
        context.moveTo(700,100);
        context.lineTo(700,700);
        context.stroke();
    }
</script>
</body>
</html>
```
## 六、绘制五角星
思路：将五角星的中心放在同心圆上进行绘制，分析如下图：

![draw a star](http://img.blog.csdn.net/20171124125347391?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2VpeGluXzM3OTcyNzIz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<canvas id="canvas" style="border: 1px solid #000; display:block; margin:50px auto">

</canvas>
<script type="text/javascript">
    window.onload = function () {
        var canvas = document.getElementById("canvas");
        canvas.width = 800;
        canvas.height = 800;
        var context = canvas.getContext("2d");
        //用变量的形式绘制五角星（函数化）
        context.lineWidth = 10;
        context.lineJoin = "miter";
        drawStar(context,150,300,400,400,0);
    };
    
    function drawStar(cxt,r,R,x,y,rot) {
        cxt.beginPath();
        for(var i=0; i<5; i++) {
            cxt.lineTo(Math.cos((18+i*72-rot)/180*Math.PI)*R+x,
                -Math.sin((18+i*72-rot)/180*Math.PI)*R+y);
            cxt.lineTo(Math.cos((54+i*72-rot)/180*Math.PI)*r+x,
                -Math.sin((54+i*72-rot)/180*Math.PI)*r+y);
        }
        cxt.closePath();
        cxt.stroke();
    }
</script>
</body>
</html>
```
### 本教程主要是根据慕课网刘宇波老师的canvas绘图详解视频教程总结而来。在此特别感谢刘老师的无私奉献。
### 课程链接
<https://www.imooc.com/learn/185>