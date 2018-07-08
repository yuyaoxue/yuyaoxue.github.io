---
layout: post
title:  "Unity Compilation Bug"
date:   2018-07-08 16:00 +0800
---
# 前言

前几天在工作中，遇到一个有关 Unity 编译失效的问题，我修改的脚本保存后，Unity 自动编译后，编译失效。

## 问题描述

我在 VS 里修改了代码保存之后，回到 Unity 里，Unity 自动编译，编译完成之后，还是我修改代码之前的情况（我修改的文件没有被编译）。

## 问题复现

比如：我修改并了脚本 A，回到 Unity，Unity 自动编译，在编译期间修改并保存了脚本 B。编译完成之后，脚本 B 修改的部分极有可能没有被编译。

## 问题原因

1.检查了 Unity 的自动编译开关 Edit->Unity Preferences->General->Auto Refresh; 排查了 Unity 的设置问题
![auto Refresh](https://github.com/yuyaoxue/yuyaoxue.github.io/blob/master/assets/_v_images/AutoRefresh.png?raw=true)

2.后来问了同事之后才了解的，这个问题 Unity 标记最后编译时间机制有关系。对于 Unity 而言，VS 只是一个编辑脚本工具，所有的编译工作都在 Unity 这边执行。Unity 不会记每个文件的编译时间，只会记录一个总的最后的编译时间。比如 Unity 编译开始时间为 t1 编译过程需要 10s，最后的编译时间为 t1+10s，如果在这 10s 内又对脚本做了修改，恰好这个脚本已经被编译过了。那这个脚本不会重新被编译，导致了后面的那次修改没有被编译。

## 解决办法

如果发生了编译失效问题，可以试试以下方法解决：

1.修改并保存编译失效脚本；

2.关闭并重新打开 Unity，重新修改并保存编译失效脚本。

## 总结

有两点疑问没有解决：
1.Unity 最后编译时间存储在什么地方？
2.各个文件的编译时间存储在什么地方？

后续查 Unity 标记编译时间相关的资料没查到。听同事说这是 Unity 编译的一个 Bug，个人解决不了，只能在做的过程中尽量避免。现在我都会尽量避免在 Unity 执行编译的时候修改脚本。