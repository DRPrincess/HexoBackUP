---
title: Git三大特色之Stage(暂存区)
date: 2017-11-01 14:14:09
tags:
---

有人说，暂存区是 Git 最精彩的设计，同时也是最难理解的部分～
<!--more -->



# 这是开篇

有人说，暂存区是 Git 最精彩的设计，同时也是最难理解的部分，两者我都感觉不太明显，但当我想写关于暂存区的理解后，发现的确不怎么好讲，这个玩意，有点只可意会的感觉，用 Git 用熟练了，自然体会到暂存区设计的精彩之处。

在我看来，学习其他命令之前，对暂存区有一个概念和大概理解是非常重要的，因为，很多命令都涉及到了它。

# 为什么 commit 之前要先 add 一下呢？

我在刚接触 Git 命令的时候，对 Git 没什么概念，就是赶鸭子上线式的学习，用到什么，就去 Google 什么，例如第一天我搜索的就是“git first commit”,然后搜到很多 Git 的初级教程，几乎都有说先执行 git add ，然后 git commit。在我照着教程一步步 add & commit 的时候，我就在想，commit 就 commit唄，为什么 commit 之前必须要 add 一下呢？

当时才疏学浅，不敢胡乱尝试，担心试错了整不回来原来的样子。现在胆子大了些，决定尝试一下没有 add 的 commit。

通过 echo 命令给 master.txt 文件加了一行内容，然后执行提交：

![不 add 就 commit 的测试](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/3b3d9cc8226b1b18cdf6c5914d9ed4b8.png)


![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/4d3f34127fce833b801ace49de19421c.png)

测试结果很明显，commit 失败，失败原因是没有可以提交的修改,然而奇怪的是，查看 master.txt 文件，内容却已经添加成功。
好在 Git 非常贴心的给了我们详细的错误说明，下面仔细看一下：

```
On branch master
Changes not staged for commit:
	modified:   master.txt

no changes added to commit

```

英语渣的我最近热衷于翻译：
```
在 master 分支
修改没有被暂存起来以备提交
	修改:   master.txt

没有可以提交的修改

```

从这个提示信息中，我似乎嗅到了一丝丝的真（jian）相(qing)，commit 时检测是否有修改的 master.txt，好像不是我看到的 master.txt。那我看到 master.txt 是什么？我没看到的又是什么？而且它在哪里？

答案就比较明显了，肯定在 Git 的暂存区（不然我为啥要举这个例子，哈哈）。

git commit 执行时，会提交暂存区的内容。git add 命令会将我们看到的修改添加到暂存区中，这就是为什么 git commit 之前要先执行 git add 的原因。

接着上面的问题，思维稍微发散一下，还可以问出很多问题，例如，add 将修改放入暂存区，那么 add 之前数据存放在哪里？commit 又将存储区的数据提交到什么地方了呢？以及为什么要这么分为几个存储部分？等看完这篇博客，希望你这些问题，都能找到答案。


# Git 可以大概分为三个区

Git 本地数据管理，大概可以分为三个区，工作区,暂存区和版本库。
- **工作区（Working Directory）**   
  是我们直接编辑的地方，例如 Android Studio 打开的项目，记事本打开的文本等，肉眼可见，直接操作。
- **暂存区（Stage 或 Index）**   
  数据暂时存放的区域，可在工作区和版本库之间进行数据的友好交流。
- **版本库（commit History）**   
  存放已经提交的数据，push 的时候，就是把这个区的数据 push 到远程仓库了。



下面是，当开发者通过 git 修改数据时，各区之间的数据传递流程示意图。

![git 数据流程图示意图](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/2429e4d2661e60027537aea0077f6e40.png)

为了验证以上流程的正确性，我们可以自己动手实验一下，为了对比三个区之间的数据差别，过程中，可以借助神奇的 diff 命令。

|命令|作用|
|---|---|
|git diff |工作区 vs 暂存区|
|git diff head|工作区 vs 版本库|
|git diff --cached|暂存区 vs 版本库|

