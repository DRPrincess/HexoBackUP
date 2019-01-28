---
title: Git-你好, HEAD 同学
date: 2018-02-27 17:03:01
tags:
---

真是一场自我尬聊的好表演
<!--more-->

# 开篇之我为什么开始了和 HEAD 同学的尬聊

在之前的博客中，多次提到了 HEAD，例如这个从 讲分支那篇博客扒出来的图：

![深层次的分支概念图](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/f299bd7aeadd9d4b6a57dd5134b8b69a.png)

我一直认为它是一个指向当前分支的指针，但是这两天看扔物线大神的 [Git 原理详解及实用指南](https://juejin.im/book/5a124b29f265da431d3c472e) 的时候，突然之前，我对这个 HEAD 指针有了不一样的看法。

多次相逢不相识，现在我要研究它。

# HEAD 在哪？HEAD 是谁？

第一步，我们先要去找到 HEAD。找之前，先考大家一个问题：
> Git 是怎么和项目联系起来的呢？

你们思考着，反正我先公布答案了，哈哈哈。

是因为在执行第一个 git 命令操作 `git init`的时候，在项目的根目录下，创建了一个`.git`文件夹, 此文件是隐藏文件，Mac 系统小伙伴按下快捷键 `command+option+.` ，Windows 小伙伴勾选显示隐藏文件可以显示隐藏文件，就可以看到它了。

打开`.git`，可看到如下内容：

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/169f15d6d29b199d8d6d2e1d10225421.png)


`.git` 文件中存储 Git 对这个项目进行版本控制的所有信息，其他的先不说，小伙伴们，你们有没有发现重点?,有一个大写没加粗的 `HEAD` 文件！赶紧打开看看：

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/fc272aee260b1ba46030e1c845594cb0.png)


HEAD 文件的内容，是指向了另一个文件的应用，于是找的  `refs/heads/master` 打开,诡异的是，这个文件根本不存在，因为我刚刚对这个项目进行版本管理，此时，无任何提交，什么分支都不存在，master 分支是 git 的默认分支，所以会指向它。

不管怎样，我们找到了它，第一步完成。

另外，想分享给大家的是，我们还可以通过 `git show ` 命令来查看 HEAD 指针目前指向什么。

# HEAD 的本质是什么？

刚刚创建的项目没有版本管理的历史，现在创建几个提交和分支，然后切换分支看一下 HEAD 的内容。

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/fc272aee260b1ba46030e1c845594cb0.png)

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/455d1434db2f707e17ec5e36fc9534c2.png)

很熟悉的一大串数字，很像 commit 的 SHA1 值。我们通过 `git log ` 查看，发现是 master 分支最新的提交。

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/606427e4e7bfc23675d4430016174570.png)

目前 HEAD 文件指向的内容是传递顺序是这样的：

> HEAD->refs/heads/master->8813419d6b148d03192b6b166e2ed8fd800d0dc3

我得出了一个结论，HEAD 的本质不是提向分支,而是指向 commit 提交。

问题又来了：
 - HEAD 为什么要通过 refs/heads/master 中转一下，而不是直接指向 master 分支的提交？

 - HEAD 可以直接指向提交吗？



解释以上问题，需要知道 `refs/heads/` 文件夹存储的内容是当前项目所有分支的头指针，每个分支的头指针都指向该分支的最新提交。

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/9cb66f88f3fa5d0bd293c6be297d33da.png)

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/b3d6dfdd3e970698d2323710e61744d6.png)

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/455d1434db2f707e17ec5e36fc9534c2.png)
![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/22b34bf181e1a004264285574bf82264.png)
![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/3b7c469e240c5fef9fcd905ef8671ce9.png)


从上图看，三个分支的头指针指向的提交都不同，HEAD直接指向提交毫无问题。

但是此时出现一个 dev 分支，刚从 master 检出，头指针和 master 相同，HEAD直接指向提交的话，怎么知道，当前工作分支是 master  还是 dev 呢？

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/c4405eb93ef8be792450fe7994759142.png)

