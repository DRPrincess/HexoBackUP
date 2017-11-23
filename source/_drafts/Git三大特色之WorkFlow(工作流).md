---
title: Git三大特色之WorkFlow(工作流)
date: 2017-11-10 14:42:09
tags:
---

怎么说，双十一前夜，还在这写博客的我，真想奖励给自己一朵大红花。

<!--more-->

# 开篇

Git 三大特色，分支，暂存区，工作流，今天终于要写到 WorkFlow 了，我彷佛已经看到胜利的曙光，走起。

# 何谓工作流

WorkFlow 的字面意思，工作流，即工作流程。在分支篇里，有说过这样的话：因为有分支的存在，才构成了多工作流的特色。事实的确如此，因为项目开发中，多人协作，分支很多，虽然各自在分支上互不干扰，但是我们总归需要要把分支合并到一起，而且真实项目中涉及到很多问题，例如版本迭代，版本发布，bug 修复等，为了更好的管理代码，需要制定一个工作流程，这就是我们说的工作流，也有人叫它分支管理策略。

工作流不涉及任何命令，因为它就是一个规则，完全由开发者自定义，并且自遵守，正所谓无规矩不成方圆，就是这个道理。

# 工作流最受欢迎榜

目前使用度最高的工作流前三名（排名不分先后哈）分别是以下三种：

- [Git Flow](http://nvie.com/posts/a-successful-git-branching-model/)
- GitHub Flow
- GitLab Flow

# Git Flow

这个工作流，是 Vincent Driessen 2010 年发布出来的他自己的分支管理模型，到现在为止，使用度非常高，我自己管理项目用的就是这个，也可以说是比较熟悉的啦。


Git Flow 的分支结构很特别，按功能来说，可以分支为5种分支，从5 种分支的生命时间上，又可以分别归类为长期分支和暂时分支，或者更贴切描述为，主要分支和协助分支。

#### 主要分支：
在采用 Git Flow 工作流的项目中，代码的中央仓库会一直存在以下两个长期分支：

- **master**
- **develop**

其中 origin/master 分支上的最新代码永远是版本发布状态。origin／develop 分支则是最新的开发进度。

当 develop 上的代码达到一个稳定的状态，可以发布版本的时候，develop上这些修改会以某种特别方式被合并到 master 分支上，然后标记上对应的版本标签。

#### 协助分支：

除了主要分支，Git Flow 的开发模式还需要一系列的协助分支，来帮助更好的功能的并行开发，简化功能开发和问题修复。是的，就是下面的三类分支。这类分支是暂时分支非常无私奉献，在需要它们的时候，迫切地创建，用完它们的时候，又挥挥衣袖地彻底消失。   
协助分支分为以下几类：

- **Feature Branch**
- **Release Branch**
- **Hotfix Branch**

Feature 分支用来做分模块功能开发，命名看开发者喜好，不要和其他类型的分支命名弄混淆就好，举个坏例子，命名为 master 就是一个非常不妥当的举动。模块完成之后，会合并到 develop 分支，然后删除自己。

Release 分支用来做版本发布的预发布分支，建议命名为 release-xxx。例如在软件 1.0.0 版本的功能全部开发完成，提交测试之后，从 develop 检出release-1.0.0 ,测试中出现的小问题，在 release 分支进行修改提交，测试完毕准备发布的时候，代码会合并到 master 和 develop，master 分支合并后会打上对应版本标签 v1.0.0, 合并后删除自己，这样做的好处是，在测试的时候，不影响下一个版本功能并行开发。

Hotfix 分支是用来做线上的紧急 bug 修复的分支,建议命名为 hotfix-xxx。当线上某个版本出现了问题，将检出对应版本的代码，创建 Hotfix 分支，问题修复后，合并回 master  和 develop ，然后删除自己。这里注意，合并到 master 的时候，也要打上修复后的版本标签。

#### Merge 加上 no-ff 参数

需要说明的是，Git Flow 的作者 Vincent Driessen 非常建议，合并分支的时候，加上 no-ff 参数，这个参数的意思是不要选择 Fast-Forward 合并方式，而是策略合并，策略合并回让我们多一个合并提交。这样做的好处是保证一个非常清晰的提交历史，可以看到被合并分支的存在。

下面是对比图，左侧是加上参数的，后者是普通的提交：

![](http://oriwplcze.bkt.clouddn.com/4bf39a7e3f8cfcff2f2320cc632adb80.png)


#### Git Flow 示意图


下面这张图，我在刚学习 Git 的时候，看到很多次这个图，然并卵，一直都没看懂过，也不知道这张图来自 Git Flow ，只能说，我当初学 Git 的方式的确不怎么认真和系统 。好在，我现在已经能看明白了这个图，并且还写了个博客，时光真是好神奇。

![](http://oriwplcze.bkt.clouddn.com/f2a8e2be7b4515665137cc295d6347a5.png)


图中画了 Git Flow 的五种分支，master，develop，feature branchs,release branchs,hoxfixes，其中 master 和 develop  字体被加粗代表主要分支。master 分支每合并一个分支，无论是 hotfix 还是 release ,都会打一个版本标签。通过箭头可以清楚的看到分支的开始和结束走向，例如 feature 分支从 develop 开始，最终合并回 develop ，hoxfixes 从 master 检出创建，最后合并回 develop 和 master，master 也打上了标签。

看懂之后，觉着这个图画的真好看。

想了解更多，可以看 [Git Flow 的英文说明文档](http://nvie.com/posts/a-successful-git-branching-model/)，点击可以看到作者对它的说明和讲解，而且作者还特别贴心的附了这个示意图的文件,是用 keynote 画的。

# GitHub Flow

GitHub 使用的工作流。

# GitLab Flow

GitLab 使用的工作流。
