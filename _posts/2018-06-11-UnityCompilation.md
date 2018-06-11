---
layout: post
title:  "Unity 文件夹编译顺序"
date:   2018-06-11 1.20 +0800
categories: jekyll update
---
# Unity 特殊文件夹编辑顺序

前一段时间做图文混排导入了一个插件 Super Text Mesh，把文件夹直接导入到项目根目录下 Assets 文件夹里面，忘记放到 Plugins 文件夹下。之后再转移文件目录的时候，会报一个外部类不存在之类的错误，查了半个小时无果，同事提示我查 Unity 的编译顺序，看完 Unity 相关的官方文档之后恍然大悟，原来跟 Unity 的特殊文件夹的编译顺序有关。

在使用 Unity 创建项目的时候，我们可以根据需求创建相应的文件夹;不过有一些特殊文件夹是 Unity 指定的，具有特殊的功能。
Assets，Plugins，  SteamingAssets ， StandardAseets，Editor ,Resources, Editor default resources。