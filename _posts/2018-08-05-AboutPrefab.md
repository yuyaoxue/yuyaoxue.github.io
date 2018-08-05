---
layout: post
title:  "有关于 .prefab 与自身挂载组件的关系"
date:   2018-08-05 20:00 +0800
---
# 问题

在 UI Panel 添加了一个粒子特效，打开该 Panel 时，特效不显示，选中特效时，特效又会显示出来。

## 测试

同事提醒可能跟特效的父级的 Scale 有关系。经过测试，确实跟 Canvas 的 Scale 有关系。因为我们项目 UI 框架设计的原因，将 UIPanel 制作成 prefab 时，将 UIPanel 的 Active 设置为 false；在 UIPanel 在加载成功时，Canvas 的 Scale 为 0，设置 UIPanel.SetActive(true) 之后，Canvas 的 Scale 为 1。

## 测试 Canvas 的 Scale 为 0 的原因

经过测试，Canvas 的 Scale 为 0 的原因跟 Canvas 的 Render Mode 有关系：

Canvas 的 Render Mode 设置不同，对于 RectTransform 组件上的值是有影响的。

当 Renderer Mode 为 Screen Space - Camera 或 Screen Space -Overlay 时 Canvas 的 Scale 会为 0 ，如果为 World Space 时，Scale 就会变为 1。

![Screen Space - Camera](https://github.com/yuyaoxue/yuyaoxue.github.io/blob/master/assets/_v_images/Image1.png?raw=true)  ![Screen Space - Overlay](https://github.com/yuyaoxue/yuyaoxue.github.io/blob/master/assets/_v_images/Image2.png?raw=true)  ![World Space](https://github.com/yuyaoxue/yuyaoxue.github.io/blob/master/assets/_v_images/Image3.png?raw=true)

以下是预制体在 Assets 文件下查看 Prefab 的 Canvas 的参数。

![Screen Space - Camera](https://github.com/yuyaoxue/yuyaoxue.github.io/blob/master/assets/_v_images/Image4.png?raw=true)  ![Screen Space - Overlay](https://github.com/yuyaoxue/yuyaoxue.github.io/blob/master/assets/_v_images/Image5.png?raw=true)  ![World Space](https://github.com/yuyaoxue/yuyaoxue.github.io/blob/master/assets/_v_images/Image6.png?raw=true)

在 Hierarchy 上点击 UI Panel 查看 Canvas 的 Inspector， Render Mode 为 Screen Space-Camera 或 Screen Space - Overlay 的时候，RectTransform 组件上的值被锁住了，是通过 Canvas 脚本里面计算出来的；而 Render Mode 为 World Space 的时候，RectTransform 组件上的值代表的是 Canvas 的世界坐标，可以进行修改。

.prefab 文件是一种模板，存储着各种组件。将 .prefab 文件拖到 Hierarchy 里面，实际上是在场景中创建了一个新对象实例，这时候，挂载的组件才会起到作用，比如：Canvas。

## .prefab 与自身挂载组件的关系

.prefab 文件在 unity 引擎中，扮演着一种配置文件一样的作用，用它将多个组件关联在了一起，组成了一个整体。当 .prefab文件被实例化到场景上，并且组件被激活后，才能起到作用。

.prefab 文件默认是一个二进制文件，我们是无法观察它里面的信息的。

可以通过一些设置，然后通过文本编辑器打开就可以看到里面的内容。[.prefab 转换为 txt 格式](https://blog.csdn.net/WPAPA/article/details/51560288)

以下是通过文本编辑器打开 UI.prefab，UI2.prefab。

![prefabEditor1](https://github.com/yuyaoxue/yuyaoxue.github.io/blob/master/assets/_v_images/prefabEditor1.png?raw=true),![prefabEditor2](https://github.com/yuyaoxue/yuyaoxue.github.io/blob/master/assets/_v_images/prefabEditor2.png?raw=true)

## 思考

在解决问题的过程中，发现一些现象，不要单纯地从表面去观察问题；而是多问自己几个为什么，透过现象，发现问题的本质。

参考资料：[Canvas Scaler](https://docs.unity3d.com/Manual/script-CanvasScaler.html)，[Canvas Render](https://docs.unity3d.com/Manual/class-Canvas.html)， [Prefab](https://docs.unity3d.com/Manual/Prefabs.html)
