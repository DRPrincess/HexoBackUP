---
title: Git-丢失的 commit 是真的消失了吗？
date: 2018-03-14 10:45:01
tags:
---
当然没有，它只是被挂了起来
<!--more-->


# 丢失的 commit 变成了 dangling commit

所谓“丢失的 commit”其实并没有消失，而是成为了一个 dangling commit（悬挂的提交？有点奇怪的翻译，意思是没有任何分支指针或头指针指向它，于是被悬挂了起来），等待 Git 回收。

而关于 Git 回收，Git 虽然会不定时地自动运行称为 "git auto gc" 的命令，但是这个命令一般什么都不干。如果有 7,000 个左右的松散对象或是 50 个以上的 packfile，Git 才会真正调用 gc 命令。

所以，“丢失的 commit”只是在我们的分支历史中消失了。

我们可以通过下面的命令，查看项目中存在的 dangling commits：

```
//查看“丢失的”对象们
git fsck --lost-found

```

![](http://oriwplcze.bkt.clouddn.com/5c898b9623122b0ba7f559fe1c43ca1c.png)

从结果截图中， 可以看到 dangling commit 的类型说明，以及该提交的 SHA1 值。

但是，我们并不能从长达 40 位的 SHA1 值中看出这是个什么提交，所以需要通过 `git show <commit>`来查看提交的内容

![](http://oriwplcze.bkt.clouddn.com/fe510b470e7e1c5a386f1882f4c60c3a.png)

![](http://oriwplcze.bkt.clouddn.com/9991aa5794bdc6dca896e511ed68f893.png)

不过不是你要的提交，你就挨个多试试，如果刚好是你要恢复的提交，那么，恭喜你，拿到后面的 SHA1 值，就可以根据自己的需求操作了， checkout ,merge,rebase,reset 等命令供君挑选！

# git fsck，初次见面

Git 的命令真的很丰富，平常使用的只是其中一部分，如果不是为了查这个问题，还真的不知道有 `git fsck` 。

官方文档对它的介绍是验证数据库中对象的连通性和有效性。

![](http://oriwplcze.bkt.clouddn.com/5d20877eef1bfc45789ab1cf8256c354.png)

这个命令有很多的查询方法，各自对应不用的查询范围，我看了一下，需要对 Git 的原理有点了解的基础，这里我不就深究了，有兴趣的娃子自己去看 [git-fsck 官方文档](https://git-scm.com/docs/git-fsck)。


# 写在后面

上篇博客，我在结尾留下了我的疑问：
>丢失的 commit 真的丢了吗？没丢的话，在哪里？

这篇博客，就是为解释这一系列疑问而写出来的，希望能帮助到有同样疑惑的小伙伴们，另学识浅薄，如有错误之后，还望指正。

see you next blog~



---

刚刚开通了个人微信公众号，最新的博客，好玩的事情，都会在上面分享，欢迎关注，让我们一起学习和成长。

<div  align="center">    

![微信公众号](http://oriwplcze.bkt.clouddn.com/qrcode_for_gh_e8f891ce77fb_258.jpg)

</div>
