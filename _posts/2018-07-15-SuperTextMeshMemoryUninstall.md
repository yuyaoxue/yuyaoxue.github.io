---
layout: post
title:  "SuperTextMesh MemoryUninstall Bug"
date:   2018-07-15 22:00 +0800
---
# 前言

前一段时间将 SuperTextMesh 导入到项目中，实现图文混排。最近使用 SuperTextMesh 来实现炫酷的文字特效。在使用过程中，发现了 SuperTextMesh  内存卸载的 Bug。从查找问题到解决断断续续花了有一周的时间，现在问题解决了，写一个总结记录下。

## 问题描述

项目中，在游戏界面上有使用 SuperTextMesh 来实现文字特效，在网络断开的时候文字会突然消失。经过调试，发现了网络断开的时候，代码在调用了 Resources.UnloadUnusedAssets() ，之后 SuperTextMesh 的文字就消失了。测试将那行代码注释掉，文字就不消失了。于是就沿着这个方向开始查找。

## 问题复现

为了验证 Bug 确实是资源卸载引起的，重新创建了一个全新的项目，将插件导入空项目中。
在场景中，创建 SuperTextMesh，Button；给 Button 添加点击事件，事件里调用Resources.UnloadUnusedAssets()；开始运行，文字显示；点击按钮 Button 文字消失。

## 找出原因

### 猜想一

当时还未锁定是资源卸载的问题的时候，排查方向是文字的参数发生了变化。

之前美术同事在做特效的时候，也出现过类似的问题，也会消失；然后选中该特效又能显示出来。后来找出的问题是：在过程中，有父集的 Scale 为 0 导致没有显示。所不同的是，我的一开始是显示的，只是后面消失了。

我根据同事的解决办法去检测下，SuperTextMesh 消失后他的 Scale 的情况，一并检测了其他会影响消失的参数，都没有找到问题所在，所以排除这个猜想。

之后的猜想都是关于资源卸载后，将使用中的 Text 里面的资源卸载掉的原因。

### 猜想二

[Resources.UnloadUnusedAssets()](https://docs.unity3d.com/ScriptReference/Resources.UnloadUnusedAssets.html)；会卸载掉不被引用的 assets，查代码找到 SuperTextmMeshData 的一个引用是通过 Resources.load() 加载资源赋值的，后来看到 data 是一个私有静态成员变量，Resources.UnloadUnusedAssets() 不可能会卸载掉他（静态成员变量的引用一直会在）。

### 猜想三

问题出现的时候，在 FrameDebug 中 UGUI.Rendering.RenderOverlays 里所对应的 DrawMesh 也消失了，所以猜想 mesh 可能丢失。经过调试，mesh 数据一直在，没有丢失，排除这个猜想。

### 猜想四

影响渲染的除了 Mesh，另一个是 Material；猜想 Material 可能丢失。这个是同事帮我调试出来的，发现了 Material 为 Null。后来我自己也调试了一下，文字消失的时候，通过打印，可以看到 Material 为 Null 最后得出原因是 Material 丢失。

## 解决方案

### 尝试解决

想解决 Material 为 Null 这个问题，目前看起来比之前的要容易。就是在 Update() 里检测出 Material 为 Null 时,重新设置一下就可以，但是没有从根本上解决。

我还没找到为什么 Material 会消失。没有主动调用清理，那还有什么原因导致 Material 为 Null 呢。

在随后的排查中，我找到了存放 Material 的数据变量 newMats 发现他是一个局部变量，想修改为私有成员变量，这样就能看到这个值在问题出现的问题是否为 Null 了。结果怎么都复现不了问题了，一开始我还很诧异，怎么不复现了，后来回过神来，是我修改为成员变量的缘故，后来反复修改测试证实了我的猜想。随后查了成员变量和局部变量的作用域验证了我的想法。

目前，我锁定 Material 丢失的原因是存储 Material 的变量 newMats 的生命周期比 SuperTextMesh 的生命周期短，导致在调用 Resources.UnloadUnusedAssets() 的时候，被标记为没有引用的内存清理掉。我的解决方法是将 ApplyMaterial() 的局部变量 newMats 为成员变量 ，以保证 newMats 的生命周期和 SuperTextMesh 的生命周期是一样的。

### 最终方案

将存放  Material 的局部变量 newMats 修改为私有成员变量。

## 思考与总结

解决问题的方法很简单，在找解决方法的过程中，比较煎熬。一开始，我在解决这个问题上表现出来的态度是以最快的速度解决问题，而不是找到问题的原因然后从根本上解决，所以反复好几次处理这个问题，浪费了好多时间。同事说：『 解决问题的态度决定你会走多远 』，要逐渐纠正自己处理问题的态度。另外，在说一个结论时，不要说我以为是如何如何；而是以事实为依托，来证实自己的结论。最后在同事的鼓励帮助下，找到根本原因并解决了问题。而且也跟 SuperTextMesh 的作者进行过邮件沟通，相信下一版本会修复这个 Bug。

## 疑问

上面造成问题的原因是 局部变量 newMats 在 ApplyMaterial() 函数执行完之后失效，在调用 Resources.UnloadUnusedAssets() 时，被标记为没有引用的内存清理掉。这种情况，让我想到了 [GC 的内存回收机制](https://msdn.microsoft.com/en-us/library/xe0c2357(v=vs.110).aspx)。后来，我用 GC.Collect() 代替 Resources.UnloadUnusedAssets() 后，问题没有复现。猜测他们的回收对象可能不一样。

查到了一篇关于 [内存泄漏](https://testerhome.com/topics/5780/show_wechat) 的博客，里有提到他们的区别

     Mono 内存是通过 GC 来回收的，而 Unity 也提供了一种类似的方式来回收内存。不同的是，Unity 的内存回收是需要主动触发的。就好比说，我们把垃圾扔在门口的垃圾桶里，GC 是每天来看一次，有垃圾就收走；而 Unity 则需要你打个电话给它，通知它有垃圾要回收，它才会来。主动调用的接口是 Resources.UnloadUnusedAssets()。其实 GC 也提供了同样的接口 GC.Collect() 用来主动触发垃圾回收，这两个接口都需要很大的计算量，我们不建议在游戏运行时时不时主动调用一番，一般来说，为了避免游戏卡顿，建议在加载环节来处理垃圾回收的操作。有一点需要说明的是，Resources.UnloadUnusedAssets() 内部本身就会调用 GC.Collect()。

