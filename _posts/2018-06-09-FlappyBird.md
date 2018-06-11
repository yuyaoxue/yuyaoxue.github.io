---
layout: post
title:  "FlappyBird 制作进度"
date:   2018-06-09 1.20 +0800
categories: jekyll update
---
# FlappyBird 制作进度：

1.创建 GitHub 仓库；

2.安装 Unity2018.1.0b12,创建 Unity 工程，导入项目资源；

3.处理图集；

4.整理整体思路，创建相关文件夹以及目前会涉及到的脚本；

5.根据背景图大小设置 Camera 的相关参数；

6.制作 Bird ,Wall 等预制体以及相关动画；

7.制作 Start、GameOver、Score 等 UI 界面。

## FlappyBird 制作进度 6月9号

1.创建游戏状态机，bird的状态机

2.Bird 默认飞行，按键向上飞行

3.Bird 与 Walls、Ground 的碰撞检测

剩余功能：

1.动态加载 Wall、ground、bird。

2.Audio 音效系统

3.完善状态机，将游戏流程串联起来。

4.评分机制

5.游戏模式：简单，复杂，困难

## FlappyBird 制作进度 6月11号

1.简易 UIManager

2搭建（Start, Ready,Gameover）界面；

3.Bird 的碰撞检测;

4.Ground Pipe 循环移动;

5.创建 GameMansger 实现游戏循环；

未完成部分:

1.音效管理器；

2.根据选择的不同难度，Pipe 的动态加载；

3.加分机制（加分，本地数据存储）；

4.随机背景颜色；

5.Bird （随机颜色，上升掉落角度调整）;