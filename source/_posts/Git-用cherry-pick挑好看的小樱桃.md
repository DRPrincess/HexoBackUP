
---
title: Git-用 cherry-pick 挑好看的小樱桃
date: 2018-03-05 18:34:01
tags:
---

挑啊，挑啊，挑樱桃～
<!-- more-->

# 前篇

> 在此之前，我想问一个问题，你是在接触 Git 多久之后，知道有这个命令的？

我的答案是很久很久之后，这真是一个悲伤的故事。懒，是万恶之源，此话果然不假。

# cherry-pick 能干啥？

cherry，中文翻译是樱桃，pick， 中文翻译是采集，挑选。所以，cherry-pick 就是挑选樱桃,`git cherry-pick` 就是从你的项目文件中找出"樱桃"二字，找到就可以找博主来兑换樱桃了。

以上是开玩笑，写博客呢，干什么，正经点！

cherry-pick 的翻译是择优挑选，使用`git cherry-pick`命令，可以选择将现有的一个或者多个提交的修改引入当前内容。

那么，什么情况下会有到这么不常见的命令呢？

假设你现在正在开发一个项目，有一个功能分支 feature，开发分支 develop。 feature 有3个提交，分别是 A ，B ，C 。develop 分支只想加入 C 功能， 此时合并操作无法满足，因为直接合并 feature，会将3个提交都合并上，我想合并就只有 C，不要 A，B。此时就需要挑樱桃大法--cherry pick！

具体的做法：

1. 切换到 develop 分支。
2. 通过 `git log feature`,找到 C 的 SHA1 值。
3. 通过 `git cherry-pick <C的SHA1>` ，将 C 的修改内容合并到当前内容分支 develop 中。
4. 若无冲突，过程就已经完成了。如果有冲突，按正常冲突解决流程即可。


![cherry-pick 示意图](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/9aa1f519d3cd1582fe2df23e1daec86d.png)




# cherry-pick VS merge, Ready? GO!

从上面简单的小例子上看，我想，小伙伴们，都应该已经对 merge 和 cherry-pick 有了大概的区分，这里做下对比，让大家有个清晰明确的掌握，防止似是而非，以后误操作。

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/2b21a3fbbfb2075a6bff00005864c0dd.png)
![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/84636ade7b19f14305ced1b0f2a2b6b7.png)

`git merge ` ：将两个提交历史合并。   
`git cherry-pick`：将提交对应的内容合并。

这里，非常需要明确的一点，**commit 代表的是修改！**

例中，提交 C 的内容，就是对比 B 上面做的修改，可能是创建了一个文件，或者修改了一个词语。那么 C 内容就是一个文件的添加，和一个词语的修改。

以提交 C 为结束点的提交历史，实际内容是提交 C 和 C 之前所有的修改。

cherry-pick 操作的对象就是 commit。
merge 操作的对象就是 commit history。

所以，使用的时候，你要知道，你想要的什么。

# 博主邀请你参加挑樱桃游戏

光说不练假把式，现在写个小 demo 测试一下。

1. 创建一个空文件夹 GitDemo,`git init`初始化。  
2. 随便创建一个文件，完成初次提交，创建 master 分支。
3. 创建并切换 develop 分支，创建个提交，每一个提交中创建一个文件，方便测试。

具体命令如下：

```
// 切换到GitDemo目录下,并初始化Git
cd .../GitDemo  
git init  

//创建初次提交，创建 master 分支
touch cherry-pick.txt
git add .
git commit -m '创建cherry-pick文件，初次提交'  


//创建并切换到 develop 分支，创建提交“樱桃1号”
git checkout -b develop
touch 樱桃1号.txt
git add .
git commit -m "创建樱桃1号文件"


//创建提交“樱桃2号”
touch 樱桃2号.txt
git add .
git commit -m "创建樱桃2号文件"

//创建提交“樱桃3号”
touch 樱桃3号.txt
git add .
git commit -m "创建樱桃3号文件"

```

以上，测试场景构建完毕。现在用 `git log develop` 查看 develop 的提交历史如下：

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/1c0d9f7a0c51d14323f276aff30cee6e.png)

现在，仔细瞅瞅，你最喜欢几号樱桃，喜欢哪个，就挑哪个。我喜欢3号，从上图看到3号的 SHA1 值是`9e2d49b7c6d868c4cac4c5198d6661837eca813b`,使用前几位就足够了。

```
//切换到 master 分支
git checkout master
//挑选3号樱桃
git cherry-pick 9e2d49b

```

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/a21997ec056754d93f5f4f19945931dd.png)

挑选成功，通过 `ls` 命令，看到成功加入`樱桃3号.txt`。

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/1711676addc7ab27b171966dcf5c5b83.png)

挑樱桃游戏成功！

另外，需要说明的是，cherry-pick 到 master 的樱桃3号，事实上不是真的 3 号，是 3 号的复制品， 两者的 SHA1 值是不同的，由此可确认这是两个提交。

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/227958ecf8d386c9a061939823a15b4d.png)


# 了解更多的 cherry-pick

理解 cherry-pick 操作的本质，之后，再看其他的命令，就毫无压力了。全部命令详看[官方文档](https://git-scm.com/docs/git-cherry-pick)，这里我给出几个比较常用的：



```
git cherry-pick <commits>

```
挑选多个提交合并,提交之间用空格相隔。例如，想挑选1号和3号的，就可以用`git cherry-pick 4d2951 e4cdff9`命令一步到位了。


```
git cherry-pick <start-commit>..<end-commit>

```

挑选一个范围的多个提交合并,但是这个语法对应操作区别是左开右闭，不包含start-commit。另外要注意两个commit 之间要求有连续关系的，并且前者要在后者之前，顺序不能颠倒。

```
git cherry-pick <start-commit>^..<end-commit>

```

这个和上面一样，区别就是加了一个`^`符号，就变成闭区间了，包含 start-commit。


```
git cherry-pick <branch name>
```
挑选 branch 最顶端的提交。例如挑选 3 号樱桃可以用`git cherry-pick develop`。

```
git cherry-pick --continue  //继续下个操作
git cherry-pick --quit //退出
git cherry-pick --abort //停止本次操作

```

以上是关于 cherry-pick 操作控制命令，当  cherry-pick 多个提交时，假设遇到冲突，`--continue `继续进行下个，` --quit `结束 cherry-pick 操作，但是不会影响冲突之前多个提交中已经成功的,`--abort `直接打回原形，回到 cherry-pick 前的状态，包括多个提交中已经成功的。


# 尾篇

对于这个命令来说，理解 commit 的本质是修改很关键。好了，下篇博客见～，这个3月要将当初计划的 Git 系列博客补完，Fighting！

---

刚刚开通了个人微信公众号，最新的博客，好玩的事情，都会在上面分享，欢迎关注，让我们一起学习和成长。

<div  align="center">    

![微信公众号](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/qrcode_for_gh_e8f891ce77fb_258.jpg)

</div>
