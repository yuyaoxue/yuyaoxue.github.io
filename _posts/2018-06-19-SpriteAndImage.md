---
layout: post
title:  "Unity Sprite 与 Image 有什么区别？"
date:   2018-06-19 1.20 +0800
categories: jekyll update
---
# 前言

最近在做一个 2D 游戏，用到了 Sprite 与 Image。UI 界面使用 Image ,游戏场景相关制作使用 Sprite。后来跟小伙伴交流的时候，提到了这一点，说到具体原理，我记得是渲染方式不同，Image 的渲染方式是 Canvas Renderer；Sprite 的渲染方式是 Sprite Renderer，至于具体为什么要使用，一知半解。

## 异同

1.都是使用一个 Sprite 源进行渲染，而 Image 需要位于 Canvas 下才能显示出来。

2.场景中的 Sprite 可以像普通的 3D 游戏物体一样处理，通过 Transform 组件进行移动，旋转等操作；而 Image 则使用 RectTransform 进行布局，以便通过 Canvas 统一管理，RectTransform 可以设置大小，对齐方式等。

3.同一 Canvas 下 不同的 Image 的渲染顺序是根据 Image 在 Hierarchy 的上下顺序渲染的，上面的先被渲染，下面的后被渲染；不同的 Sprite 是根据 Order in Layer 数值的大小渲染的，数值小的先被渲染，数值大的后被渲染。

4.Image 和 Sprite 都可以引用 Material; 都可以通过 Color 修改颜色；如果想对图片进行拉伸，而不影响图片的显示， Sprite 可以通过 DrawMode 修改渲染模式，Image 则是通过 Image Type 修改 Image 的显示方式。

![Sprite Inspector](https://raw.githubusercontent.com/yuyaoxue/yuyaoxue.github.io/master/assets/_v_images/sprite_1.png)

Sprite Inspector

![Image Inspector](https://github.com/yuyaoxue/yuyaoxue.github.io/blob/master/assets/_v_images/sprite_2.png?raw=true)

Image Inspector

5.不同的屏幕分辨率下，Image 可能会 Canvas 改变显示位置和实际大小。就做的 2D 项目来说，我一开始用 Image 显示游戏内元素，需要调整的东西很多，比如 不同游戏元素的相对 Scale ，自身 Position 等，使用 Sprite 的话，根据原图设置的Pixels Per Unit 的值 修改  Sprite 的 Camera 自身属性的 Orthgraphic 的 Size 就好。

6.渲染上， Sprite 使用 SpriteRenderer 组件渲染，而 Image 则由 CanvasRenderer 组件渲染，在视觉上没有任何区别。默认的渲染也都是在 Transparent Geometry 队列中。
而在引擎的处理上，两者则有很大的不同。将 Wireframe 选项打开然后在场景中观察，就可以清楚地发现，Image 会为一个矩形的 Sprite 生成两个三角形拼成的矩形几何体，而 Sprite 则会根据显示内容，裁剪掉元素中的大部分透明区域，最终生成的几何体可能会有比较复杂的顶点结构。

## 如何选择使用

在 2D 游戏开发中，游戏场景中的元素，应该尽量使用 Sprite；而 Image 应该仅用于 UI 显示，这样调整起来比较简单。还有一个我觉得比较实际的问题，就是 如果目前 2D 游戏升级为成 3D 游戏，相对来说修改的比较少一些。

## 结束语

Sprite 与 Image 显示方面可能相差不大，如果游戏元素多的话，在性能方面就会有很大的差距，之后测试去做进一步验证。