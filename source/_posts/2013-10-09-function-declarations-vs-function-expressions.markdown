---
layout: post
title: "函数声明和函数表达式（function declarations vs function expressions）"
date: 2013-10-09 13:54
comments: true
categories: web
---

原文地址：[http://javascriptweblog.wordpress.com/2010/07/06/function-declarations-vs-function-expressions/](http://javascriptweblog.wordpress.com/2010/07/06/function-declarations-vs-function-expressions/)

在开始之前先提出一下几个问题。在下面的几种情况下弹出内容分别是什么？
<!--more-->
情况1:

    function foo(){
    function bar() {
    return 3;
    }
    return bar();
    function bar() {
    return 8;
    }
    }
    alert(foo());

情况2：
    function foo(){
    var bar = function() {
    return 3;
    };
    return bar();
    var bar = function() {
    return 8;
    };
    }
    alert(foo());

情况3：

    alert(foo());
    function foo(){
    var bar = function() {
    return 3;
    };
    return bar();
    var bar = function() {
    return 8;
    };
    }

情况4：

    function foo(){
    return bar();
    var bar = function() {
    return 3;
    };
    var bar = function() {
    return 8;
    };
    }
    alert(foo());

如果你给出的答案不是8,3,3和[报出错误：bar is not a function],请继续往下读（确实要往下读=-=）

#### 什么是函数声明？ ####

函数声明是定义了一个命名了的函数变量，并不需要声明变量。函数声明出现正独立的结构中并且不能嵌套在非函数代码块中。对比一下变量的声明就比较好理解了。就像变量什么声明那样要以“var"开头，函数声明则以”function“开头。

例如：

    function bar() {
    return 3;
    }

ECMA5(13.0)定义的语法规则为：

    function Identifier ( FormalParameterListopt ) { FunctionBody }

函数的名字在它的做作用域和其父作用域中都是可见的（这样非常好，否则它将不可读）

    function bar() {
    return 3;
    }
     
    bar() //3
    bar  //function

#### 什么是函数表达式？ ####

一个函数表达式定义了一个大量表达式语义的一个部分（通常是一个变量委派）。通过函数表达式可以定义带名称的函数也可以是一个匿名函数。函数表达式不一定需要以”function"开始（因此下面的用圆括号包围的是self invoking例子）

例如：

    //anonymous function expression
    var a = function() {
    return 3;
    }
     
    //named function expression
    var a = function bar() {
    return 3;
    }
     
    //self invoking function expression
    (function sayHello() {
    alert("hello!");
    })();

ECMA 5（13.0）定义的语法规则为：

    function Identifieropt ( FormalParameterListopt ) { FunctionBody }

（虽然这会让你感觉不完全，因为它遗漏了必要的条件（就是生命了一个表达式并没有以“function"开头）
函数名（任何）在其作用域之外是不可见的（和函数声明是相反的）、

#### 什么是函数Statement？ ####

又是可以把它看做是函数声明的别名。但是kangax指出，在mozilla中一个函数statement是一个函数声明的拓展，允许函数声明语法在声明允许的任何地方使用。当然现在还没有任何标准，所以不推荐用于产品的开发。

#### 关于问题的解答 ####

问题1使用函数声明，意味着他取得了hoisted...

#### 等等，什么是hoisted？ ####

引用Ben cherry的文章里的一句话“函数声明和函数变量总是被编译器移动到javascript作用域的最上面”。

函数声明会覆盖前面的函数声明，所以当编译器处理完情况1中的代码后，执行的效果是这样的：

    //**Simulated processing sequence for Question 1**
    function foo(){
    //define bar once
    function bar() {
    return 3;
    }
    //redefine it
    function bar() {
    return 8;
    }
    //return its invocation
    return bar(); //8
    }
    alert(foo());

#### 但是我们总是受到这样的教训：返回一个不可获取的声明之后编写代码 ####

javascript执行上下文（ECMA5将其分为LexicalEnvironment,VariableEnvironment,和ThisBinding)和处理（一些被激活的状态队列）。声明属于VariableEnvironment当执行域被键入时。它们是和statement区分开来的，并不遵循处理的规则。

#### 函数表达式不能被挂起吗？ ####

这个要根据表达式来做判断。让我们看看问题2的表达式：

    var bar = function() {
    return 3;
    };
    
左边的（var bat）是一个变量声明。变量声明被挂起但是赋予它的表达式并没有。所以当bar被挂起，编译器最初将其设置为var bar=underfined.函数定义本身并没有被挂起。

