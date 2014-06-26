---
layout: post
title: "面向 Java 开发人员的 Ajax: Google Web Toolkit 入门"
date: 2013-09-23 13:54
comments: true
categories: web
---
简介： Ajax 被用于创建更加动态和交互性更好的 Web 应用程序。Google Web Toolkit (简称GWT) 是 Google 推出的 Ajax 应用开发包，GWT 支持开发者使用Java 语言开发 Ajax 应用。本文中作者将介绍如何使用 GWT 开发 Ajax 应用的基本方法和步骤。
<!--more-->
## Ajax简介 ##
Ajax是 Asynchronous JavaScript and XML（以及 DHTML 等）的缩写，由XHTML、CSS、JavaScript、XMLHttpRequest、XML等技术组合而成，是当前Web应用开发领域的热门技术，用于创建更加动态和交互性更好的Web应用程序，提升用户的浏览体验。
Ajax的核心是JavaScript对象XmlHttpRequest。XmlHttpRequest处理所有服务器通信的对象，是一种支持异步请求的技术。简而言之，XmlHttpRequest使您可以使用JavaScript向服务器提出请求并处理响应，而不阻塞用户。
Ajax并不是本文的中心关注点，因此这里不再赘述，请大家访问参考资源区的相关链接，了解更多关于Ajax技术和应用方面的知识。

## 为什么选择GWT？ ##

众所周知，即使对于Ajax技术非常熟悉的开发者而言，Ajax应用的开发和调试过程也不是一件容易的事情，更困难的是，到目前为止，一直没有出现合适的开发工具能够支持Ajax应用的开发和调试。
与此相反的是，Java语言--企业应用开发的主流语言-的开发和调试过程却因为有各种各样开发工具的支持而简单的多，而且这样的开发工具我们可以免费获得，比如Eclipse、NetBeans。
如果能够应用Java语言开发Ajax，Ajax应用开发的最大难题-开发工具的缺失-就将迎刃而解。这种情况下，我们就可以既充分利用Java语言的开发优势降低Ajax应用开发的难度，加快Ajax应用的开发速度，为Ajax的大规模应用创造可能，又可以充分发挥Ajax技术的优势，创建更加动态和交互性更好的Web应用程序，提升用户的浏览体验。
Google Web Toolkit(简称GWT)的出现为我们提供了这种可能。GWT是Google推出的Ajax应用开发包，支持开发者使用Java语言开发Ajax应用。GWT框架本身是开源的，但是GWT中的开发工具仅仅提供开发用License，不允许分发。
GWT提供了一组基于Java语言的开发包，这个开发包的设计参考Java AWT包设计，类命名规则、接口设计、事件监听等都和AWT非常类似。熟悉Java AWT的开发者不需要花费多大的力气就能够快速的理解GWT开发工具包，将更多地时间投入到GWT应用的开发过程中。
开发出来的Java应用将由GWT开发包提供的编译工具编译后声生成对应的、应用了Ajax技术的Web应用，Java应用中出现的、和服务器之间的交互动作被自动生成的异步调用代码所代替。

## GWT特性 ##

GWT除了支持将应用Java语言开发的应用转化为Ajax应用，同时提供了更多的高级特性，下面是这些特性的简单描述。

