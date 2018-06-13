---
layout: post
title:  "Unity 文件夹编译顺序"
date:   2018-06-11 1.20 +0800
categories: jekyll update
---
# Unity 特殊文件夹编辑顺序

前一段时间做图文混排导入了一个插件 Super Text Mesh，把文件夹直接导入到项目根目录下 Assets 文件夹里面，忘记放到 Plugins 文件夹下。之后再转移文件目录的时候，会报一个外部类不存在之类的错误，查了半个小时无果，同事提示我查 Unity 的编译顺序，看完 Unity 相关的官方文档之后恍然大悟，原来跟 Unity 的特殊文件夹的编译顺序有关。

在使用 Unity 创建项目的时候，我们可以根据需求创建相应的文件夹；不过有一些特殊文件夹名称会被 Unity 解释为一个指令，文件夹中的内容会被 Unity 特殊对待，这些文件夹的会影响脚本编译的编译顺序。

Assets

Plugins

SteamingAssets

StandardAseets

Editor

Resources

Editor

default resources

以上是 Unity特殊文件夹名称

有关于这些特殊文件夹名称的相关信息，官网文档[Special folder names](https://docs.unity3d.com/Manual/SpecialFolders.html)给出了详细的说明

脚本编译有四个不同的阶段，处于哪个阶段编译由其父目录确定。

编译阶段：

阶段1：编译Standard Assets、Pro Standard Assets、Plugin中的运行时脚本

阶段2：编译Standard Assets、Pro Standard Assets、Plugin中的Editor脚本

阶段3：编译其他不在Editor目录下的脚本

阶段4：编译Editor目录下的脚本

    不同的编译顺序对脚本之前的引用很重要。基本规则是 被引用的类一定是在较早的阶段编译的。也就是说，在当前阶段之后编译的任何内容都不能被引用。在当前结算或早起阶段编译的任何内容都是完全可用。

我出现的情况是 我转移到Plugins的文件夹里面有脚本调用 Plugins以外的普通文件夹里的脚本。因为Plugins文件夹的编译顺序比Plugins之外的普通文件夹的编译顺序靠前，所以会报
“xx类未定义，找不到xx类的定义”之类的问题。

解决了我的问题之后，回头想想为什么要放到 Plugins 呢？除了规范文件夹的目录位置，还有什么作用呢？

在网上查了一些信息，大概是说会缩短项目的编译时间。

如果项目当中一些长时间不需要改动的脚本代码放到特殊文件夹里面，这些代码只需要编译一次，这样会缩短项目的编译时间。

还需要看下Unity Script 相关的文档，再来做编译时间的总结。