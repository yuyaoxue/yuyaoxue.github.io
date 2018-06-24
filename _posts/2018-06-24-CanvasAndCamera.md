---
layout: post
title:  "Unity API 之 Canvas 与 Camera"
date:   2018-06-24 1.20 +0800
categories: jekyll update
---
# 前言

最近在做一个 2D 游戏，项目做的差不多了，整理一下自己在制作过程中用到的知识点，这篇写 Canvas 与 Camera 的使用。

## Canvas

画布是所有 UI 元素都应该在其中的区域。Canvas 是一个带有画布组件的游戏对象，所有 UI 元素都必须是此类画布的子元素。下面是 Canvas 组件

![Canvas](https://github.com/yuyaoxue/yuyaoxue.github.io/blob/master/assets/_v_images/canvas.png?raw=true)

Canvas 有以下三种渲染模式：

1.Screen Space Camera

此模式画布被放置在指定的摄像机前的距离。UI 元素由这个摄像头呈现，这意味着摄像头的设置会影响 UI 的外观。如果将相机设置为透视，UI 元素将以透视的方式呈现，视角的失真程度可以通过相机的视角来控制。如果屏幕被调整了大小，改变了分辨率，或者相机的截屏改变了，画布也会自动地改变大小以匹配。

tip：如果想在 UI 上显示特效，可以使用此渲染模式

2.Screen Space Overlay

此模式将 UI 元素放置在场景顶部呈现的屏幕上。如果屏幕被调整大小或改变分辨率，画布将自动改变大小以匹配它。

tip：如果想在 UI 上显示特效 ，此模式不适用。

3.World Space

在此模式中，画布将表现为场景中的任何其他对象。画布的大小可以通过它的 Rect 变换来手动设置，UI 元素会基于 3D 布局呈现在场景中的其他对象的前面或后面。这对于注定要成为世界一部分的 UI 是有用的。

UI 适配

Canvas Scaler

对于设置为“Screen Space - Overlay” or “Screen Space - Camera”的画布，可以将画布比例器 UI 比例模式设置为 Constant Pixel Size, Scale With Screen Size, or Constant Physical Size。

Constant Pixel Size

UI 元素的位置和大小以屏幕上的像素为单位指定。这也是画布的默认功能，当不附带画布标量时。但是，在画布标量中设置了比例因子设置之后，可以将常量扩展应用到画布中的所有 UI 元素。

Scale With Screen Size

可以根据指定参考分辨率的像素指定位置和大小。如果当前的屏幕分辨率大于参考分辨率，画布将只保留参考分辨率的分辨率，但将按比例放大以适应屏幕。如果当前屏幕分辨率小于参考分辨率，那么画布也会相应地缩小以适应。

如果当前屏幕分辨率与参考分辨率有不同的长宽比，分别缩放每个轴以适应屏幕将导致非均匀缩放，这通常是不可取的。相反，ReferenceResolution 组件将使画布分辨率偏离参考分辨率，以尊重屏幕的纵横比。可以使用屏幕匹配模式设置来控制这种偏差的行为。

UI 适配 可以设置此缩放模式

Constant Physical Size

UI 元素的位置和大小在物理单元(如毫米、点或 picas )中指定。此模式依赖于正确报告屏幕DPI 的设备。您可以指定一个 fallback DPI 用于不报告 DPI 的设备

## Camera

相机是用来捕捉和显示世界给玩家的设备。通过定制和操作摄像头，你可以使你的游戏呈现真正独特。你可以在一个场景中拥有无限数量的相机。它们可以被设置成任何顺序，在屏幕上的任何位置，或只有屏幕的某些部分，下面是 Camera 组件。

![Camera](https://github.com/yuyaoxue/yuyaoxue.github.io/blob/master/assets/_v_images/camera.png?raw=true)

上面的 Canvas 里面有提到当 Canvas 的渲染模式选择为 Screen Space Camera 时，它所引用的 Camera 的投影设置会影响到 Canvas 里 UI 元素的显示。

Projection 包括 Perspective，Orthographic 两个投射方式

Perspective：透视

摄像机将用透视的方式来渲染游戏对象。

如果设置为 Perspective，还需要设置一个参数 Field of view (视野范围，用于控制摄像机的视角宽度以及纵向的角度尺寸。

Orthographic：正交

摄像机将用无透视的方式来渲染游戏对象。
注意：在 Orthographic 模式下不支持延迟渲染。总是使用前向渲染。

把相机标记为 Orthographic 会从相机的视图中移除所有的视角。这对于制作等距或 2D 游戏非常有用。

如果设置为 Orthographic，还需要设置一个参数 Size（用于控制正交模式摄像机的视口大小）。

这个大小的设置会根据 UI 素材的尺寸大小以及设置的 Pixels Per Unit 值有关系，一般默认为 100。也就是 288x512 的图片在 Unity 里大小是 2.88x5.12。如果想让这个图片显示全屏，Size 设置为 2.56。

以下是 Unity 官方文档传送门：

[Camera](https://docs.unity3d.com/Manual/class-Camera.html)

[Canvas](https://docs.unity3d.com/Manual/UICanvas.html)

[Canvas Scaler](https://docs.unity3d.com/Manual/script-CanvasScaler.html)
