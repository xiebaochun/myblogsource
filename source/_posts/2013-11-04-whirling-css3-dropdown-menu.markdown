---
layout: post
title: "制作旋转CSS3下拉菜单（whirling CSS3 dropdown menu）"
date: 2013-11-04 14:39
comments: true
categories: web
---
![](http://www.script-tutorials.com/demos/364/thumb.png)


原文地址：[http://www.script-tutorials.com/whirling-dropdown-menu/](http://www.script-tutorials.com/whirling-dropdown-menu/)

译者：[微个日光日](http://xiebaochun.github.io/)

<!--more-->

很多下拉菜单都可以通过css3的选择器轻松实现。只要稍微激发一下你的创作灵感，你就可以做出一个真实而独特的设计。在今天的教程中，我们将创建一个特别的css3下拉菜单，当鼠标移动到父级菜单项时将会呈现新的下拉特效，子菜单项将会以旋转的方式出现。只要一步一步地跟着本教程就能创建这个菜单。

### 预览： ###

![](http://www.script-tutorials.com/demos/364/menu.png)

[在线演示](http://www.script-tutorials.com/demos/364/index.html)

[下载DEMO](http://www.script-tutorials.com/demos/364/source.zip)

### 步骤 1.HTML ###

首先需要为css菜单建一个HTML结构。和大部分下拉菜单类似，这是一个UL-LI结构的下拉菜单。下面是此菜单的HTML代码。

index.html

    <ul class="menu">
    <li><a href="#">Home</a></li>
    <li><a href="#s1">Menu 1</a>
        <ul class="submenu">
            <li><a href="#">Submenu a</a></li>
            <li><a href="#">Submenu b</a></li>
            <li><a href="#">Submenu c</a></li>
            <li><a href="#">Submenu d</a></li>
            <li><a href="#">Submenu e</a></li>
            <li><a href="#">Submenu f</a></li>
            <li><a href="#">Submenu g</a></li>
            <li><a href="#">Submenu h</a></li>
        </ul>
    </li>
    <li class="active"><a href="#s2">Menu 2</a>
        <ul class="submenu">
            <li><a href="#">Submenu a</a></li>
            <li><a href="#">Submenu b</a></li>
            <li><a href="#">Submenu c</a></li>
            <li><a href="#">Submenu d</a></li>
            <li><a href="#">Submenu e</a></li>
            <li><a href="#">Submenu f</a></li>
            <li><a href="#">Submenu g</a></li>
            <li><a href="#">Submenu h</a></li>
        </ul>
    </li>
    <li><a href="#">Menu 3</a>
        <ul class="submenu">
            <li><a href="#">Submenu a</a></li>
            <li><a href="#">Submenu b</a></li>
            <li><a href="#">Submenu c</a></li>
            <li><a href="#">Submenu d</a></li>
            <li><a href="#">Submenu e</a></li>
            <li><a href="#">Submenu f</a></li>
            <li><a href="#">Submenu g</a></li>
            <li><a href="#">Submenu h</a></li>
        </ul>
    </li>
    <li><a href="#">Menu 4</a></li>
    <li><a href="http://www.script-tutorials.com/whirling-dropdown-menu/">Back to tutorial</a></li>
    </ul>

### 步骤 2.CSS ###

css/main.css

    .menu, .menu ul {
    list-style: none;
    padding: 0;
    margin: 0;
    }
    .menu {
    height: 58px;
    }
    .menu li {
    background: -moz-linear-gradient(#292929, #252525);
    background: -ms-linear-gradient(#292929, #252525);
    background: -webkit-gradient(linear, left top, left bottom, color-stop(0%, #292929), color-stop(100%, #252525));
    background: -webkit-linear-gradient(#292929, #252525);
    background: -o-linear-gradient(#292929, #252525);
    filter: progid:DXImageTransform.Microsoft.gradient(startColorstr='#292929', endColorstr='#252525');
    -ms-filter: "progid:DXImageTransform.Microsoft.gradient(startColorstr='#292929', endColorstr='#252525')";
    background: linear-gradient(#292929, #252525);

    border-bottom: 2px solid #181818;
    border-top: 2px solid #303030;
    min-width: 160px;
    }
    .menu > li {
    display: block;
    float: left;
    position: relative;
    }
    .menu > li:first-child {
    border-radius: 5px 0 0;
    }
    .menu a {
    border-left: 3px solid rgba(0, 0, 0, 0);
    color: #808080;
    display: block;
    font-family: 'Lucida Console';
    font-size: 18px;
    line-height: 54px;
    padding: 0 25px;
    text-decoration: none;
    text-transform: uppercase;
    }

当鼠标移动到某个菜单项上时，将其高亮。

    .menu li:hover {
    background-color: #1c1c1c;
    background: -moz-linear-gradient(#1c1c1c, #1b1b1b);
    background: -ms-linear-gradient(#1c1c1c, #1b1b1b);
    background: -webkit-gradient(linear, left top, left bottom, color-stop(0%, #1c1c1c), color-stop(100%, #1b1b1b));
    background: -webkit-linear-gradient(#1c1c1c, #1b1b1b);
    background: -o-linear-gradient(#1c1c1c, #1b1b1b);
    filter: progid:DXImageTransform.Microsoft.gradient(startColorstr='#1c1c1c', endColorstr='#1b1b1b');
    -ms-filter: "progid:DXImageTransform.Microsoft.gradient(startColorstr='#1c1c1c', endColorstr='#1b1b1b')";
    background: linear-gradient(#1c1c1c, #1b1b1b);

    border-bottom: 2px solid #222222;
    border-top: 2px solid #1B1B1B;
    }
    .menu li:hover > a {
    border-radius: 5px 0 0 0;
    border-left: 3px solid #C4302B;
    color: #C4302B;
    }

子菜单项默认是隐藏的，并且它的元素被选择了90度。当鼠标悬停在父级菜单项时，子菜单将显示它的所有菜单项。

    /* submenu styles */
    .submenu {
    left: 0;
    max-height: 0;
    position: absolute;
    top: 100%;
    z-index: 0;
    
    -webkit-perspective: 400px;
    -moz-perspective: 400px;
    -ms-perspective: 400px;
    -o-perspective: 400px;
    perspective: 400px;
    }
    .submenu li {
    opacity: 0;
    
    -webkit-transform: rotateY(90deg);
    -moz-transform: rotateY(90deg);
    -ms-transform: rotateY(90deg);
    -o-transform: rotateY(90deg);
    transform: rotateY(90deg);
    
    -webkit-transition: opacity .4s, -webkit-transform .5s;
    -moz-transition: opacity .4s, -moz-transform .5s;
    -ms-transition: opacity .4s, -ms-transform .5s;
    -o-transition: opacity .4s, -o-transform .5s;
    transition: opacity .4s, transform .5s;
    }
    .menu .submenu li:hover a {
    border-left: 3px solid #454545;
    border-radius: 0;
    color: #ffffff;
    }
    .menu > li:hover .submenu, .menu > li:focus .submenu {
    max-height: 2000px;
    z-index: 10;
    }
    .menu > li:hover .submenu li, .menu > li:focus .submenu li {
    opacity: 1;
    
    -webkit-transform: none;
    -moz-transform: none;
    -ms-transform: none;
    -o-transform: none;
    transform: none;
    }

最后，为了使其更特别一点，给每个子菜单项添加一些延迟，这样他们就会依次显示出来。

    /* CSS3 delays for transition effects */
    .menu li:hover .submenu li:nth-child(1) {
    -webkit-transition-delay: 0s;
    -moz-transition-delay: 0s;
    -ms-transition-delay: 0s;
    -o-transition-delay: 0s;
    transition-delay: 0s;
    }
    .menu li:hover .submenu li:nth-child(2) {
    -webkit-transition-delay: 50ms;
    -moz-transition-delay: 50ms;
    -ms-transition-delay: 50ms;
    -o-transition-delay: 50ms;
    transition-delay: 50ms;
    }
    .menu li:hover .submenu li:nth-child(3) {
    -webkit-transition-delay: 100ms;
    -moz-transition-delay: 100ms;
    -ms-transition-delay: 100ms;
    -o-transition-delay: 100ms;
    transition-delay: 100ms;
    }
    .menu li:hover .submenu li:nth-child(4) {
    -webkit-transition-delay: 150ms;
    -moz-transition-delay: 150ms;
    -ms-transition-delay: 150ms;
    -o-transition-delay: 150ms;
    transition-delay: 150ms;
    }
    .menu li:hover .submenu li:nth-child(5) {
    -webkit-transition-delay: 200ms;
    -moz-transition-delay: 200ms;
    -ms-transition-delay: 200ms;
    -o-transition-delay: 200ms;
    transition-delay: 200ms;
    }
    .menu li:hover .submenu li:nth-child(6) {
    -webkit-transition-delay: 250ms;
    -moz-transition-delay: 250ms;
    -ms-transition-delay: 250ms;
    -o-transition-delay: 250ms;
    transition-delay: 250ms;
    }
    .menu li:hover .submenu li:nth-child(7) {
    -webkit-transition-delay: 300ms;
    -moz-transition-delay: 300ms;
    -ms-transition-delay: 300ms;
    -o-transition-delay: 300ms;
    transition-delay: 300ms;
    }
    .menu li:hover .submenu li:nth-child(8) {
    -webkit-transition-delay: 350ms;
    -moz-transition-delay: 350ms;
    -ms-transition-delay: 350ms;
    -o-transition-delay: 350ms;
    transition-delay: 350ms;
    }
    
    .submenu li:nth-child(1) {
    -webkit-transition-delay: 350ms;
    -moz-transition-delay: 350ms;
    -ms-transition-delay: 350ms;
    -o-transition-delay: 350ms;
    transition-delay: 350ms;
    }
    .submenu li:nth-child(2) {
    -webkit-transition-delay: 300ms;
    -moz-transition-delay: 300ms;
    -ms-transition-delay: 300ms;
    -o-transition-delay: 300ms;
    transition-delay: 300ms;
    }
    .submenu li:nth-child(3) {
    -webkit-transition-delay: 250ms;
    -moz-transition-delay: 250ms;
    -ms-transition-delay: 250ms;
    -o-transition-delay: 250ms;
    transition-delay: 250ms;
    }
    .submenu li:nth-child(4) {
    -webkit-transition-delay: 200ms;
    -moz-transition-delay: 200ms;
    -ms-transition-delay: 200ms;
    -o-transition-delay: 200ms;
    transition-delay: 200ms;
    }
    .submenu li:nth-child(5) {
    -webkit-transition-delay: 150ms;
    -moz-transition-delay: 150ms;
    -ms-transition-delay: 150ms;
    -o-transition-delay: 150ms;
    transition-delay: 150ms;
    }
    .submenu li:nth-child(6) {
    -webkit-transition-delay: 100ms;
    -moz-transition-delay: 100ms;
    -ms-transition-delay: 100ms;
    -o-transition-delay: 100ms;
    transition-delay: 100ms;
    }
    .submenu li:nth-child(7) {
    -webkit-transition-delay: 50ms;
    -moz-transition-delay: 50ms;
    -ms-transition-delay: 50ms;
    -o-transition-delay: 50ms;
    transition-delay: 50ms;
    }
    .submenu li:nth-child(8) {
    -webkit-transition-delay: 0s;
    -moz-transition-delay: 0s;
    -ms-transition-delay: 0s;
    -o-transition-delay: 0s;
    transition-delay: 0s;
    }

[在线演示](http://www.script-tutorials.com/demos/364/index.html)

[下载DEMO](http://www.script-tutorials.com/demos/364/source.zip)

### 总结: ###

我们只是随意地制作了一个css3下拉菜单。谢谢你耐心看完本教程，如果你喜欢今天的主题，请使用下面的社交平台将其分享给你的朋友吧！