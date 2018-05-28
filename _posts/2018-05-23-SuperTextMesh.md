---
layout: post
title:  "图文混排总结"
date:   2018-05-23 18:02:01 +0800
categories: jekyll update
tags: SuperTextMesh
---
# 图文混排总结

最近在做图文混排相关的功能，先后使用了几种方式实现；有的本身有问题，有的不适用与我们的项目， 总结一下以后略过这些坑。

## 第一种：项目里的 TextWithImage (搁置)

Text 中加载个 Image 是没有问题，加载1个以上，就会报错(错误位置是 TextWithImage 里 99 行)，将多余的 Image 的 enable 设置为 false，不能进行设置，删除等操作，还会出现加载一个 Image 创建出 2 个， 加载 2 个，创建出 3 个...翻倍的创建 Image，消耗性能。

## 第二种是：TextInlineSprite （text中插入图片）（搁置）

   1：总体显示方面都还可以 (可以设置 Image 显示大小，支持动态图）

   2：显示 Bug（表情会出现丢失的情况，关掉界面再打开就会显示）

   3: 需要手动清理画布上的表情 list 显示(如果是分页显示的话，切换表情时，需要把前一个 Image 内同清理掉)

   4:最严重的问题时分辨率适配问题(不同比例的分辨率，表情位置会出现偏差，目前没有找到合适的办法解决)，
   在网上看的是， 写插件的作者之前设计的时候，没有把适配考虑进去，

   5:性能未测试

参考链接：

[TextInlineSprite](https://blog.csdn.net/qq992817263/article/details/51000744)

## 第三种是 EmojiText （文字和图片独立渲染）（搁置）

  这个方案目前想到有两个弊端：

  1：Outline、Shadow 使用时，图片也被处理的问题

  2：图片是根据文字大小来渲染的，跟文字大小一样，显示看起来表情没有很突出

参考链接：

[EmojiText (文字和图片独立渲染)](https://blog.uwa4d.com/archives/Sparkle_UGUI.html)

 上面的这些情况是我在项目中遇到的实际问题，以下链接中有详细的

[UGUI 图文混排解决方案和优化](http://www.cnblogs.com/zsb517/p/6667050.html)

## 第四种：Super Text Mesh （采用）

SuperTextMesh 是个功能很强大的插件，可以实现渲染动态文字，富文本支持图文混排。具体的功能类型有：文字效果、TextColor、Automatic、Master、Inline；（图文混排就是 Inline 的一种实现方式：Quads）
还有一个比较好的是，SuperTextMesh 是开源的，根据项目的需求可以修改源代码。

unityAssetStore 下载链接

[Super Text Mesh](https://assetstore.unity.com/packages/tools/gui/super-text-mesh-57995)

1.导入 SuperTextMesh1.7.1

新建一个 Unity 项目，然后双击 Super Text Mesh.unitypackage 导入 如图1.1

![图 1.1](https://github.com/yuyaoxue/yuyaoxue.github.io/blob/master/assets/_v_images/_图11_1526006366_19009.png)
                 图 1.1

导入项目之后，接下来说下 SuperTextMesh 的使用流程。

2.添加 Super Text Mesh 到 Scene

1->创建 UI Super Text Mesh 如图 2.1
![图 2.1](https://github.com/yuyaoxue/yuyaoxue.github.io/blob/master/assets/_v_images/_图21_1526006495_27911.png)
        如图 2.1
2->创建 3D Super Text Mesh  如图 2.2
![图 2.2](https://github.com/yuyaoxue/yuyaoxue.github.io/blob/master/assets/_v_images/_图22_1526006523_32629.png)
                             图2.2

我创建了一个 UI SuperTextMesh 创建之后如下图 2.3：
![图 2.3](https://github.com/yuyaoxue/yuyaoxue.github.io/blob/master/assets/_v_images/_图23_1526006541_5787.png)
        图 2.3
3.图文混排的实现

  在 Text 上输入<q=face>就可以显示出图片来.实现了图文混排，如上图 3.1 所示
![图 3.1](https://github.com/yuyaoxue/yuyaoxue.github.io/blob/master/assets/_v_images/_图31_1526006562_27080.png)
                            图3.1

Game窗口显示效果：
![图 3.2](https://github.com/yuyaoxue/yuyaoxue.github.io/blob/master/assets/_v_images/_图32_1526006593_23188.png)
               图 3.2

“face”是 face.asset 文件；SuperTextMesh 里的资源加载方式是通过 Resources.Load()；
如果需要修改加载资源方式，可以在 SuperTextMeshData 类文件修改。
创建 .asset 文件->在 Project 里的 Resource 文件夹右击->Create->Super Text Mesh  出来的 Data 数据就是你可以创建的数据文件了，
如下图 3.3，3.4 所示：
![图 3.3](https://github.com/yuyaoxue/yuyaoxue.github.io/blob/master/assets/_v_images/_图33_1526006610_27818.png)
                                   图 3.3
![图 3.4](https://github.com/yuyaoxue/yuyaoxue.github.io/blob/master/assets/_v_images/_图34_1526006630_24233.png)
                                   图 3.4

SuperTextMesh 里面的相关参数的使用官方文档里有详细的解释，我在这里就不赘述了，
以下是文档链接:
[SuperTextMesh 官方文档](http://supertextmesh.com/docs/SuperTextMesh.html)

接下来说下根据我公司的项目需求对源码做了哪些扩展。

1.显示方面
    size:  可以在 .asset 文件上，或者通过<s=1.2f>修改 size （是个比例值，相对于被插入的文本的大小），默认是1；
         如果需要设置 size，会涉及到一个位置偏移的问题，需要设置 .asset 文件上的 offset 参数，
        目前 size 设置的是 1.2 offset.y : -0.2（具体根据实际情况设置）

2.颜色格式
        SuperTextMesh 里的颜色格式是<c=name></c>或者<c=hexcolor></c> hexcolor 不包括 #，0x 的字符检测。
     结合项目的需求，添加了“<color></color>”的颜色格式，在检测颜色字符里添加了“#，x0”

3.加载资源
         Super Text Mesh 的例子中加载 .asset 文件是通过 Resource.load 加载的(.asset 文件，SuperTextMesh 预制体)，
项目中目前使用的是 AssetBundle 的异步加载，

        1>:  目前将 .asset 文件修改为异步加载了，LoadQuadAssets.cs

        2>:  SuperTextMesh 预制体目前还是使用 Resource.Load 加载

4.布局排版问题
1.6 版本: 之前用的 1.6 版本布局排版有问题)我们聊天有个需求是背景框会根据聊天内容来调整背景框的宽高使用 layout，
在设置 HorizontalLayoutGroup 下，Super Text Mesh 的 width height 一直是 0 导致背景框无法做相应的缩放

1.7.1 版本: 2018 年 4 月 27 日发布的 1.7.1 支持自定义布局，需要对应加入 ContentSize Fitter；Super text mesh 没有在代码里修改过 height，
width 会随着 Content Size Fitter 的控制为变化； SuperTextMesh 里的 height 有一个作用是这个文本框的最大垂直空间，如果 rect.height 的值为 0 则无限制
           问题:对于我们项目来说，有一个问题:我们是需要根据文本的行数去动态的设置文本背景框的宽高， 这个是根据 text 的 height 值来进行变化的，所以需要我们通过代码来设置 height。

解决：在 text 上添加一个 layout element 组件，我们可以根据 SuperTextMesh 里的本文的边框来计算出来文本框的高度，RecalculateBounds() 方法里重新计算边框的四个点，可以通过这四个点计算出宽高，然后写一个委托把值传到 Layout Element 里，就可以了。

5.不同分辨率适配：
     如果项目中的 Canvas 的 Canvas Scaler mode 为 Scale With Screen Size ，那么 text 的显示就会跟分辨率有关系

原因：text 的虚拟边框的数据跟所在跟自身以及所有父节点的 Scale 有关系， Super Text Mesh 代码中在计算边框时使用到了 Transform.TransformPoint，而 TransformPoint 是跟自身以及所有父节点的 Scale 值有关系，所以如果需要获取 text 的虚拟边框值，需要注意下这点。

![图 6.1](https://github.com/yuyaoxue/yuyaoxue.github.io/blob/master/assets/_v_images/_图61_1526006647_20181.png)
                     图 6.1   蓝色框时虚拟框
![图 6.2](https://github.com/yuyaoxue/yuyaoxue.github.io/blob/master/assets/_v_images/_图62_1526006662_12044.png)
                    图 6.2   计算边框代码位置

最后： 图文混排在 SuperTextMesh 只是冰山一角，有时间实践下其他的功能。
