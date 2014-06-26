---
layout: post
title: "(web开发的编程习惯）coding conventions in web development"
date: 2013-10-15 10:30
comments: true
categories: web
---
![](http://www.script-tutorials.com/demos/362/thumb.png)

今天我要讨论的是一些编程习惯。每个人编程时间长了都会遇到编写代码风格的统一问题。我觉得每个人都会遇到这样的问题，就是不同的工程可以使用不同的规则和风格来编写，并且有时会很遇到很多麻烦，以至于你想要一些统一的编写规范。换句话说，编程习惯就是某种特定语言推荐的编程风格，方法和实践。这些习惯一般包括注释，文件结构管理，缩进，声明，空格，命名习惯，编程习惯和原则等。我们建议你遵循这些规则，他将会帮助你提高代码的可读性和易维护性。
<!--more-->
提供的大部分例子是PHP语言，但是很容易运用于其他语言中。

### 1.Tabulation(Tab) ###

首先，我先提出一个问题：什么情况下使用tab和空格？一般，indentation并不是所有语言必须的，是次要的。开发者最好是将结构转成人们易读的形式。特别的，indentation是让控制流结构比如循环，包含结构更加亲切熟悉。

我从来都没有真正理解Tabs和Spaces的争议。但是无论如何，我可以建议你使用space而不是Tab。以曾经有过这样的争议：当我们使用不同的软件查看代码时，tabs和space看起来不一样。仅仅是缩进两行代码，第一行使用tab,第二行使用4个空格。他将看起来和Nptepad++。但是用备份的版本和这个对比，你讲会注意到tab比4个空格还长（结果，我们得到一个很糟糕的格式化代码）。因此在调整你的工程时请尽量使用空格代替tab.这个习惯将会给你将来带来很大的帮助。

### 2.类的名字（class names) ###

为了避免类的命名重复，类名必须以一个前缀开头并且每个单词的首字母一大些开始。例如：
    
    class MyClassName{
     function MyClassName(){
       }
    }

### 3.变量名字（variable names) ###

因为严格的数据编写在大多数的web语言中的遗失（虽然还可以使用），为了方便，变量名应该以一个有意义小写字母开始：

可以使用的前缀：

- i integer
- f float/double
- s string
- a array
- o object
- r resource
- b boolean
- is boolean

前缀后面的单词的首字母都以大写字母开头。如果你需要声明一个私有变量，你可以使用下划线。例如(PHP):

    public $isFirstName=""some value";
    publid $isSeconedName="some value";
    private $_iVariable;
    $iDigit1=$iDigit2=10;
    $oMyClass=new MyClass();

### 4.常量名字（Constant names) ###

常量是静态变量，它意味着它的值是不会改变的。一般情况下，我们使用所有大写字母来命名常量（不要忘记可以使用可能的前缀），例如：

    define("PRJ_WEBSITE","HTTP://www.website.com/");
    define("PRJ_GLOBAL_CURRENCY","$");

### 5.函数声明（名字和格式） ###

所有的函数的名字都应该以小学字母开始，并且每个单词的首字母都应该使用大写字母。大括号应该和函数名在同一行（在参数之后）。例如：

    function getPropertyValue($sName){
    //your custom code is here
    }
    function setPropertyValue($sName,$sValue){
    //your custom code is here
    }

### 6.特殊结构规范（special structures formatting) ###

特殊结构，例如if/else,foreeach,while等因遵循以下规则：

- 结构名和随后的圆括号之间需要一个空格（增加可读性）
- 包含在花括号里的条件语言应该使用空格使其分开（增加可读性）
- 在左边圆括号之后和右边圆括号之前不应该有空格
- Open curly brace（大括号）在同一行
- 内部的条件语句应该使用tab来缩进

例如：

    foreeach($akey as $iKey=>$sValue){
     //your custom code is here
    }
    
    if($bCondition){
       //your custom code is here
    }else{
    //your custom code is here
    }
    
    switch($sKey){
     case 1:
     //your custom code is here
     break;
     case 2:
     //your custom code is here
     break;
     case 3:
     //your custom code is here
     break;
     default:
    //your custom code is here
     break;
    }

### 7.数据库表格名字（Database table names) ###

当你创建一个表格时，使用容易理解的前缀，并使用下划线将每个单词分开。使用大写字母不是必须的。表格的关键字同样适用，例如：

    CREATE TABLE IF NOT EXISTS 'myprj_records_table'(
       'id' int(11) NOT NULL AUTO_INCREMENT,
       'pid' int(11) NOT NULL,
       'snippet' varchar(128) NOT NULL,
       'description' varchar(255) NOT NULL,
    } ENGLISH=MyISAM DEFAULT　CHARSET=utf-8;

### 8.工程文件结构 ###

当我们在团队工作时保持一个良好的文件结构式很重要的。不要将所有的文件放在一个单独的文件里，这样会很混乱。例如：

    /root folder
      /backup
      /cache
      /classes
      /css
      /js
      /media
    /images
    /mp3
    /video
    
    index.php
    otherFiles.php

---

### 总结： ###

最后，我想说我不能强迫你遵循上面提到的规则，这只是建议。实际上，很多开发团队会使用他们自己的变成习惯，但是无论如何，我们的建议会所有人都很有帮助。我希望你喜欢这篇文章。我会非常感谢你将其分享给你的朋友们。祝你好运并欢迎再次回访！    

原文地址：[http://www.script-tutorials.com/coding-conventions-in-web-development/](http://www.script-tutorials.com/coding-conventions-in-web-development/)