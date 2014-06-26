---
layout: post
title: "xna for wp寒冰对战天使游戏"
date: 2013-09-12 17:05
comments: true
categories: other
---
最近迷上玩LOL了，特别喜欢寒冰这个英雄，突发奇想不如使用XNA框架在WP7上写个寒冰对战天使骑士的游戏（理论上wp8上也能运行）。下面简单介绍一下开发的过程吧。
     首先构思了一下游戏的整体逻辑：大概是这样，打开游戏应用，屏幕上方不断地有很多天使骑士从天空中飞过，寒冰的弓箭在屏幕的左下方，玩家可以控制弓箭的方向，按下射击按钮，射击天上飞的着的敌人。先预览一下游戏吧，如下：
![](http://www.devdiv.com/data/attachment/blog/201301/17/180627ejhseshh05hnpz0j.jpg)
<!--more-->
 每个天使骑士将以不同的位置和速度从屏幕右方进来，玩家可以通过按上面的两个箭头来调整弓箭的方向，按下射击按钮既可以发射冰箭了，射中后冰箭和敌人将会以动画方式消失。最多同时可以发射5枚冰箭，
现在还没有完善分数统计功能，还有游戏难度、关卡、声音（貌似windows phone平台不支持XACT）等。但是基本的游戏功能是满足的。
 
下面开始介绍步骤吧：
首先：制作一些游戏需要的图片，这里选的主题是寒冰，图片格式是png的：如下图所示：

![](http://www.devdiv.com/data/attachment/blog/201301/17/18121339aw2blaa32364ha.jpg)

有背景、按钮、弓、箭，还有敌人飞行是连续的动作、被击中的动画，如下图：
 
飞行动画：

![](http://www.devdiv.com/data/attachment/blog/201301/17/181615g230ltg323uqtbk0.jpg)

击中动画：

![](http://www.devdiv.com/data/attachment/blog/201301/17/1817227bdfbxbdv1jfm1jf.jpg)

好的，需要准备的素材就这些。
 
 
    下面看一下游戏是怎么实现的。
1、将上面的图片添加到工程的content中，如下所示：


![](http://www.devdiv.com/data/attachment/blog/201301/17/1822135tngbdoosr5b8zdn.jpg)

2、添加好了之后就可以实现游戏逻辑了，如下：

![](http://www.devdiv.com/data/attachment/blog/201301/17/1826291o4j4d4net4oojz1.jpg)

上面是game基本的逻辑实现，这里还新建了一个GameObject类（为敌人和冰箭而准备的）
详细介绍后续。后面还需要慢慢完善游戏。
 
 
 
PS:好像游戏画面搞不到全屏模式：
this.graphics.IsFullScreen=true;加上这句也没用
没有完全填充

我把分别率设置成：this.graphics.PreferredBackBufferWidth=480;
this.graphics.PreferredBackBufferHeight=800;
但是一点反应都没有
好像绘制背景就是固定死的一样
箭头所指区间怎么填充不了，难道是框架问题？

![](http://www.devdiv.com/data/attachment/blog/201301/17/190347liep4zq4kzgggvzg.jpg)