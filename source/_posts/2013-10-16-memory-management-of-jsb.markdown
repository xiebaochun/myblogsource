---
layout: post
title: "JSB内存管理（Memory Management of JSB）"
date: 2013-10-16 15:23
comments: true
categories: ios
---
原文地址：[http://www.cocos2d-x.org/wiki/Memory_Management_of_JSB](http://www.cocos2d-x.org/wiki/Memory_Management_of_JSB)

译者：晋文格墨 

注：因译者水平有限，如有错误，请批评指正。

## JSB的内存管理 ##

基于Cocos2d-x 2.15,但同样适用于Cocos2d-x 3.0。

### JSB对象的生命周期 ###

总所周知，javascript有自己的内存管理机制，即垃圾回收。Cocos2d-x模拟垃圾回收系统来管理Cocos对象。但这里有一个问题，就是将Cocos2d-x对象绑定到javascript对象时由谁负责内存管理。
<!--more-->
先看一个案例。

### 通过XXX.create()来分配对象内存 ###

下面的代码分配了一个全局变量。

    gnode=cc.Node.create();

gnode并没有通过addChild()添加到其它cc.Node中。

在菜单项的回调函数中添加如下代码：

    //meuItem callback
    onButton:function(sender){
    sender.addChild(gnode);
    }

当点击此按钮，你将会看到如下错误信息：

    Cocos2d: jsb: ERROR: File /Users/u0u0/Documents/project/SK_parkour/scripting/javascript/bindings/generated/jsb_cocos2dx_auto.cpp: Line: 3010, Function: js_cocos2dx_CCNode_addChild
    Cocos2d: Invalid Native Object

发生了什么！“Invalid Native Object”是什么意思？

在javascript中gnode是一个全局变量，意味着它不能被回收。

但是gnode里的CCNode会被Cocos2d-x回收。

为了弄清楚这个问题，你需要了解一下spidermonkey并深入研究javascript的绑定代码。

cc.Node.create()的内部实现

详细的实现代码如下：

    static JSFunctionSpec st_funcs[] = {
    JS_FN("create", js_cocos2dx_CCNode_create, 0, JSPROP_PERMANENT | JSPROP_ENUMERATE),
    JS_FS_END
    };
    
    jsb_CCNode_prototype = JS_InitClass(
    cx, global,
    NULL, // parent proto
    jsb_CCNode_class,
    js_cocos2dx_CCNode_constructor, 0, // constructor
    properties,
    funcs,
    NULL, // no static properties
    st_funcs);

cc.Node.create()被对应的C函数是js_cocos2dx_CCNode_create()，如下：

    JSBool js_cocos2dx_CCNode_create(JSContext *cx, uint32_t argc, jsval *vp)
    {
    if (argc == 0) {
    cocos2d::CCNode* ret = cocos2d::CCNode::create();
    jsval jsret;
    do {
    if (ret) {
    js_proxy_t *proxy = js_get_or_create_proxy<cocos2d::CCNode>(cx, ret);
    jsret = OBJECT_TO_JSVAL(proxy->obj);
    } else {
    jsret = JSVAL_NULL;
    }
    } while (0);
    JS_SET_RVAL(cx, vp, jsret);
    return JS_TRUE;
    }
    JS_ReportError(cx, "wrong number of arguments");
    return JS_FALSE;
    }

通过cocos2d:CCNode::create()成功分配的对象将会被封装成js_get_or_create_proxy()创建的一个新对象js_proxy_t。

在js_get_or_create_proxy()函数里，只要关注下面这行代码：

    JS_AddObjectRoot(cx, &proxy->obj);

这段代码将一个JSObject添加到垃圾回收器的根集合中的spidermonkey api。proxy->obj是对应javascript里的一个JSObject。

所以通过cc.Node.create()分配的对象将会一直保留在内存中，直到调用JS_RemoveObjectRoot()。

但是cocos2d::CCNode::create()是一个自动释放对象，它会在下一个游戏画面被Cocos2d-x回收。

CCobject的析构函数将会被调用，请注意下面的代码：

    // if the object is referenced by Lua engine, remove it
    if (m_nLuaID)
    {
    CCScriptEngineManager::sharedManager()->getScriptEngine()->removeScriptObjectByCCObject(this);
    }
    else
    {
    CCScriptEngineProtocol* pEngine = CCScriptEngineManager::sharedManager()->getScriptEngine();
    if (pEngine != NULL && pEngine->getScriptType() == kScriptTypeJavascript)
    {
    pEngine->removeScriptObjectByCCObject(this);
    }
    }

pEngine->removeScriptObjectByCCObject做了一件神奇的事情。

    void ScriptingCore::removeScriptObjectByCCObject(CCObject* pObj)
    {
    js_proxy_t* nproxy;
    js_proxy_t* jsproxy;
    void *ptr = (void*)pObj;
    nproxy = jsb_get_native_proxy(ptr);
    if (nproxy) {
    JSContext *cx = ScriptingCore::getInstance()->getGlobalContext();
    jsproxy = jsb_get_js_proxy(nproxy->obj);
    JS_RemoveObjectRoot(cx, &jsproxy->obj);
    jsb_remove_proxy(nproxy, jsproxy);
    }
    }

JS_RemoveObjectRoot函数将JSobject从javascript根集合中移除。 jsb_remove_proxy将proxy(委托）从hash表中移除。

现在我们可以解释本文开始提出的问题了。

### Cocos2d-x的垃圾回收系统负责内存管理 ###

回到gnode,它是一个全局变量。CCObject的析构函数JS_RemoveObjectRoot的作用只是平衡JS_AddObjectRoot的创建。Spidermonkey将不会回收这个全局变量，但是gnode的本地对象将会被释放。访问gnode的本地对象将会产生之前看到的那个错误。

### 通过new分配对象 ###

思考一下下面的代码:

    gnode=new cc.Node;

为了找到正确答案，同样需要深入研究JSB代码。

如之前提到的，cc.Node的构造函数是js_cocos2dx_CCNode_constructor()。

请注意下面的代码：

    if (argc == 0) {
    cocos2d::CCNode* cobj = new cocos2d::CCNode();
    cocos2d::CCObject *_ccobj = dynamic_cast<cocos2d::CCObject *>(cobj);
    if (_ccobj) {
    _ccobj->autorelease();
    }

本地对象被压入到Cocos2d-x的自动释放池中。所以new和create()是一样的。

### 关于retain()和release() ###

有两个函数可以用于手动控制对象的生命周期。如果你想避免之前例子中产生的错误。

你可以有以下两个选择：

1.将gnode添加到其它的CCNode中，addChild()将gnode保留在内部。

2.在create()之后立即调用gnode.retain()。

在第二种情况下，你需要在合适的时候调用gnode.release()以防止内存泄漏。下一节将会介绍它。

### ctor()和onExit() ###

Cocos2d-x JSB使用[Simple JavaScript Inheritance By John Resig](http://api.viglink.com/api/click?format=go&key=9b4efad421c8b103b2c94b796db973b0&loc=http%3A%2F%2Fwww.cocos2d-x.org%2Fwiki%2FMemory_Management_of_JSB&subId=d654e26afef9085d79aba37dfe97be28&v=1&libId=0b3cce4b-8d39-40a9-a3f9-62d2885b09d0&out=http%3A%2F%2Fejohn.org%2Fblog%2Fsimple-javascript-inheritance%2F&title=Cocos2d-x%20%7C%20Memory%20Management%20of%20JSB&txt=Simple%20JavaScript%20Inheritance%20By%20John%20Resig&jsonp=vglnk_jsonp_138191400851911)。但是构造函数的名字不一样。

在JSB中，ctor是构造函数。对应的onExit则扮演的是析构函数，它会在CCNode释放之前被调用。

下面的例子演示了如何手动控制JSB对象的生命周期。

    var container = cc.Node.extend({
    ctor:function () {
    this._super();
    this.gnode = cc.Node.create();
    this.gnode.retain();
    },
    onExit:function() {
    this.gnode.release();
    this._super();
    },
    });