(ECMA 5 12.2 A变量和一个初始化器被赋值为它的AssignmentExpression,当VariableStatement被执行，并不是当变量被创建时）

因此问题2中的代码执行的更加有顺序。

    //**Simulated processing sequence for Question 2**
    function foo(){
    //a declaration for each function expression
    var bar = undefined;
    var bar = undefined;
    //first Function Expression is executed
    bar = function() {
    return 3;
    };
    // Function created by first Function Expression is invoked
    return bar();
    // second Function Expression unreachable
    }
    alert(foo()); //3

#### Ok I think that makes sense. By the way, you’re wrong about Question 3. I ran it in Firebug and got an error ####

Try saving it in an HTML file and running it over Firefox. Or run it in IE8, Chrome or Safari consoles. Apparently the Firebug console does not practice function hoisting when it runs in its “global” scope (which is actually not global but a special “Firebug” scope – try running “this == window” in the Firebug console).

#### Question 3 is based on similar logic to Question 1. This time it is the foo function that gets hoisted. ####

Now Question 4 seems easy. No function hoisting here…

Almost. If there were no hoisting at all, the TypeError would be “bar not defined” and not “bar not a function”. There’s no function hoisting, however there is variable hoisting. Thus bar gets declared up front but its value is not defined. Everything else runs to order.

    //**Simulated processing sequence for Question 4**
    function foo(){
    //a declaration for each function expression
    var bar = undefined;
    var bar = undefined;
    return bar(); //TypeError: "bar not defined"
    //neither Function Expression is reached
    }
    alert(foo());

#### What else should I watch out for? ####

Function Declarations are officially prohibited within non-function blocks (such as if) . However all browsers allow them and interpret them in different ways.

For example the following code snippet in Firefox 3.6 throws an error because it interprets the Function Declaration as a Function Statement (see above) so x is not defined. However in IE8, Chrome 5 and Safari 5 the function x is returned (as expected with standard Function Declarations).

    function foo() {
    if(false) {
    function x() {};
    }
    return x;
    }
    alert(foo());

#### I can see how using Function Declarations can cause confusion but are there any benefits? ####

Well you could argue that Function Declarations are forgiving – if you try to use a function before it is declared, hoisting fixes the order and the function gets called without mishap. But that kind of forgiveness does not encourage tight coding and in the long run is probably more likely to promote surprises than prevent them. After all, programmers arrange their statements in a particular sequence for a reason.

#### And there are other reasons to favour Function Expressions? ####

How did you guess?

a) Function Declarations feel like they were intended to mimic Java style method declarations but Java methods are very different animals. In JavaScript functions are living objects with values. Java methods are just metadata storage. Both the following snippets define functions but only the Function Expression suggests that we are creating an object.

 //Function Declaration
function add(a,b) {return a + b};
//Function Expression
var add = function(a,b) {return a + b}; 

b) Function Expressions are more versatile. A Function Declaration can only exist as a “statement” in isolation. All it can do is create an object variable parented by its current scope. In contrast a Function Expression (by definition) is part of a larger construct. If you want to create an anonymous function or assign a function to a prototype or as a property of some other object you need a Function Expression. Whenever you create a new function using a high order application such as curry or compose you are using a Function Expression. Function Expressions and Functional Programming are inseparable.

    //Function Expression
    var sayHello = alert.curry("hello!");

#### Do Function Expressions have any drawbacks? ####

Typically functions created by Function Expressions are unnamed. For instance the following function is anonymous, today is just a reference to an unnamed function:

    var today = function() {return new Date()}

Does this really matter? Mostly it doesn’t, but as Nick Fitzgerald has pointed out debugging with anonymous functions can be frustrating. He suggests using Named Function Expressions (NFEs) as a workaround:

    var today = function today() {return new Date()}

However as Asen Bozhilov points out (and Kangax documents) NFEs do not work correctly in IE < 9

#### Conclusions? ####

Badly placed Function Declarations are misleading and there are few (if any) situations where you can’t use a Function Expression assigned to a variable instead. However if you must use Function Declarations, it will minimize confusion if you place them at the top of the scope to which they belong. I would never place a Function Declarations in an if statement.

Having said all this you may well find yourself in situations where it makes sense to use a Function Declaration. That’s fine. Slavish adherance to rules is dangerous and often results in tortuous code. Much more important is that you understand the concepts so that you can make your own informed decisions. I hope this article helps in that regard.

Comments are very welcome. Please let me know if you feel anything I’ve said is incorrect or if you have something to add.

See also [ECMA-262 5th Edition](http://es5.github.io/) sections 10.5, 12.2, 13.0, 13.2