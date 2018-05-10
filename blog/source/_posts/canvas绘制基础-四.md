---
title: canvas绘制基础(四)
date: 2018-05-10 20:17:09
tags: 随笔
---

##一、文字渲染
文字是页面制作必不可少的一部分，漂亮的文字会使网页赏心悦目，关于文字，我们可以设置它的字型、大小、填充、渐变色等等。
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

        //开始编码
        context.font = "bold 40px Arial";
        context.fillStyle = "#058";
        context.fillText("做西邮最优秀的男人！",40,100);

        context.lineWidth = 1;
        context.strokeStyle = "#058";
        context.strokeText("优秀",40,200);

        //可选参数用于控制字符串的长度
        context.fillText("做西邮最优秀的男人,赢取白富美",40,300,200);
        context.strokeText("优秀",40,400,100);

        //渐变字体
        var linearGrad = context.createLinearGradient(0,0,800,0);
        linearGrad.addColorStop(0.0,"red");
        linearGrad.addColorStop(0.25,"yellow");
        linearGrad.addColorStop(0.5,"green");
        linearGrad.addColorStop(0.75,"blue");
        linearGrad.addColorStop(1.0,"black");
        context.fillStyle = linearGrad;
        context.fillText("莫要心急，莫要慌张，每天都在进步",40,500);

        //利用图片填充字体
        var backgroundImage = new Image();
        backgroundImage.src = "img/img.jpg";
        backgroundImage.onload = function () {
            var pattern = context.createPattern(backgroundImage,"repeat");
            context.font = "bold 80px Arial";
            context.fillText("canvas",40,650);
            context.strokeText("canvas",40,750);
        }
    }
</script>
</body>
</html>
```

<!-- more -->

## 二、font：字型、字号和字体
context.font="bold 80px Arial";//设置字体的样式、大小、字型
context.fillText(String,x,y);//设置所填充的文字以及位置
context.strokeText(String,x,y);//绘制一行文字，这行文字只有外边框

context.fillText(String,x,y,[maxlen]);
context.strokeText(String,x,y,[maxlen]);
[maxlen]为可选参，描述所写文字的最长宽度为多少。

font-style的属性值：normal（默认值）、italic（斜体字）、oblique（倾斜字体）；
font-variant的属性值：normal（默认值）、small-caps（小写字母变为小的大写字母）；
font-weight的属性值：lighter（大多数浏览器不支持）、normal（默认值）、bold、bolder（大多数浏览器不支持）；
font-size的属性值：20px（默认值）、2em、150%、xx-small、x-small、medium、large、x-large、xx-large；
font-family:设置多种字体备选（各个字体之间用逗号隔开）；支持@font-face。

## 三、文本对齐
### 3.1、描述文本的横向对齐方式(基准是给定的文本的初始坐标值）
context.textAlign="left";
context.textAlign="right";
context.textAlign="center";
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
        //1.描述文本的横向对齐方式（基准是给定文本的初始坐标值）

        //context.textAlign="left";
        //context.textAlign="right";
        //context.textAlign="center";

        //文本水平对齐
        context.fillStyle = "#058";
        context.font = "bold 20px sans-serif";

        context.textAlign = "left";
        context.fillText("textAlign=left", 400, 100);

        context.textAlign = "center";
        context.fillText("textAlign=center", 400, 200);

        context.textAlign = "right";
        context.fillText("textAlign=right", 400, 300);
        //辅助线
        context.strokeStyle = "#888";
        context.moveTo(400, 0);
        context.lineTo(400, 400);
        context.stroke();
    }
</script>
</body>
</html>
```
![这里写图片描述](http://img.blog.csdn.net/20171124170407771?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2VpeGluXzM3OTcyNzIz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 3.2描述文本的垂直对齐方式
context.textBaseline="top";
context.textBaseline="middle";
context.textBaseline="bottom";
context.textBaseline="alphabetic";//（默认值）基于拉丁字符的语言。context.textBaseline="ideographic";//基于汉字及日本文字来设置的垂直方向的基准线。
context.textBaseline="hanging";//基于印度文的字符串来设计的垂直方向的基准线。
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
        
        context.fillStyle="#058";
        context.font="bold 20px sans-serif";

        context.textBaseline="top";
        context.fillText("自古深情留不住",40,100);
        drawBaseline(context,100);

        context.textBaseline="middle";
        context.fillText("自古深情留不住！",40,200);
        drawBaseline(context,200);

        context.textBaseline="bottom";
        context.fillText("自古深情留不住！",40,300);
        drawBaseline(context,300);

        function drawBaseline(cxt,b){
            var width=cxt.canvas.width;
            cxt.save();
            cxt.strokeStyle="#888";
            cxt.lineWidth=2;
            cxt.moveTo(0,b);
            cxt.lineTo(width,b);
            cxt.stroke();
            cxt.restore();
        }
    }
</script>
</body>
</html>
```
## 四、文本度量
context.measureText(string).width传入字符串将返回一个对象，这个对象拥有一个.width的属性，这个属性将返回传入字符串在canvas中渲染时渲染出的字符串的宽度
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
        context.fillStyle = "#058";
        context.font = "bold 40px sans-serif";
        context.fillText("西邮创客空间优秀的人真多！",40,100);
        var textWidth = context.measureText("西邮创客空间优秀的人真多").width;
        context.fillText("以上字符的宽度为:"+textWidth+"px",40,200);
    }
</script>
</body>
</html>
```


