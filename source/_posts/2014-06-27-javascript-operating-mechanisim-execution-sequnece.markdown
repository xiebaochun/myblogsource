---
layout: post
title:  "javascript运行机制及其执行顺序"
date: 2014-06-27 19:59
comments: true
categories: web
---

在前端开发领域已有些年头了，对javascript的认识也逐渐清晰明朗，在不同时期对任何事物都有不同的了解，或程度上，或性质上。今天写一下我对javascript运行机制的一些总结。

javascript的性质：描述性脚本语言，直接由browser动态解析执行。

那么js的解析原理和执行顺序是怎么样的呢？在此先了解一下基本的一些概念。

<!--more-->

## 1、js代码块 ##
例如：

    ''' javascript hello word http://xiebaochun.github.io

    <script type="text/javascript>
    console.log("this is code block1");
    </script>
    <script type="text/javascript>
    console.log("this is code block2");
    </script>

    '''

js按照代码块动态编译并执行，代码块之间相互独立，仅共享变量和方法。例如：

    <script type="text/javascript">  
      console.log(str);//因为没有定义str，所以浏览器会出错，下面的不能运行  
      console.log("this is block1");//没有运行到这里  
      var test = "this is first varaible";  
    </script>  
    <script type="text/javascript">  
      console.log("this is block2"); //这里有运行到  
      console.log(test); //打印"我是代码块一变量"  
    </script> 

上面的代码中代码块1中会报错，但不影响代码块2的执行，此即其独立性。代码块2可调用块1中的变量，即块间共享性。

## 2、声明式函数与赋值式函数 ##
js中，函数分为声明式函数与赋值式函数，具体可参考我之前写的[函数声明和函数表达式](http://xiebaochun.github.io/public/blog/2013/10/09/function-declarations-vs-function-expressions/)

这里简单举个例子：

    <script type="text/javascript">  
	     function Fn(){ //声明式函数  
	      
	     }  
	      
	     var Fn = function{  //赋值式函数（函数表达式）
	      
	     }  
    </script> 

重点理解：js预编译期，声明式函数会先被提取出来，然后再按顺序执行js代码。

## 3、预编译期与执行期 ##
js解析分为：预编译期（预处理）与执行期。

重点提示：预编译期JS会对本代码块中的所有声明的变量和函数进行处理（类似与C语言的编译），但需要注意的是此时处理函数的只是声明式函数，而且变量也只是进行了声明但未进行初始化以及赋值。

如：

    <script type="text/javascript">  
     Fn();  //执行结果："执行了函数2",同名函数后者会覆盖前者  
     function Fn(){ //函数1  
    console.log("执行了函数1");  
     }  
      
     function Fn(){  //函数2  
    console.log("执行了函数2");  
     }  
    </script>

-------------------------------------------------------

    <script type="text/javascript">  
      Fn();  //执行结果："执行了声明式函数",在预编译期声明函数及被处理了，所以即使Fn()调用函数放在声明函数前也能执行。  
      function Fn(){ //声明式函数  
     console.log("执行了声明式函数");  
      }  
      
      var Fn = function(){  //赋值式函数  
     console.log("执行了赋值式函数");  
      }  
    </script> 

-------------------------------------------------------

    //代码块一  
    <script type="text/javascript">  
      console.log(str);//浏览器报错,但并没有弹出信息窗  
    </script>  
    //代码块二  
    <script type="text/javascript">  
      console.log(str); //弹窗"undefined"  
      var str = "aaa";  
    </script>  
    //js在预处理期对变量进行了声明处理,但是并没有进行初始化与赋值，所以导致代码块二中的变量是unfiened的，而代码一中的变量是完全不存在<span style="font-family: Arial, Helvetica, sans-serif;">的，所以浏览器报错。</span> 

 理解了上面的几个术语，相信大家对JS的运行机制已经有了个大概的印象了，现在我们来看个例子：

    <script type="text/javascript">  
      Fn();  //浏览器报错:"undefined"  
    </script>  
    <script type="text/javascript">  
      function Fn(){ //函数1  
      alert("执行了函数1");  
      }  
    </script>

为什么运行上面的代码浏览器会报错呢？声明函数不是会在预处理期就会被处理了吗，怎么还会找不到Fn()函数呢？其实这是一个理解误点，我们上面说了JS引擎是按照代码块来顺序执行的，其实完整的说应该是按照代码块来进行预处理和执行的，也就是说预处理的只是执行到的代码块的声明函数和变量，而对于还未加载的代码块，是没法进行预处理的，这也是边编译边处理的核心所在。

## 总结： ##

      step 1.  读入第一个代码块。
      step 2.  做语法分析，有错则报语法错误（比如括号不匹配等），并跳转到step5。
      step 3.  对var变量和function定义做“预编译处理”（永远不会报错的，因为只解析正确的声明）。
      step 4.  执行代码段，有错则报错（比如变量未定义）。
      step 5.  如果还有下一个代码段，则读入下一个代码段，重复step2。
      step6. 结束。


 而根据HTML文档流的执行顺序，需要在页面元素渲染前执行的js代码应该放在<body>前面的<script>代码块中，而需要在页面元素加载完后的js放在</body>元素后面，body标签的onload事件是在最后执行的。

    <script type="text/javascript">  
    alert("first");  
    function Fn(){  
    alert("third");  
    }  
    </script>  
    <body onload="Fn()">  
      
    </body>  
    <script type="text/javascript">  
    alert("second");  
    </script>  


如果有什么问题可在留言板中留言，我会第一时间回答。

下面是测试数学公式：

$$
\begin{align*}
  & \phi(x,y) = \phi \left(\sum_{i=1}^n x_ie_i, \sum_{j=1}^n y_je_j \right)
  = \sum_{i=1}^n \sum_{j=1}^n x_i y_j \phi(e_i, e_j) = \\
  & (x_1, \ldots, x_n) \left( \begin{array}{ccc}
      \phi(e_1, e_1) & \cdots & \phi(e_1, e_n) \\
      \vdots & \ddots & \vdots \\
      \phi(e_n, e_1) & \cdots & \phi(e_n, e_n)
    \end{array} \right)
  \left( \begin{array}{c}
      y_1 \\
      \vdots \\
      y_n
    \end{array} \right)
\end{align*}
$$