1.	GWT编译器
GWT编译器是GWT的核心，负责完成将Java代码翻译很Ajax内容的工作。GWT编译器能够翻译Java语言的大部分特性。包括支持Java语言中的基本类型、违例处理等，支持java.lang包和java.util包中的绝大部分类和接口，支持正则表达式和序列化。
2.	跨平台支持
如果你使用GWT中提供的显示组件(比如Button)和组装组件(比如VerticalPanel)，GWT编译生成的Ajax应用能够支持大部分的浏览器和操作系统，比如Internet Explorer、Firefox等，也能够支持Linux、Windows等不同操作系统。这是因为GWT最大限度的将这些控件翻译成浏览器内置的类型。比如Button类编译后生成的是标准HTML：<input type="button">。
GWT建议使用CSS修饰页面元素的显示效果。GWT的类中很少提供访问页面元素样式属性的方法，我们可以直接在CSS文件中通过对应的样式名称来设置页面元素的默认显示效果。比如使用 .gwt-Button { font-size: 150%; } 使用Button元素的默认显示效果。
3.	宿主模式(Hosted Mode)
宿主模式是指我们和没有转换为Ajax应用的GWT应用交互的状态。当我们开发和调试时，我们就一直处在宿主模式下。在这种情况下，Java虚拟机使用GWT内置的浏览器运行GWT应用编译后的class内容，因此能够提供"编码、测试、调试"过程的最佳速度。
我们可以运行com.google.gwt.dev.GWTShell启动宿主模式。
4.	Web模式(Web Mode)
Web模式是指已经成功转化为Ajax应用的状态，这种状态下，我们已经开始通过Web方式来访问Ajax应用了。
在Web模式下运行时，不再需要GWT工具包或者JVM的支持。
5.	命令行工具
GWT工具包中提供了几个非常适用的小工具来帮助我们更快的建立GWT应用开发环境：projectCreator、applicationCreator、junitCreator。
projectCreator 
创建在Eclipse中开发GWT应用所需要的项目基本文件和可选的Ant buildfile文件。
applicationCreator 
applicationCreator命令用于创建基本的HelloWorld！应用和GWT应用开发环境。
junitCreator 
生成junti测试代码。
通过上面的内容，我们已经了解了GWT工具集工作的基本原理和主要特性。有些迫不及待了吧，那么就请随我一起进入GWT应用开发的过程吧，享受应用Java语言开发Ajax应用带来的简单和便利。
本文中所有的环境准备、实例开发和说明均针对Windows操作平台，如果使用其他的操作系统，请根据实际情况进行适当的调整。

## 环境准备##


1、	下载和安装JDK1.4.X

GWT工具包的编译需要JDK支持，因此在安装GWT工具包之前请下载和安装合适的JDK。GWT工具支持Java语言1.4版本或者以下版本，因此JDK版本选择JDK1.4.X是比较合适的，不需要采用最新的JDK5.0或者更高版本。
请访问java.sun.com网站上下载安装版本，下载后安装到C:/jdk目录下，本书中的后续内容中将使用%JAVA_HOME%变量来引用这个目录。
您可以根据实际情况将JDK安装到任意目录下。但是在运行本文中例子的时候，请记住将对应的%JAVA_HOME%变量替换为您的实际安装目录。

2、	下载和安装GWT

请访问http://code.google.com/webtoolkit/下载GWT的最新版本，将下载的压缩文件解压缩到C:/GWT目录下。本书中的后续内容中将使用%GWT_HOME%变量来引用这个目录。
GWT工具包支持不同的操作系统，请根据自己的操作系统选择合适的安装包。

您可以根据实际情况将GWT安装到任意目录下。但是在运行本文中例子的时候，请记住将对应的%GWT_HOME%变量替换为您的实际安装目录。

## 第一个例子-Hello World！ ##

下面的内容中我们将介绍如何使用GWT工具集来完成第一个GWT的例子-"Hello World！"，并且将使用GWT编译及将他转化为Ajax应用，在浏览器中完成测试。
我们要完成的例子要实现的功能包括：

1、	在页面上显示一个按钮

2、	点击该按钮，默认情况下，我们将在按钮后面紧跟着显示字符串"Hello World!"。

3、	如果点击按钮时，"Hello World!"字符串已经显示在浏览器中，我们要将他隐藏起来。 我们现在开始使用GWT工具集完成"Hello World！"例子的开发，下面的步骤是完成"Hello World！"例子开发环境配置、应用开发、编译的基本步骤，同样适用于其他GWT应用的开发，只是根据实际情况可能有增减。

1、	创建GWT应用开发环境

从上面的GWT特性部分我们知道，GWT工具包中提供的applicationCreator命令行工具可以帮助我们创建GWT应用开发所需要的环境，因此我们可以直接使用applicationCreator帮助我们完成这项工作。
打开命令行工具，进入C:/根目录下，执行"mkdi"命令创建新的文件目录HelloWorld。


    C:\> mkdir HelloWorld

执行下面的命令将%JAVA_HOME%\bin目录和%GWT_HOME%目录加入到PATH路径中。

    C:\>set path=%JAVA_HOME%\bin;%GWT_HOME%

请将命令行中的%JAVA_HOME%替换为实际环境中JDK的安装目录，将%GWT_HOME%替换为GWT工具包的安装目录。
进入新创建的HelloWorld目录，然后运行applicationCreator命令创建GWT应用开发环境。