现在三个区的数据是一致的，执行 git diff 命令都为空
|命令|接果|
|---|---|
|（工作区 vs 暂存区）git diff |![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/805899178327987c785580f569efc8a1.png)|
|（工作区 vs 版本库）git diff head|![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/805899178327987c785580f569efc8a1.png)|
|（暂存区 vs 版本库）git diff --cached|![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/805899178327987c785580f569efc8a1.png)|

然后给 master.txt 添加一行内容后，现在工作区内容发生变化，暂存区和版本库内容不变。
![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/4b5f4d1382948420956683d65d323271.png)

|命令|接果|
|---|---|
|（工作区 vs 暂存区）git diff |![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/47125eec79eea756b08c57f1673d0275.png)|
|（工作区 vs 版本库）git diff head|![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/daca76543e05a93a4539544b6cfa037c.png)|
|（暂存区 vs 版本库）git diff --cached|![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/805899178327987c785580f569efc8a1.png)|




执行git add 操作后，修改同步到暂存区，现在工作区和暂存区数据一致。
![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/80084f3335343b6f2e3400582df2147c.png)
|命令|接果|
|---|---|
|（工作区 vs 暂存区）git diff |![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/805899178327987c785580f569efc8a1.png)|
|（工作区 vs 版本库）git diff head|![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/daca76543e05a93a4539544b6cfa037c.png)|
|（暂存区 vs 版本库）git diff --cached|![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/daca76543e05a93a4539544b6cfa037c.png)|


执行 git commit 操作后，修改已经同步到版本库，三区数据再次保持一致。
![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/018cb4ce2a6582ae743f9262aca73f5f.png)

|命令|接果|
|---|---|
|（工作区 vs 暂存区）git diff |![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/805899178327987c785580f569efc8a1.png)|
|（工作区 vs 版本库）git diff head|![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/805899178327987c785580f569efc8a1.png)|
|（暂存区 vs 版本库）git diff --cached|![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/805899178327987c785580f569efc8a1.png)|

# Stage 赋予 Git 更多灵活性

不知道时，你对它可能毫无所感。知道后，你一定会感动地想哭，并十分之膜拜 Git 的开发者- Linus Torvalds ，stage 就是这么精彩的玩意。以下看起来比较束手无策的场景，只要理解 stage,用好相应命令，都能轻易解决：

- 修改了4个文件，在不放弃任何修改的情况下，其中一个文件不想提交，如何操作？（没add : git add  已经add: git reset --soft ）
- 修改到一半的文件，突然间不需要或者放弃修改了，怎么恢复未修改前文件？ (git checkout)
- 代码写一半，被打断去做其他功能开发，未完成代码保存？(git stash)
- 代码写一半，发现忘记切换分支了？(git stash & git checkout)
- 代码需要回滚了？（git reset）
- 等等

上面提到的 checkout & stash & reset 等命令，通过不同的参数搭配使用，可以在工作区，暂存区和版本库之间，轻松进行数据的来回切换。

例如前篇 Branch 博客用到的 git reset 回滚命令，带上不同参数就有不同的作用，如下：

|命令|作用|
|---|---|
|git reset --soft |暂存区->工作区|
|git reset --mixed  |版本库->暂存区|
|git reset --hard|版本库->暂存区->工作区|

完事大家可以自己新建一个测试 Demo，多尝试一下，相信你会因为暂存区的存在更加地喜欢 Git。

# 这是结尾

暂存区是介于工作区和版本库之间的一个中间存储状态，很多命令都会涉及暂存区的状态，因此理解暂存区这一个存在是至关重要的。

希望这篇文章能给你的 Git 学习带来帮助，同时，如有错误之处还望指出，下篇博客见，see you next blog！


---

刚刚开通了个人微信公众号，最新的博客，好玩的事情，都会在上面分享，欢迎关注，让我们一起学习和成长。

<div  align="center">    

![微信公众号](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/qrcode_for_gh_e8f891ce77fb_258.jpg)

</div>
