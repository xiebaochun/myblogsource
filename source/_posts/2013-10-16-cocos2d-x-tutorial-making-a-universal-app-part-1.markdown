---
layout: post
title: "Cocos2D-X教程1：制作一个打地鼠游戏（Cocos2D-X Tutorial:Making a Universal App:Part 1）"
date: 2013-10-16 09:00
comments: true
categories: ios 
---
![](http://cdn2.raywenderlich.com/wp-content/uploads/2011/01/WhackAMole1.jpg)

击打这个地鼠！

原文地址：[http://www.raywenderlich.com/48180/cocos2d-x-tutorial-making-a-universal-app-part-1](http://www.raywenderlich.com/48180/cocos2d-x-tutorial-making-a-universal-app-part-1)

译者：晋文格墨   

注：译者水平有限，如有错误，请批评斧正。

本教程介绍的是如何使用Cocos2D-iPhone创建一个打地鼠游戏，但是为什么要费这么大力气创建一个Cocos2D-X版本的呢？我认为有以下几个原因：
<!--more-->

1.你可以通过本教程重新熟悉一下如何使用Cocos2D-X框架创建一个完整的游戏。

2.这是一个很好的机会去学习如何制作一个支持多分辨率和纵宽比的游戏。

3.Cocos2D-X是一个非常火的游戏引擎（特别是在中国），它是为多平台游戏而设计的（不像Cocos2D-iPhone或者Android,它是iOS特有的）。所以在本教程介绍Cocos2D-X是很有必要的。

本教程是以其他Cocos2D-X教程为基础的：

[Cocos2D-X for iOS and Android:Getting Started](http://www.raywenderlich.com/11283/cocos2d-x-for-ios-and-android-getting-started)

[Cocos2d-X for ios and Android: Space Game](http://www.raywenderlich.com/33752/cocos2d-x-tutorial-for-ios-and-android-space-game)

[Cocos2D-X Tile Map Tutoral:Part1](http://www.raywenderlich.com/39113/cocos2d-x-tile-map-tutorial-part-1)

[Cocos2D-X Tile Map Tutoral:Part2](http://www.raywenderlich.com/40544/cocos2d-x-tile-map-tutorial-part-2)

如果你还没看过这些Cocos2D-X教程（或者相关知识），我建议你先学习一下。

本系列教程分为两个部分。第一部分，我们将会创建游戏的基本内容-让小地鼠从地洞里弹出来。为了能让游戏在iPhone,iPad,和视网膜分辨率下达到很好的显示的效果，还有高效率，你将会花很多时间去思考如何管理素材和坐标。

### 规划素材配置：概要 ###

为了能让游戏在iPhone,iPad和Android设备上工作正常，你首先要需要花一些时间来思考如何处理这些素材。

为了理解如何设置素材的合理尺寸，你首先需要学习一下两点知识：

- 视网膜分辨率和Cocos2D-X
 
- iPhone,iPad和Android纵横比

现在开始吧！

### 视网膜分辨率和Cocos2D-X ###

好消息，Cocos2D-X的最新版本2.14完美解决了多分辨支持的问题，所以你可以较之前更容易制作了。

1.自从Cocos2D-X的2.04版本默认支持视网膜分辨率，你只要在你的app delegate里重新初始化你的资源就行了。

2.添加高分辨率的sprites到你的app，但是不要使用@2x或者-hd拓展名，你只要将所有高分辨率资源放在名为hd的文件夹中。当加载你的sprites时，根据屏幕的像素，Cocos2D-X将会从这个文件夹里加载hd图片到视网膜分辨。

3.现在在Cocos2D-X里你可以使用点代替像素布局你的sprites（精灵）。

当实际运用时，最简单的做法就是让你的美工制作所需的最高分辨率图片（比如视网膜分辨率的2X尺寸），并且你可以很容易将这些图片缩小到适合iPhone大小的尺寸。

你也许好奇为什么要弄成两个不同尺寸的图片-为什么不只加载大图并且通过代码来调整它的大小？要知道，对app来说将纹理图片加载到内存中是消耗内存的一种做法，所以如果你准备将app运行在不需要高分辨率的图片设备上时，加载设备对应的小图片可以节省很多内存空间。

但是不用担心-你不需要在photoshop上反复地缩小这些图片。TexturePacker提供了一个很棒的功能，它可以轻松地将这些图片进行缩放并提供所有分辨率的图片，并且在本教程中会使用到这个功能。

### iPhone,iPad和Android纵横比 ###

现在处理视网膜屏幕很简单，但是对于iPad和Android呢？

在本教程，如果你知道怎么样处理iOS设备，你只要把重点放在在iOS设备上，你也可以处理Android设备上的情况。

iPhone的分辨率有480*320,960*640和1136*640，即纵横比是1.5或者1.75。但是iPad的分辨率有768*1024和1536*2048，即纵横比为1.33。

这就是说，如果你有一张刚好可以填满iPad屏幕的图片，又想将其用于iPhone设备上，但不能吻合地平铺于iPhone屏幕上。也许你会讲这张图片缩放为适合iPhone宽度的大小（即乘0.9375）：将会得到720*960的尺寸，并且额外多出来的部分不被裁减掉！

![](http://cdn5.raywenderlich.com/wp-content/uploads/2011/01/Aspect-Ratio-Diff.jpg)

这样会变得很麻烦，因为不仅背景图片的有问题，而且由于纵横比的关系让在设备上使用相同的坐标变得很困难。

这里有处理这个问题的几个策略，这是一些开发者经常用到的：

- 如上图所示，在屏幕的中央放置iPhone视网膜屏幕尺寸（640*960）大小的“可玩区域”。这样在“可玩区域”周围会留下额外的空间-你可以用一张背景图片将其覆盖掉并让玩家不容易察觉到。这样你就可以轻松地在设备之间转换坐标了，并且可重复使用素材（高分辨率的资源用于iPad和视网膜屏，普通分辨率资源用于普通的iPhone或者普通的iPad上）。这也是本教程使用的方法。

- 你可以在iPad的两侧各留42像素的空隙，使剩下的区域的从纵横比就和iPhone一样，这样里面的主区域分辨率就是684*1024.如果让你内容适应于684*1024大小的盒子里，那么你可以将这些图片缩小，并适用于所有的设备。

- 你可以为iPhone,iPad和视网膜屏幕准备不同的图片和不同的坐标系。这样可以保持最大的灵活性，但是会增加资源大小并且在不同的设备上从新布局对象的位置。

### 规划素材：总结 ###

基于上面的讨论，现在开始规划一下本教程。

- 这些素材被限制在了一个960*640规格的可玩区域，在带有视网膜屏幕的iPhone上使用全屏设置，并置于iPad的屏幕中央。

- 通过TexturePacker将这些素材缩小一半，用于普通iPhone设备。

- 将大尺寸的素材放置于名为hd的文件夹，小尺寸的放于名为sd的文件夹。

- 背景需要特别处理，因为它总是要铺满整个屏幕。背景图片将会做成1024*768的规格（iPad的规格）以至于能铺满整个屏幕。同样的背景图片也会用于iPhone设备应为图片完全可以铺满屏幕。一些背景会溢出屏幕，但但是这并不会对这种特殊背景造成影响。

- iPad版本的代码将会使用hd文件夹的图片，将坐标转到可玩区域内，并使用合适的纵横比，等。

请[下载](http://d1xzuxjlafny7l.cloudfront.net/downloads/WhackAMoleArt.zip)本教程的素材图片，素材有[lovely lady](http://www.vmwstudios.com/)制作。解压文件并看看是如何配置的：

- 在foreground文件夹中，前景图片是1024*768尺寸（iPad的规格）。但是实际上被分为了两部分：下面和上面各一部分，他被分为两部分，这样你可将地鼠置于中间，这样地鼠就好像是从地里出来的那样。

- 在background文件夹中，背景的纵横比是1.33（iPad的纵横比），但是其实际规格是512*384。这是因为背景上仅仅显示3个地鼠洞，所以没有必要费这么大力气加载1024*1024规格的纹理图片。加载一个小的纹理图片并将其放大。

- 在sprites文件夹，所有的精灵的大小刚刚好适用于960*640规格的可玩区域。注意，这里有一个地鼠还有对应的两个动画（一个微笑表情和一个被打动作）。

### 开始 ###

打开Xcode,选择FIle\New\Project...并使用iOS\cocos2d-x\cocos2d模板创建一个工程，然后点击Next按钮。将工程名命名为WhackAMole,将Device familiy设置为Universal,并保存工程。

![](http://cdn1.raywenderlich.com/wp-content/uploads/2013/08/figure1.jpg)

使用使用Finder将刚刚下载并解压后的文件添加到WhackAMole工程目录中。它应该和Classes文件夹位于同一级目录中。

![](http://cdn2.raywenderlich.com/wp-content/uploads/2013/08/figure2.jpg)

确保安装了TexturePacker,并在你的机器上能正常运行。如果你没有准备好，或者不知道如何做，请查看[此教程](http://www.raywenderlich.com/2361/texture-packer-tutorial-how-to-create-and-optimize-sprite-sheets-in-cocos2d-with-pixel-formats)。

现在你将要使用TexturePacker来创建工程所需的精灵表单。你讲通过TexturePacker的命令行工具和Xcode集成环境来完成此任务，所以不需要使用TexturePacker可视化工具。

现在选择Resources文件夹，按下Command+K,选择OSX\Other\Shell Script。并点击Next.将文件命名为PackerTextures.sh,并点击Create。

然后将下面的代码将PackerTexture,sh的内容覆盖掉：


    #!/bin/sh
    TP="/usr/local/bin/TexturePacker"
    if [ "${ACTION}" = "clean" ]
    then
    echo "cleaning..."
    rm Resources/hd/background*
    rm Resources/hd/foreground*
    rm Resources/hd/sprites*
    rm Resources/sd/background*
    rm Resources/sd/foreground*
    rm Resources/sd/sprites*
    else
    echo "building..."
    
    ${TP} --smart-update \
    --format cocos2d \
    --data Resources/hd/background.plist \
    --sheet Resources/hd/background.pvr.ccz \
    --dither-fs \
    --opt RGB565 \
    Art/background/*.png
    ${TP} --smart-update \
    --format cocos2d \
    --data Resources/sd/background.plist \
    --sheet Resources/sd/background.pvr.ccz \
    --dither-fs \
    --scale 0.5 \
    --opt RGB565 \
    Art/background/*.png
    ${TP} --smart-update \
    --format cocos2d \
    --data Resources/hd/foreground.plist \
    --sheet Resources/hd/foreground.pvr.ccz \
    --dither-fs-alpha \
    --opt RGBA4444 \
    Art/foreground/*.png
    ${TP} --smart-update \
    --format cocos2d \
    --data Resources/sd/foreground.plist \
    --sheet Resources/sd/foreground.pvr.ccz \
    --dither-fs-alpha \
    --scale 0.5 \
    --opt RGBA4444 \
    Art/foreground/*.png
    ${TP} --smart-update \
    --format cocos2d \
    --data Resources/hd/sprites.plist \
    --sheet Resources/hd/sprites.pvr.ccz \
    --dither-fs-alpha \
    --opt RGBA4444 \
    Art/sprites/*.png
    ${TP} --smart-update \
    --format cocos2d \
    --data Resources/sd/sprites.plist \
    --sheet Resources/sd/sprites.pvr.ccz \
    --dither-fs-alpha \
    --scale 0.5 \
    --opt RGBA4444 \
    Art/sprites/*.png
    fi
    exit 0

然后将PackTevture移动到文件路径：

![](http://cdn4.raywenderlich.com/wp-content/uploads/2013/08/figure3.jpg)

不要忘了将PackTexture.sh的引用移动到工程中：

![](http://cdn5.raywenderlich.com/wp-content/uploads/2013/08/figure4.jpg)

TexturePacker脚本将为背景图片，前景图片和精灵创建了精灵表单。并为他们准备了高质量和普通质量的图片。

既然每章图片的存放格式都是pvr.ccz,所以跟有效地利用了内存和硬盘空间。