---
layout: post
title:  "有关于对待解决问题态度的报告"
date:   2018-07-29 17:00 +0800
---
# 前言

本文是前几天我在工作中使用 git 指令合并分支的时候出现了问题，而当时采取了直接 “跳过问题” 的解决方案，导致了文件显示不出来了。所以写一份报告总结记录下。

## 错误

在 master 分支上跟其他分支进行 merge 时，二进制文件出现冲突。后续使用解决冲突的方式不对，导致二进制文件（.png 图片）显示不出来。

## 表面原因

出现错误的原因是：在出现冲突时，我先错误的使用了 “他人分支”，出现错误 “无法更新一个或多个文件，请确保没有其他的应用正在锁定你的文件” 时，后使用 “标记为已解决”。后来冲突文件消失了，我当时以为问题真的被解决了。

## 根本原因

在我的分支上跟 master merge 过，后来提交过几次之后，又进行了 rebase 操作重写历史提交信息后没有成功修改我的提交信息，后来我又通过 --soft 操作把我分支上的所有的提交都 reset 了，最后合并成一个提交。

执行 rebase 之后，跟 master merge 之后的文件的提交信息都丢失了，而这些文件在 master 分支上又被其他人修改过。导致我在回到 master 分支之后再跟我的分支 merge ，那些丢失提交信息的文件，就会出现冲突。

    rebase 会将合并提交变为普通提交，丢失父节点信息。

使用 “他人分支” 或使用 “我的分支” 解决冲突失败是因为找不到文件的版本号

     $ git checkout --theirs /e/Unity_new/client/Assets/Textures/Sprites/Quality/Content/CarQuality_D.png
     error: path 'Assets/Textures/Sprites/Quality/Content/CarQuality_D.png' does not have their version

## 思考

对于这一问题的处理方式，我犯了几个错误：

1.对于解决冲突的方式：“他人解决”，“我的解决”，“标记为已解决”，这几个操作所表示的意义不够深刻，导致做出了错误的操作。

2.在 master 分支上进行分支 merge 时候，出现冲突文件错误信息，没有仔细分析。

3.在 SourceTree 解决冲突时，出现了 “无法更新一个或多个文件，请确保没有其他的应用正在锁定你的文件” 时，没有在命令行操作下看看具体的错误信息。

4.当我在我的分支使用 git rebase -i 来重写历史信息的时候。没有思考过 rebase 操作会产生什么影响。

5.最重要的是对待问题的处理态度，遇到自己不清楚的问题，下意识地选择跳过问题。不专业，不严谨，在没有了解清除冲突信息，就进行下一步操作。

## 文中使用到的 Git 指令

1.合并分支

    git merge master

2.解决冲突文件

    git checkout --theirs xxx文件    使用他人分支解决

    git checkout --ours xxx文件      使用我们分支解决

3.改变最近一次提交信息

    git commit --amend

4.修改多个提交说明

    git rebase -i HEAD~3

5.取消暂存

     git reset HEAD <file>...

参考资料：
[git pro 中文版](https://git-scm.com/book/zh/v2)