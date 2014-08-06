---
layout: post
title: "webgl_with_three.js:basicst(three.js基础入门）"
date: 2014-06-30 14:53
comments: true
categories: web
---
先来一段废话：3D graphics in the browser have been a hot topic ever since it was first introduced. But if you were to create your apps using plain WebGL, it would take ages. This is exactly why some really useful libraries have recently came about. Three.js is one of the most popular, and in this series I will show you how best to use it in order to create stunning 3D experiences for your users.

Before we begin, I do expect you to have a basic understanding of 3D space before you start reading this tutorial, as I won't be explaining stuff like coordinates, vectors, etc.

<!--more-->

## step1:准备 ##

准备三文件：index.html,main.js,style.css.下面是代码：

index.html:

    <!DOCTYPE html>
    <html>
    <head>
    <link rel="stylesheet" href="./style.css">
    <script src="./three.js"></script>
    </head>
    <body>
    <script src="./main.js"></script>
    </body>
    </html>

引入样式和脚本，为了好看点，在style.css中添加如下code:

    canvas {
    position: fixed;
    top: 0;
    left: 0;
    }

这样canvas将会位于页面左上角，默认情况下body的margin是8像素。

## step2:场景和渲染器 ##

three.js的物件渲染方式是：将所有物件放到一个显示列表中，然后将它们渲染到屏幕。

这里有一个THREE.Scene的对象，他就是存放所有要显示的物件的地方。一个渲染器同时只能渲染一个场景，但是你可以在不同场景之间来回切换。

渲染器只是将所有的物件从场景中渲染到webgl的canvas。但这货也支持SVG或者2D canvas,但是这里只会将webgl中的canvas。（如果不知道它们之间的区别，可以google或者aol，但不要度娘）。

然后将窗口的宽和高存放在俩变量中，方便后面使用，实在不想定义也可以。

    var width=window.innerWidth;
    var height=window.innerHeight;

接着定义渲染器和场景，别偷懒，这个一定要有。

    //创建webgl渲染器，antialias设为true,使物件边缘光滑一点
    var renderer=new THREE.WebGLRenderer({antialias:true});
    renderer.setSize(width,height);//设置渲染的尺寸
    //在页面中插入webgl canvas这货
    document.body.appendChild(renderer.domElement);
    //创建场景
    var scene=new THREE.Scene;


在倒数第二行，你也可以用你喜欢的Jquery来向页面中插入canvas元素：

    $('body').append(renderer.domElement));

## step3：添加立方体 ##

就像学习任何一门语言一样，一开始通常会写一个hello world！的demo。学习3D引擎，通常会绘制一个立方体。


[http://code.tutsplus.com/tutorials/webgl-with-threejs-basics--net-35688](http://code.tutsplus.com/tutorials/webgl-with-threejs-basics--net-35688)











