
---
title: Git-rebase 黑魔法之打造完美的线性历史
date: 2018-03-06 15:44:01
tags:
---

这不是一篇博客，而是一篇黑魔法教习大全
<!--more-->

# 写在前面

到现在，相信大家都已经能够使用 Git 做日常的项目管理了，今天给大家介绍的是 Git 的黑魔法 `rebase` 命令。

`rebase` 黑魔法和 `merge` 本质上做的是一个事情，都是分支历史的合并。不会这个技能对你的日常没有什么影响，但是 GET 这个技能之后会让你帅气值加10分。

现在，您是否 Get 这个黑魔法呢?

好的，以下不是一篇博客，而是一篇黑魔法教习大全。


# rebase 是一个有故事的命令

如果上一篇 `cherry-pick` 翻译为挑草莓，是一个美好的童话，`rebase` 的翻译就是一出大型的人性伦理记录片，因为它的中文翻译是 **变基** 。嗯，这个命令真是生来就拥有故事。

以上，不是开玩笑，`rebase` 的意思真的是变基,只不过，改变的不是某种取向，而是 commit 的基础提交，也就是它的父提交。

我第一次看也不理解，没关系，先来个小例子平静先，如下图所示，有两个分支，develop 和 feature，两个分支都有了各自的提交。


![例1分支初始示意图](http://oriwplcze.bkt.clouddn.com/d1768ce1956caa5905d3c9b7add05dc3.png)

在见识到 rebase 的处女秀之前，来让我们温故而知一下，动动小手画一下普通的分支合并的示意图。嗯，这是一个很明显的策略式合并，会产生一个新的合并提交，如图：

![merge 示意图](http://oriwplcze.bkt.clouddn.com/3e03b933d8569b0fed71b66ee02aed5b.png)

温故完毕，现在开始 rebase 变基的黑魔法，请瞪大你的眼睛，擦亮你的眼镜儿。

![rebase 示意图](http://oriwplcze.bkt.clouddn.com/70b059fa56a970a0acaebe9649bc9e65.png)

咦？！！此刻，是否感觉有点懵懵哒？

这个乍一看，有点像快进式合并，但不是，因为上面我们说了，很明显是要策略合并的。那这是什么呢，就是 **变基** !从图中看到，C3 的基提交由 C1 变成了 C2。

# rebase 打造线性的提交历史

上例中，你可能不明所以，但肯定能感受到的是，reabse 操作后的分支示意图是很简洁的一条线，非常直，没有分叉，这是我们说的线性的提交历史。

简洁的线性的历史，是很多人喜欢用 rebase 的原因。现在我们用小 Demo 实战一下上例中的操作，感受更深刻。

1. 创建一个空文件夹 GitDemo,`git init`初始化。  
2. 随便创建一个文件，完成初次提交，创建 master 分支。
3. 基于初次提交创建并切换 develop 分支，然后创建两个提交。
4. 切换会 master 分支，再创建一个提交。

这样就会构建上例中的分支模型了，具体命令如下：

```
// 切换到GitDemo目录下,并初始化Git
cd .../GitDemo  
git init  

//创建初次提交，创建 master 分支
touch rebase.txt
git add .
git commit -m '创建 reabse.txt 文件，初次提交'  


//创建并切换到 develop 分支，创建提交“develop1”
git checkout -b develop
touch develop1.txt
git add .
git commit -m "创建 develop1.txt 文件"


//创建并切换到 develop 分支，创建提交“develop2”
touch develop2.txt
git add .
git commit -m "创建 develop2.txt 文件"

//切换到 master 分支 创建提交“master1”
touch master1.txt
git add .
git commit -m "创建 master1.txt 文件"

```

rebase 操作的思想上分为两个步骤：
1. 确定变基对象：就是你改变的是哪个分支的提交，然后 checkout 到此分支。
2. 选好 base ：选好作为基点的提交，形象地来说，就是你要变到那条线上。
3. 开始 re ：rebase 基提交。


我现在要把 develop 合并到 master 上，所以要改变的是 feature 的提交，选择的基提交是 master 的最新提交，于是命令如下：

```
git checkout develop
git rebase master

```

![](http://oriwplcze.bkt.clouddn.com/c6c9cb4917e3e27c07d49da664ab1831.png)

rebase 操作执行成功，现在 develop 分支指向的变基的最新提交，但是 master 分支的指针不变，仍然指向“master1 提交”，所以，如果需要，使用`git merge master develop`快进式合并一下，移动 master 的指针指向最新的提交。


以上，通过 rebase 打造线性历史作战成功。



#  rebase 的两个细节

来看下 rebase 前后的 develop 的分支历史，如下图，左侧是 rebase 前，右侧是 rebase 后。


![](http://oriwplcze.bkt.clouddn.com/0a9234e8964fc3be8a38b016e4108c99.png)

注意上图标识箭头的地方，对比后你会得到两个结论

- rebase 为每一个变基后的提交都创建了一个内容相同新的提交。  
 develop 的两个变基提交 `创建 develop1.txt 文件`和`创建 develop2.txt 文件`的 SHA1 值都发生的变化，都是新创建的提交。变基前的提交已经被丢弃，已经被回收或者正在等待回收。

- 变基后的提交会依次排到 master 分支的后面。     
 注意到`创建 master1.txt 文件` 这个提交的时间是比 develop 分支的两个提交要晚的，如果是 `merge` 操作的话，分支的历史，会根据时间把 `创建 master1.txt 文件` 放在 develop 两个提交的后面。但 rebase 操作会所有变基的对象会直接整个排到基础提交的末端，不会让分支历史混乱。（关于 merge 的提交合并，你平时可以观察一下，是不是）


# rebase 的唯一法则

rebase 使用起来非常神奇，但我们要有底线，这个底线就是：

**不要对在你的仓库外有副本的分支执行变基。**

这句话的直白意思，就是不要对已经推送到远程的分支，做变基操作。想象一下，如果你的同事小明在 develop 分支上做了新的提交，你却把 develop 分支给变基了，小明基于的提交让你给变没了，你说小明会不会打死你？

所以，不要对在你的仓库外有副本的分支执行 rebase 操作，做 rebase 操作之前脑海中都要回想一下这句话。


# 灵魂拷问：好好的为什么要 rebase ？

要知道 rebase 和 merge 得到的目的是相同的，都是合并代码。于是，让一个人用惯了 merge 的人换成 rebase ,肯定会问“好好的，我为什么要换？”

rebase 想较于 merge 的优点就是完美的线性历史了。不扯什么分支的历史是代表项目的功能节点，还是项目真实过程的这种哲理性问题。只说，你喜欢线性历史，你就用 rebase,不喜欢你就用 merge。

至于不知道自己喜不喜欢的，我建议你俩都体验一下，然后再决定用谁。

我之前用的都是 merge,现在开始尝试 rebase ，慢慢感受线性历史的美好。

# 写在后面

其实 rebase 还有一种用法 `rebase -i`，被成为交互式 rebase ,又是一个强大的黑魔法，威力太大所以单独成篇，后续[《Git-rebase 黑魔法之打磨 commit 颗粒度》](http://blog.csdn.net/qq_32452623/article/details/79475057)已经发布，敬请关注！

小伙伴们，下篇博客见～



---

刚刚开通了个人微信公众号，最新的博客，好玩的事情，都会在上面分享，欢迎关注，让我们一起学习和成长。

<div  align="center">    

![微信公众号](http://oriwplcze.bkt.clouddn.com/qrcode_for_gh_e8f891ce77fb_258.jpg)

</div>
