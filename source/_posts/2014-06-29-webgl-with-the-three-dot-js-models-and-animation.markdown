---
layout: post
title: "webgl之three.js模型和动画"
date: 2014-06-29 21:22
comments: true
categories: web
---
自从3d图形被引入浏览器，浏览器中的3D图形开发就变成一个热门的话题。但是如果你直接使用原始的webgl api来开发应用将会很费劲。因此诞生了很多与之相关的类库。three.js是最流行的一个，在此文中我将介绍它。

[在线演示](http://xiebaochun.github.io/mylab/webgl/webgl-models-animation/final/) [源码下载](https://github.com/xiebaochun/webgl-models-animation)

在开始本文之前，我默认你已经具有了3D空间的基本知识，比如坐标和向量等。
<!--more-->

## 准备: ##
本文将在之前的代码基础上开始。下载并解压我提供的资源，你就可以开始了。

## step1:在blender中导出模型 ##
在blender中导出three.js支持的模型，你需要注意以下几点：

**- 首先，remove the parenting。**three.js导出插件不会导出任何你留下的动画（还有Armature Modifier）

**- 其次，群组顶点。**如想让骨骼移动移动时，周围的顶点也随之移动，那么就必须将他们群组起来，并将组的名字命名为骨骼的名字。

**- 第三，可以只有一个动画。**这可能是一个大问题，但是之后我会解释。

同样，当你导出时，在导出器中确保一下这些选项都勾选了：skinning,Bones和Skeletal Animation(骨骼动画）。

## step2:导入模型 ##

导入模型非常的简单。这里有一个特殊类，即THREE.JSONLoader（他会为我们处理所有的导入相关工作。当然，它只能导入JSON格式的models,但是three.js建议使用他们，所以我将只介绍这个loader（其他的loader也是一样的）。让我们首先初始化它:

    var loader=new THREE.JSONLoader;
    var animation;

这里不需要参数。我们需要为动画定义一个变量，这样我们可以在后面访问它。现在我们可以加载模型了：

    loader.load('./model.js',function(geometry,materials){
    var skinnedMesh=new THREE.SkinnedMesh(geometry,new THREE.MeshFaceMaterial(materials));
    skinnedMesh.positon.y=50;
    skinnedMesh.scale.set(15,15,15);
    scene.add(skinnedMesh);
    
    animate(skinnedMesh);
    });

此load方法接受两个参数：一个是model的path,还有一个是回调函数。这个回调函数在导入model完成后被调用（你可以在此之前显示一个加载进度条给用户）。这个回调函数调用时将会传入两个参数：一个是model的几何体和它的材质（随model一起导出来的）。在这个回调函数中，我们创建了mesh。但是这次的是THREE.SkinnedMesh，它支持动画。

接下来，我们将model往上移动50个单位，置于立方体的顶端，将其放大15倍（因为它很小，在blender制作中），并将其添加到场景中。接下来我们调用aimate函数，它会配置并运行动画。


## step3:动画 ##

现在我们配置动画。下面是animate函数的原型：

    function animate(skinnedMesh){
      var mterials=skinnedMesh.material.materials;
    
      for(var k in materials){
     materials[k].skinning=true;
    }
    
     THREE.AnimationHeander.add(skinnedMesh.geometry.animation);
      animation=new THREE.Animation(skinnedMesh,"ArmatureAction",THREE.AnimationHander.CATMULLROM);
      animation.play();
    }

首先我们必须开启model中所有部件的蒙皮动画。接下来，必须将model中的动画（animation）添加到THREE.AnimationHandler并创建THREE.Animation对象。参数依次为：mesh（动画网格），model中的动画名称和插值类型（在像人这种复杂模型中很有用，可以使网格平滑过渡）。最后，我们执行动画。

但是现在打开浏览器的话，你会发现模型并没有动。

![](https://cdn.tutsplus.com/net/uploads/2013/12/model_still.png)


为此，我们需要添加一行代码到render函数中，就是位于particleSystem旋转的下面：


    if(animation) animation.update(delta);

这样就可以定时更新动画了，因为THREE.AnimationHandler知道去渲染那一帧。现在打开浏览器，你将会看到顶部的立方体左右弯曲摆动。

![](https://cdn.tutsplus.com/net/uploads/2013/12/model_animated.png)

## step4:多个动画 ##

现在model中只有一个动画队列，现在我们想将每个动画添加到一个队列中，当一个动画结束，另一个立马接着开始执行。接下来,在导出model后，需要修改动画代码。其中1-3秒为站立动画，从第三秒到最后为行走动画。然后在render函数中检查每一秒的动画名字，如果到了当前队列的最后时间，将停止动画并从新开始。

    var currentSequence='standing';
    
    function(render){
    ...
       if(animation) animation.update(delta);
       if(currentSequence=='standing'){
      if(animation.currentTime>4){
     animation.stop();
     animation.play(false,0);//play the animation not looped,from 0 0s
       }
     }else if(currentSequence=='walking'){
      if(animation.currentTime<=4||aniamtion.currentTime>8){
      animation.stop();
      animation.play(false,4);//play the animation not looped,from 4s
     }
      }
      ...
     }

在此，在正确的时间以非循环模式执行动画。当然，如果用户的帧率真的低的话，会出现怪异的现象，因为delta会更高，以致animation.cuurentTime可能比特定队列的限制时间大，从而会执行下一队列的部分动画。但是只要delta在300-500m之间，问题是不会太大的。

现在更改animate函数来执行行走动画，只要添加如下参数至animation.play中即可：


    animation.play(false,0);

我们也可以让用户通过按键a来切换动画。在文件的底部添加如下代码，只要在render()调用之前。

    document.addEventListenner('keyup',function(e){
      if(e.keyCode=="A'.charCodeAt(0)){
     currentSequece==(currentSequece=='standing'?'walking':'standing');
       }
    });


## step5：Attach to bone（依附骨骼） ##

此项技术非常适合RPG game(角色扮演游戏），但是也使用于其他一般的游戏。这涉及到将其他对象依附到骨骼动画对象中，比如衣服，武器，等。

让我们以loader.load回调函数开始吧，添加如下代码至scene.add（skinnedMesh）下面：

    item=new THREE.Mesh(new THREE.CubeGeometry(100,10,10),new THREE.MeshBasicMaterial({color:0xff0000}));
    item.position.x=50;
    pivot=new THREE.Object3D();
    pivot.scale.set(0.15,0.15,0.15);
    pivot=add(item);
    pivot.useQuaternion=true;
    skinnedMesh.add(pivot);

此item既是要模拟依附到动画对象中的一个物件。让他围绕着一个特定的点旋转，但是不是中心点，我们将为它添加一个pivot对象，并将其向右移动50个单位。并缩小至0.15倍，因为我么要将其添加到已经放大了15倍的skinnedMesh中。最后在将其添加至我们的动画中之前，我们命令它使用quaternions(四元数，矩阵）。

通常，四元数是一个数字系统，但是Three.js已经强大到不用我们去关心这些底层的数据了。如果你感兴趣的话可以google或者aol，但千万别度娘。这些四元数是用来旋转物件（也是对象），而不会出现gimbal lock的危险。

现在在render函数中，更新对象的位置和旋转：

    pivot.position=new THREE.Vector3().getPositionFromMatrix(skinnedMesh.bones[2].skinnedMatrix);
    pivot.quaternion.setFromRotationMatrix(skinnedMesh.bones[2].skinnedMatrix);

在这里，首先我们将pivot的位置设为model最后一个骨骼的相同位置。我们使用skinnedMatrix属性来计算它。然后我们使用同样的属性来为pivot计算旋转矩阵。完之，打开浏览器，将会看见一个红色的物件（如下）依附到了我们的模型：


![](https://cdn.tutsplus.com/net/uploads/2013/12/attach_to_bone.png)

## 总结： ##

我希望你能在此文中能获得想要的知识点。文中代码工程可以免费下载并使用。下一次，我们将会介绍OpenGL/webGL的shader,即显卡编程。

这里提供一下3d模型下载的网址：
[http://tf3dm.com/](http://tf3dm.com/)
[http://www.turbosquid.com/Search/3D-Models/free/fbx](http://www.turbosquid.com/Search/3D-Models/free/fbx)
[http://free.cgtrader.com/](http://free.cgtrader.com/)



[http://code.tutsplus.com/tutorials/webgl-with-threejs-models-and-animation--net-35993](http://code.tutsplus.com/tutorials/webgl-with-threejs-models-and-animation--net-35993)





