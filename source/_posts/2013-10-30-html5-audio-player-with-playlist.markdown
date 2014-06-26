---
layout: post
title: "制作HTML5音频播放器（HTML5 Audio Player With playlist）"
date: 2013-10-30 11:02
comments: true
categories: web 
---

![](http://www.script-tutorials.com/demos/363/thumb.png)

原文：[http://www.script-tutorials.com/html5-audio-player-with-playlist/](http://www.script-tutorials.com/html5-audio-player-with-playlist/)

译者：[晋文格墨](http://xiebaochun.github.io/)

<!--more-->

也许对于很多人来说这是人生中第一次制作音频播放器。通常你会选择一个可用的播放器，一般是flash播放器。但是你也许已经发现，flash播放器在移动设备（如iPhone/Android)上不能很好的工作。今天，我将教你如何使用HTML5技术制作你自己的音频播放器。这个播放器包含了以下元素：title(标题），author(作家），cover（封面），基本的控制（播放/暂停，前进/后退），两个滑动条（jQuery UI sliders）:音量滑动条和一个歌曲跟踪器(song tracker)，还有一个歌曲列表。

### 预览 ###

[在线演示](http://www.script-tutorials.com/demos/363/)

[下载例子](http://www.script-tutorials.com/demos/363/source.zip)

我相信你应该知道如何使用<audio>这个标准的标签来创建一个简单的音频播放器。在本教程里，我们会使用一个特殊的标签，我将使用所有所需的控制元素来创建我们的播放器。我们将会使用javascript写好的一个音频元素的事件处理来控制这个播放器。

### 步骤1，HTML###

像往常一样，我们需要在head部分包含几个文件。

    <!DOCTYPE html>
    <html lang="en">
    <head>
    <meta charset="utf-8" />
    <meta name="author" content="Script Tutorials" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=0" />
    <title>HTML5 Audio player with playlist | Script Tutorials</title>
    
    <!-- add styles and scripts -->
    <link href="css/styles.css" rel="stylesheet" type="text/css" />
    <script type="text/javascript" src="js/jquery-1.7.2.min.js"></script>
    <script type="text/javascript" src="js/jquery-ui-1.8.21.custom.min.js"></script>
    <script type="text/javascript" src="js/main.js"></script>
    </head>

现在看一下播放器的标签。

    <div class="player">
    <div class="pl"></div>
    <div class="title"></div>
    <div class="artist"></div>
    <div class="cover"></div>
    <div class="controls">
    <div class="play"></div>
    <div class="pause"></div>
    <div class="rew"></div>
    <div class="fwd"></div>
    </div>
    <div class="volume"></div>
    <div class="tracker"></div>
    </div>
    <ul class="playlist hidden">
    <li audiourl="01.mp3" cover="cover1.jpg" artist="Artist 1">01.mp3</li>
    <li audiourl="02.mp3" cover="cover2.jpg" artist="Artist 2">02.mp3</li>
    <li audiourl="03.mp3" cover="cover3.jpg" artist="Artist 3">03.mp3</li>
    <li audiourl="04.mp3" cover="cover4.jpg" artist="Artist 4">04.mp3</li>
    <li audiourl="05.mp3" cover="cover5.jpg" artist="Artist 5">05.mp3</li>
    <li audiourl="06.mp3" cover="cover6.jpg" artist="Artist 6">06.mp3</li>
    <li audiourl="07.mp3" cover="cover7.jpg" artist="Artist 7">07.mp3</li>
    </ul>

### 步骤2.CSS ###

现在可以将赤裸裸的HTML5文件变成漂亮的播放器了，为此，我们需要为每个元素定义样式。

css/styles.css

    .example {
    margin: 50px auto 0;
    width: 400px;
    }
    .player {
    background: transparent url("../images/spr.png") no-repeat scroll center top;
    height: 162px;
    position: relative;
    width: 326px;
    z-index: 2;
    }
    .title, .artist {
    font-family: verdana;
    left: 167px;
    position: absolute;
    
    -moz-user-select: none;
    -webkit-user-select: none;
    -ms-user-select: none;
    }
    .title {
    color: #FFFFFF;
    font-size: 14px;
    font-weight: bold;
    top: 23px;
    }
    .artist {
    color: #EEEEEE;
    font-size: 12px;
    top: 40px;
    }
    .pl {
    background: transparent url("../images/spr.png") no-repeat scroll -274px -175px;
    cursor: pointer;
    height: 34px;
    left: 270px;
    position: absolute;
    top: 20px;
    width: 32px;
    }
    .pl:hover {
    top: 21px;
    }
    .cover {
    background: transparent url(../data/cover1.jpg) no-repeat scroll center top;
    border-radius: 5px 5px 5px 5px;
    height: 94px;
    left: 20px;
    position: absolute;
    top: 20px;
    width: 94px;
    }
    .controls {
    cursor: pointer;
    height: 23px;
    left: 167px;
    position: absolute;
    top: 65px;
    width: 138px;
    }
    .controls .play, .controls .pause, .controls .rew, .controls .fwd {
    background: transparent url("../images/spr.png") no-repeat scroll 0 0;
    float: left;
    height: 100%;
    width: 33%;
    }
    .controls .play {
    background-position: -8px -171px;
    }
    .controls .pause {
    background-position: -8px -198px;
    display: none;
    }
    .controls .rew {
    background-position: -54px -171px;
    }
    .controls .fwd {
    background-position: -100px -171px;
    }
    .controls .play:hover {
    background-position: -8px -170px;
    }
    .controls .pause:hover {
    background-position: -8px -197px;
    }
    .controls .rew:hover {
    background-position: -54px -170px;
    }
    .controls .fwd:hover {
    background-position: -100px -170px;
    }
    .hidden {
    display: none;
    }
    .controls .visible {
    display: block;
    }
    .volume {
    height: 11px;
    left: 186px;
    position: absolute;
    top: 96px;
    width: 112px;
    }
    .tracker {
    height: 15px;
    left: 20px;
    position: absolute;
    top: 126px;
    width: 285px;
    }
    .ui-slider-range {
    background: transparent url("../images/spr.png") no-repeat scroll 5px -222px;
    height: 100%;
    position: absolute;
    top: 0;
    }
    .ui-slider-handle {
    cursor: pointer;
    height: 10px;
    margin-left: -5px;
    position: absolute;
    top: 2px;
    width: 10px;
    z-index: 2;
    }
    .volume .ui-slider-handle {
    background: url("../images/spr.png") no-repeat scroll -201px -188px rgba(0, 0, 0, 0);
    height: 13px;
    width: 13px;
    }
    .playlist {
    background-color: #333333;
    border-radius: 5px 5px 5px 5px;
    list-style-type: none;
    margin: -10px 0 0 2px;
    padding-bottom: 10px;
    padding-top: 15px;
    position: relative;
    width: 326px;
    z-index: 1;
    }
    .playlist li {
    color: #EEEEEE;
    cursor: pointer;
    margin: 0 0 5px 15px;
    }
    .playlist li.active {
    font-weight: bold;
    }

### 步骤3.JavaScript ###

现在这个漂亮的播放器还不能做任何事情-不可用的状态。为了让其能正常工作，你需要为其添加一些事件和方法。首先，我们需要初始化几个变量。

js/main.js

    // inner variables
    var song;
    var tracker = $('.tracker');
    var volume = $('.volume');
    
    // initialization - first element in playlist
    initAudio($('.playlist li:first-child'));
    
    // set volume
    song.volume = 0.8;
    
    // initialize the volume slider
    volume.slider({
    range: 'min',
    min: 1,
    max: 100,
    value: 80,
    start: function(event,ui) {},
    slide: function(event, ui) {
    song.volume = ui.value / 100;
    },
    stop: function(event,ui) {},
    });
    
    // empty tracker slider
    tracker.slider({
    range: 'min',
    min: 0, max: 10,
    start: function(event,ui) {},
    slide: function(event, ui) {
    song.currentTime = ui.value;
    },
    stop: function(event,ui) {}
    });

然后为控制按钮添加事件处理。play和Pause按钮。

    // play click
    $('.play').click(function (e) {
    e.preventDefault();
    
    playAudio();
    });
    
    // pause click
    $('.pause').click(function (e) {
    e.preventDefault();
    
    stopAudio();
    });

为了切换列表的歌曲，我们需要停止当前播放的歌曲，选择列表里的下一首（或者上一首），并重新初始化我们的Audio元素。Forward/Rewind按钮。

    // forward click
    $('.fwd').click(function (e) {
    e.preventDefault();
    
    stopAudio();
    
    var next = $('.playlist li.active').next();
    if (next.length == 0) {
    next = $('.playlist li:first-child');
    }
    initAudio(next);
    });
    
    // rewind click
    $('.rew').click(function (e) {
    e.preventDefault();
    
    stopAudio();
    
    var prev = $('.playlist li.active').prev();
    if (prev.length == 0) {
    prev = $('.playlist li:last-child');
    }
    initAudio(prev);
    });

最后，就是一些处理歌曲列表的函数。

    // show playlist
    $('.pl').click(function (e) {
    e.preventDefault();
    
    $('.playlist').fadeIn(300);
    });
    
    // playlist elements - click
    $('.playlist li').click(function () {
    stopAudio();
    initAudio($(this));
    });

### 步骤4.Images ###

所有的用到的图片（images)都打包到一个叫nav.png的sprite（图片）文件里。

![](http://www.script-tutorials.com/demos/363/images/spr.png)

[在线演示](http://www.script-tutorials.com/demos/363/)

[下载DEMO](http://www.script-tutorials.com/demos/363/source.zip)


### 结语： ###

这就是今天所有的内容，我们只是制作了一个有趣的音频播放器。感谢您耐心看完本教程，如果你喜欢此文章的话，请使用下面提供的社交平台分享给你所有的朋友吧！