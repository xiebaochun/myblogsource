---
layout: post
title: "log4net教程（.net的记录工具）"
date: 2013-09-13 14:00
comments: true
categories: other
---

## 介绍(Introduce) ##

.Net平台上的工具有很多，其中log4net就是其中一个很强大的工具。它的优点是简单。强大和易扩展性。

## 基础知识(The Basics) ##

log4net有三个部分组成：配置（configuration），设置（setup),还有调用（call)。配置一app.config或者web.config文件里完成（将会在下面进行详细介绍）。如果你想使用独立的配置文件来增加灵活性，请参考“Getting Away from app.config"章节。然后就是你需要选择存储配置信息的位置，并配置需要调用的几行代码来初始化一个记录器的链接。最后，就是简单的调用这个记录器链接的方法。这些都是很容易完成并理解的。
<!--more-->
## 记录权限（logging levels) ##

这里有几个记录权限，这五个权限都可以在你的代码里调用。它们是：

1. OFF 不记录任何信息 (不能被调用)
1. FATAL
1. ERROR
1. WARN
1. INFO
1. DEBUG
1. ALL 记录所有的信息 (不能被调用)

在你的代码或者在config文件中，这些levels都可以多次使用。这些各自代表的levels并没有设定规则（除了第一个和最后一个）。

## 配置（The Configuration) ##

设置一个log4net的一般方法是:在桌面应用中使用app.config文件，在web应用中使用web.config文件。为了使log4net能够正确的工作，在config文件里需要配置一些信息。这部分信息会告知log4net如何配置自己。不需要重新编译就可以改变设置的，这正是config文件最重要的地方。

### 根（Root） ###

你需要一个root的部分来提供你的top-levels的引用。这些记录器的信息都是继承你的根记录器（root)。root部分提供的唯一一个不同的记录是minimum level。既然所有的配置都继承自root，那么就没有appender会记录下面的特别指明要记录的信息。这是一个在你的应用中控制记录level的简单高效的方法。这里有一个默认等级为INFO（意味着DEBUG的信息将会被忽略） 的例子还有一个在root下的指向两个appender的无效引用。

    <root>
      <level value="INFO"/>
      <appender-ref ref="FileAppender"/>
      <appender-ref ref="ConsoleAppender" />
    </root>

### 添加记录器(Addition Loggers) ###

有时候你会想要你的程序中的某些特别部分的信息。log4net允许你在root记录器之外再指定一些记录引用。例如，这里我在config文件里放置了一个额外的记录器来记录来记录发生在OtherClass类的对象的控制信息：

    <logger name="Log4NetTest.OtherClass">
      <level value="DEBUG"/>
      <appender-ref ref="ConsoleAppender"/>
    </logger>

注意：记录器的名字是类和类所在的命名空间的全称。如果你想模拟一个整体命名空间，它将会列出你想要模拟的所有的命名空间。我再次建议尽量在多个记录器里重读使用appwenders。这样虽然可以完成任务，在莫可能会得到意想不到的结果。

### ConfigSection ###

在config文件里要设置的信息会比log4net的配置信息还要多。你需要指明log4net配置信息所要保存的地方。这里是一个简单的例子，主要是用来指明在XML里的”log4net“标签配置信息的存储位置：

    <configSection>
     <section name="log4net" type="log4net.Config.Log4netConfigurationSectionHandler,log4net"/>
    </configSection>

### Appender(General) ###

一个appender其实是一个记录的名字信息。它指明了这些信息会被记录在什么地方，是怎样被记录的，还有这些信息是在什么情况之下被记录的。下面是一个appender的一个例子：

    <appender name="ConsoleAppender" type="log4net.Appender.ConsoleAppender">

### Layout(局部） ###

在每个appender里面还必须要有一个layout（布局）部分。这也许和该所属的appender有不同的地方，但是基本上是一样的。你需要指明这些数据是以什么类型被写入的。这里有很多种选择，但是我们建议你使用模板提供的类型。他将会允许你指定你希望如何将这些数据写到数据文件夹中。如果你指定模板布局类型，你将会需要一个子标签来指定一个变换模型。这是一个数据写入的模板，我将会给你一个比较详细的选择变换模板的描述，但是现在，这里有一个指定的布局标签模型布局：
    

    <layout type="log4net.Layout.PatternLayout">
      <conversionPattern value="%date [%thread] %-5level %logger [%ndc] 
    - %message%newline"/>
    </layout>
    
### Conversion Patterns ###

如我之前提到的，用于末班布局的转换模板词条是告诉appenderr如何存储信息的。有很多不同的关键字可以用于这些模板中，和字符短语。这里我列举了一个我认为比较重要和比较有用的关键字。在log4net文档里可以找到所有的关键字。

%date-使用本地时间的信息输出数据。可以使用大括号或者像%date{MMMM dd,yyyy HH:mm:ss,fff}这种布局模型来格式化这个数据（这种布局模型将会输出“January 01,2011 14:15:43,767"这种格式）。但是log4net已经提供了一个格式化方式（ABSOLUTE,DATE,or ISO8601)，所以我比较建议使用这种格式化方式。
%utcdate-这和%date是一样的修饰符，但是它输出的事世界时间。这种修饰符和date/time的工作原理是一样的。
%exception-如果有异常被传进来，当异常发生后它将会被输入并写下一行信息。如果没有异常被传入，这个词条将会被忽略并且不会写下任何信息。通常会把它写在记录的最后面，并且通常会在在异常发生之前将信息写入记录中。
%level-这是你为事件（DEBUG,INFO,WARN,等）指定的等级。
%message-这是你传入到记录事件里的信息。
%newline-这是一个新记录词条，根据你的程序所运行的平台，它会被翻译成对应的合适的新词条格式。这是输入一个新词条的推荐方法，并且它没有执行问题，相对于在特定平台上的操作。
%timestamp-这是程序开始执行到此刻的总毫秒数。
%thread-它将返回创建词条所在线程的名字（或者是线程的ID，如果线程没有名字的话）。




原文地址：[http://www.codeproject.com/Articles/140911/log4net-Tutorial#](http://www.codeproject.com/Articles/140911/log4net-Tutorial#)

