applicationCreator.cmd命令支持的语法如下。

`ApplicationCreator [-eclipse projectName] [-out dir] [-overwrite] [-ignore] className` 

其中最重要的一个参数是classname，也就是我们创建的GWT应用中的主Java类，我们这里选择使用`org.vivianj.gwt.client.HelloWorld。`


    C:\HelloWorld>applicationCreator.cmd org.vivianj.gwt.client.HelloWorld

根据GWT工具包的默认规则，GWT应用中的主Java类报名的最后一段必须是client。也就是说，只有xxx.client.Xxxx这样命名的Java类才能被识别为正确的GWT应用主类。
ApplicationCreator工具运行的时候，屏幕上会打印如下内容。

    Created directory C:\HelloWorld\src
    Created directory C:\HelloWorld\src\org\vivianj\gwt
    Created directory C:\HelloWorld\src\org\vivianj\gwt\client
    Created directory C:\HelloWorld\src\org\vivianj\gwt\public
    Created file C:\HelloWorld\src\org\vivianj\gwt\HelloWorld.gwt.xml
    Created file C:\HelloWorld\src\org\vivianj\gwt\public\HelloWorld.html
    Created file C:\HelloWorld\src\org\vivianj\gwt\client\HelloWorld.java
    Created file C:\HelloWorld\HelloWorld-shell.cmd
    Created file C:\HelloWorld\HelloWorld-compile.cmd

2、	开发"Hello World！"例子

使用ApplicationCreator工具的时候，ApplicationCreator工具其实已经帮我们生成了符合Hello World!例子要求的全部内容。为了不打断第一个例子的演示过程，我们先简单的了解一下ApplicationCreator工具的生成内容。我们将在将在测试过程后面做出更加详细的分析。

图1中显示了ApplicationCreator工具执行后生成的目录结构。

