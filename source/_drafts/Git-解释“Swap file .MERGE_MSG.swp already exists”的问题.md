---
title: Android-项目中采用的混淆加固多渠道打包方案
date: 2017-08-23 19:56:44
tags:
---


<!--more-->

# 场景：

执行 `git pull` 的时候，出现了以下错误：

![合并的时候出错了](http://oriwplcze.bkt.clouddn.com/5c045bd4a9fda402e5956105705be5f6.png)git-pull-error.jpg


 git 和 vim 命令我运用的并不熟练，当时为了解决这个问题，我切换到 Android Studio 集成的 git 工具中去操作，然后操作成功了。

 虽然问题解决了，但是这个解决方法有点逃避的感觉，我想知道这个问题发生的原因？为什么Android Studio 集成的 git 工具可以操作成功？下次发生的时候我怎么解决？于是有了这篇博文产生，算是给自己的一个交代吧^_^

# 自己给自己答疑

一个很好的解释，但是有点看不明白。
https://stackoverflow.com/questions/41284031/how-to-solve-git-merge-error-swap-file-merge-msg-swp-already-exists