此时 HEAD 文件的内容就非常有意义了：

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/35c87f70da0eb3fa6cfc661075443eb3.png)

所以，HEAD 通过先指向分支的头指针，再指向提交的意义就是表明当前所处的分支。


关于第二个问题，答案是肯定的，但是会进入一种特殊的状态 `detached HEAD`。

# detached HEAD,游离的 HEAD 指针。

使用 `git checkout <commit id>` 成功的进入了
`detached HEAD` 状态：

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/bb694924624d1c87bccb61f0faec20c3.png)

此时的 HEAD 文件内容，不再指向分支的头指针，而是直接指向了提交。

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/eef40ab691b5ee9d3c1dfd6bfe86ec64.png)

得出结论，当 HEAD 指针直接指向提交时，就会导致 `detached HEAD` 状态。在这个状态下，如果创建了新提交，新提交不属于任何分支。相对应的，现存的所有分支也不会受 `detached HEAD` 状态提交的影响。

所以，宝宝们别怕，这个状态并不是坏事，在某些特殊的场景下你可能会需要它。

例如:  
排查问题的时候，checkout 到怀疑的 commit 点上去做些测试，`detached HEAD`会保护你的现有分支不受影响，测试完了不想保存直接 checkout 到其他地方，可以放弃修改。想保存修改，可以创建一个 `git checkout  -b <new-branch-name>` 新分支保存。

是不是很 Happy 哒？

**PS：**

除了`git checkout <commit id>`，还有一个命令可以进入`detached HEAD` 状态。

```
//HEAD 直接脱离分支头指针，指向分支头指针指向的 commit
git checkout --detach
```


# 除了 HEAD 指针，还有各种花式指针。

`.git` 是一个神奇的东西，除了其中几个固定不变的文件夹，有些文件会随着一些操作，创建和删除，所以，不同的时候打开它，会看到不同的文件，真有意思。


例如：有一天，灵感突来，我打开了`.git`,发现多了 `FERCH_HEAD` 和 `ORIG_HEADORIG_HEAD`。

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/6164f9cad87ce0fc708aecc68697cad9.png)



再例如：当合并冲突的时候，打开会发现 `MERGE_HEAD`。

![MERGE_HEAD存在情况](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/224bd5dbb9e2542da45184fed0de9e19.png)



再再例如：执行 cherry-pick 命令合并 commits 时,会出现 `CHERRY_PICK_HEAD`。

![CHERRY_PICK_HEAD存在情况](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/fa8253f1d6443c48f01b66e6148309ea.png)



宝宝们，目前为止出现了以下五种 HEAD ，是否能说出来各自都是什么意思？

- HEAD   
指向当前正在操作的 commit。

- ORIG_HEAD    
当使用一些在 Git 看来比较危险的操作去移动 HEAD 指针的时候，ORIG_HEAD 就会被创建出来，记录危险操作之前的 HEAD，方便 HEAD 的恢复，有点像修改前的备份。

- FETCH_HEAD     
记录从远程仓库拉取的记录。

- MERGE_HEAD  
当运行 `git merge` 时，MERGE_HEAD 记录你正在合并到你的分支中的提交。`MERGE_HEAD`在合并的时候会出现，合并结束，就删除了这个文件。

- CHERRY_PICK_HEAD  
记录您在运行 ` git cherry-pick `时要合并的提交。同上，这个文件只在 cherry-pick 期间存在。


我们要清楚的知道，在这么多xxx_HEAD 中，HEAD 指向的永远都是当前提交。而且 Git 的很多命令,在不给参数的情况下，默认操作都是 HEAD 指针。例如最前面我说的 `git show` 和另外一个很强大的命令 `git reflog`。

# 尾篇

 没什么要说的了，但是一定要有个尾篇，让这篇博客的结束优点仪式感～


 ---

 刚刚开通了个人微信公众号，最新的博客，好玩的事情，都会在上面分享，欢迎关注，让我们一起学习和成长。

 <div  align="center">    

 ![微信公众号](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/qrcode_for_gh_e8f891ce77fb_258.jpg)

 </div>