![](http://www.ibm.com/developerworks/cn/java/j-lo-gwt-intro/images/image003.jpg)


图1中的src\org\vivianj\gwt\client目录中的HelloWorld.java是GWT应用的主类；src\org\vivianj\gwt\public目录中的

HelloWorld.html文件是例子的默认页面；src\org\vivianj\gwt目录下的HelloWorld.gwt.xml是GWT应用的配置文件，提供GWT应用中页面和主类的配置信息；根目录下的HelloWorld-compile.cmd文件用于提供将该GWT应用编译成Ajax的命令；根目录下的HelloWorld-shell.cmd文件用于启动宿主模式(Hosted Mode)，方便测试GWT应用。

3、	在宿主模式下启动"Hello World！"例子
我们可以直接在命令行中执行HelloWorld-shell.cmd来启动宿主模式(Hosted Mode)，运行新创建的"Hello World！"例子。

    C:\HelloWorld>HelloWorld-shell.cmd

这个命令将启动两个可视化界面：Google Web Toolkit Development Shell（见图2）和GWT内置的GWT应用浏览器（见图3），GWT应用浏览器中将显示"Hello World！"例子的初始界面，如果我们点击界面上的"Click Me"按钮，按钮后面将会显示"Hello World!"字符串（见图4），如果再次单击页面上的"Click Me"按钮，按钮后面的"Hello World！"字符串会消失。

#### 图2 Google Web Toolkit Development Shell运行界面 ####

![](http://www.ibm.com/developerworks/cn/java/j-lo-gwt-intro/images/image005.jpg)

#### 图3 "Hello World！"例子初始运行界面 ####
![](http://www.ibm.com/developerworks/cn/java/j-lo-gwt-intro/images/image007.jpg)

#### 图4 "Hello World！"例子-单击"Clieck Me"按钮后的界面 ####

![](http://www.ibm.com/developerworks/cn/java/j-lo-gwt-intro/images/image009.jpghttp://www.ibm.com/developerworks/cn/java/j-lo-gwt-intro/images/image009.jpg)

### 4、	编译"Hello World！"例子 ###
要将GWT应用编译成Ajax应用，我们可以执行HelloWorld-compile.cmd。

    C:\HelloWorld>HelloWorld-compile.cmd

命令运行时，界面上将会显示下面的内容。

    Output will be written into C:\HelloWorld\www\org.vivianj.gwt.HelloWorld
    Copying all files found on public path
    Compilation succeeded

其中的第一行显示生成的Ajax应用位于C:\HelloWorld\www\org.vivianj.gwt.HelloWorld目录下。

#### 图5 "Hello World！"例子编译后的目录结构 ####

![](http://www.ibm.com/developerworks/cn/java/j-lo-gwt-intro/images/image011.jpg)

从上面的图中我们可以看到，新生成的www目录下有一个名为org.vivianj.gwt.HelloWorld的目录，它的命名规则是GWT主类全名(org.vivianj.gwt.client.HelloWorld)去掉其中的"client."。

org.vivianj.gwt.HelloWorld目录下的HelloWorld.html文件就是"Hello World！"例子对应的页面，以.cache.html后缀结尾的文件就是"Hello World！"例子中对应的Ajax代码部分，而gwt.js文件则是GWT提供的、Ajax代码中需要用到的JavaScript公共函数。其他还有些辅助文件。

#### 5、	Web模式下测试"Hello World！"例子 ####

运行HelloWorld-compile.cmd后，GWT应用就已经被编译成Ajax应用了，不再依赖于JDK和GWT环境，而仅仅依赖于浏览器。
我们打开IE浏览器，打开C:\HelloWorld\www\org.vivianj.gwt.HelloWorld\HelloWorld.html文件，就可以看到"Hello World！"例子在Web模式下的运行效果（见图6），单击页面上的"Click Me"按钮，按钮后面会出现"Hello World！"字符串（见图7），如果再次单击页面上的"Click Me"按钮，按钮后面的"Hello World！"字符串会消失。

#### 图6 Web模式下运行"HelloWorld！"例子的默认效果 ####
![](http://www.ibm.com/developerworks/cn/java/j-lo-gwt-intro/images/image013.jpg)

#### 图7 Web模式下运行"HelloWorld！"例子-单击"Click Me"按钮后的效果 ####

![](http://www.ibm.com/developerworks/cn/java/j-lo-gwt-intro/images/image015.jpg)

## 实例详细分析 ##

上面的步骤中，我们已经完成第一个GWT应用的编译和测试，下面我们来详细的介绍一下GWT应用开发过程中涉及的几个文件：显示页面HelloWorld.html、主类HelloWorld和配置文件HelloWorld.gwt.xml。

### 1.	显示页面HelloWorld.html ###

GWT应用中的HTML内容必须存放在public目录下，public目录的全路径是"org\vivianj\gwt\ublic"，是将主类HelloWorld的包名"org.vivianj.gwt.client"中的client替换为"public"后形成的目录结构。

我们将主要介绍HelloWorld.html中和GWT相关的内容，HelloWorld.html文件的全部内容见清单1。

    24.<meta name='gwt:module' content='org.vivianj.gwt.HelloWorld'>

在HelloWorld.html文件的第24行，我们使用meta标签指定了本页面和GWT模块" org.vivianj.gwt.HelloWorld"之间的联系，meta标签的name属性是不变的，而content属性则是GWT主类全名(org.vivianj.gwt.client.HelloWorld)去掉其中的"client."后的结果。

    40.	<script language="javascript" src="gwt.js"></script>

在HelloWorld.html文件的第40行，我们使用script标记引入GWT工具提供的默认函数库。根据HTML语言的规定，我们可以选择在<head></head>之间加入这行代码，也可以选择在<body></body>之间加入这段代码。GWT建议在<head></head>之间加入这行代码，以便获得稍微快一些的起动速度。

    56.	<td id="slot1"></td><td id="slot2"></td>

在HelloWorld.html文件的第56行，我们使用td标签定义了两个相邻的表格单元格，他们分别命名为"slot1"和"slot2"，根据"HelloWorld!"例子的要求，名为"slot1"的单元格中应该显示按钮，而名为"slot2"的单元格中则根据情况决定显示"HelloWorld!"字符串或者不显示任何内容。
我们将在主类HelloWorld.java的源代码中使用RootPanel.get("slot1")来获得对该单元格的引用，请参考下面的"主类HelloWorld"部分的说明。

    43.	<iframe id="__gwt_historyFrame" style="width:0;height:0;border:0"></iframe>

在HelloWorld.html的第43行，我们使用iframe标签来设置当前页支持历史功能，iframe的属性必须和上面的内容保持一致，否则将不会起到任何效果。

清单1 src\org\vivianj\gwt\public\public\HelloWorld.html

    1.	<html>
    2.		<head>
    3.		
    4.			<!--   -->
    5.			<!-- Any title is fine -->
    6.			<!--   -->
    7.			<title>Wrapper HTML for HelloWorld</title>
    8.	
    9.			<!--   -->
    10.			<!-- Use normal html, such as style-->
    11.			<!--   -->
    12.			<style>
    13.				body,td,a,div,.p{font-family:arial,sans-serif}
    14.				div,td{color:#000000}
    15.				a:link,.w,.w a:link{color:#0000cc}
    16.				a:visited{color:#551a8b}
    17.				a:active{color:#ff0000}
    18.			</style>
    19.	
    20.			<!--   -->
    21.			<!-- The module reference below is the link-->
    22.			<!-- between html and your Web Toolkit module  -->		
    23.			<!--   -->
    24.			<meta name='gwt:module' content='org.vivianj.gwt.HelloWorld'>
    25.			
    26.		</head>
    27.	
    28.		<!--   -->
    29.		<!-- The body can have arbitrary html, or  -->
    30.		<!-- you can leave the body empty if you want  -->
    31.		<!-- to create a completely dynamic ui -->
    32.		<!--   -->
    33.		<body>
    34.	
    35.			<!---->
    36.			<!-- This script is required bootstrap stuff.   -->
    37.			<!-- You can put it in the HEAD, but startup-->
    38.			<!-- is slightly faster if you include it here. -->
    39.			<!---->
    40.			<script language="javascript" src="gwt.js"></script>
    41.	
    42.			<!-- OPTIONAL: include this if you want history support -->
    43.			<iframe id="__gwt_historyFrame"
    style="width:0;height:0;border:0"></iframe>
    44.	
    45.			<h1>HelloWorld</h1>
    46.	
    47.			<p>
    48.			This is an example of a host page for the HelloWorld application. 
    49.			You can attach a Web Toolkit module to any HTML page you like, 
    50.			making it easy to add bits of AJAX functionality to existing
    pages 
    51.			without starting from scratch.
    52.			</p>
    53.	
    54.			<table align=center>
    55.				<tr>
    56.					<td id="slot1"></td><td
    id="slot2"></td>
    57.				</tr>
    58.			</table>
    59.		</body>
    60.	</html>

### 2.	主类HelloWorld ###

主类HelloWorld必须继承自com.google.gwt.core.client.EntryPoint类，并且覆盖其public void onModuleLoad()方法。
在onModuleLoad()方法中，我们使用GWT中提供的默认显示组件(比如Button)和组装组件(比如VerticalPanel)来设计需要显示的界面，所有这些组件都位于com.google.gwt.user.client.ui包下面。这些组件的命名规则和Java AWT的命名规则基本类似，使用方式也和AWT的基本一致，不过GWT中提供了不少的新组建可供选择，比如可以直接用VerticalPanel来实现垂直方向的布局管理。
HelloWorld.java中的19和20行，就声明了Button和Label两个显示组件。


    19.final Button button = new Button("Click me");
    20.final Label label = new Label();


onModuleLoad()方法中，我们需要把生成的显示组件或者组装组建加入到显示页面的对应显示区域。首先，我们需要在页面上使用id属性为显示区域指定唯一的名称(参见清单1中的56行)。com.google.gwt.user.client.ui.RootPanel的静态方法get可以根据传入的字符串参数（参数值就是显示区域的id属性）获取页面上对应的显示区域。
HelloWorld.java的36、37行，就使用了RootPanel的get方法分别获得页面上id为"slot1"、"slot2"的显示区域，然后调用add方法将生成的Button和Label对象加入到显示区域中。


    36.RootPanel.get("slot1").add(button);
    37.RootPanel.get("slot2").add(label);



Ajax最重要的功能就是为用户提供更好的交互体验，GWT中使用和AWT中相同的事件监听机制完成客户端事件监听，GWT中支持的Listener接口包括ChangeListener、ClickListener等。我们可以通过各种各样的Listener接口获得用户界面上所发生的动作，通过处理这些动作来完成对应业务逻辑。

HelloWorld.java中的第22行~29行的内容就实现了一个ClickListender接口，并且提供了对应的onClick方法实现，用于处理按钮点击后的业务逻辑（判断是否需要显示"Hello World!"字符串），然后调用Button对象的addClickListener方法增加监听器。

    22.button.addClickListener(new ClickListener() {
    23.  public void onClick(Widget sender) {
    24.if (label.getText().equals(""))
    25.  label.setText("Hello World!");
    26.else
    27.  label.setText("");
    28.  }
    29.});

HelloWorld类的全部源代码内容见清单2。

清单2 src\org\vivianj\gwt\client\HelloWorld.java

    1.	package org.vivianj.gwt.client;
    2.	
    3.	import com.google.gwt.core.client.EntryPoint;
    4.	import com.google.gwt.user.client.ui.Button;
    5.	import com.google.gwt.user.client.ui.ClickListener;
    6.	import com.google.gwt.user.client.ui.Label;
    7.	import com.google.gwt.user.client.ui.RootPanel;
    8.	import com.google.gwt.user.client.ui.Widget;
    9.	
    10.	/**
    11.	 * Entry point classes define <code>onModuleLoad()</code>.
    12.	 */
    13.	public class HelloWorld implements EntryPoint {
    14.	
    15.	  /**
    16.	   * This is the entry point method.
    17.	   */
    18.	  public void onModuleLoad() {
    19.	final Button button = new Button("Click me");
    20.	final Label label = new Label();
    21.	
    22.	button.addClickListener(new ClickListener() {
    23.	  public void onClick(Widget sender) {
    24.	if (label.getText().equals(""))
    25.	  label.setText("Hello World!");
    26.	else
    27.	  label.setText("");
    28.	  }
    29.	});
    30.	
    31.	// Assume that the host HTML has elements defined whose
    32.	// IDs are "slot1", "slot2".  In a real app, you probably would not want
    33.	// to hard-code IDs.  Instead, you could, for example, search for all 
    34.	// elements with a particular CSS class and replace them with widgets.
    35.	//
    36.	RootPanel.get("slot1").add(button);
    37.	RootPanel.get("slot2").add(label);
    38.	  }
    39.	}

#### 3.	配置文件HelloWorld.gwt.xml ####
配置文件中包括两个元素。Inherits元素地设置GWT应用的继承信息，而entry-point元素的class属性则用于设置设置GWT应用的主类。

清单3 src\org\vivianj\gwt\HelloWorld.gwt.xml

    1.	<module>
    2.	
    3.		<!-- Inherit the core Web Toolkit stuff.  -->
    4.		<inherits name='com.google.gwt.user.User'/>
    5.	
    6.		<!-- Specify the app entry point class.   -->
    7.		<entry-point class='org.vivianj.gwt.client.HelloWorld'/>
    8.	  
    9.	</module>


### 结束语 ###
Ajax是当前热门的Web应用开发技术，Java是企业应用开发中的主流技术，GWT框架将这两种技术合二为一，是我们能够应用Java语言来开发Ajax，在保留Ajax技术的优点基础上，解决了Ajax应用开发、调试困难的缺点。
本文中介绍了GWT的基本知识、主要特性，提供了"HelloWorld！"例子说明使用Java和GWT框架开发Ajax应用的开发、编译、测试过程，并且通过对"HelloWorld！"例子中涉及文件的分析，讲解了使用GWT框架开发Ajax应用的技术细节和约束条件。


### 下载资源 ###
JDK下载地址：[ http://java.sun.com]( http://java.sun.com)

### 参考资料 ###
学习
GWT 在线资源： [http://code.google.com/webtoolkit/gettingstarted.html ](http://code.google.com/webtoolkit/gettingstarted.html )

developerWorks 网站 Ajax 技术资源中心：[http://www.ibm.com/developerworks/cn/xml/ajax/。 ](http://www.ibm.com/developerworks/cn/xml/ajax/。 )

获得产品和技术
JDK 下载地址：[http://java.sun.com ](http://java.sun.com )

GWT 下载地址：[http://code.google.com/webtoolkit/ ](http://code.google.com/webtoolkit/ )

讨论
GWT 在线FAQ：[http://code.google.com/webtoolkit/faq.html ](http://code.google.com/webtoolkit/faq.html )

GWT 开发者论坛：[http://groups.google.com/group/Google-Web-Toolkit。 ](http://groups.google.com/group/Google-Web-Toolkit。 )

参考地址：[http://www.ibm.com/developerworks/cn/java/j-lo-gwt-intro/](http://www.ibm.com/developerworks/cn/java/j-lo-gwt-intro/)