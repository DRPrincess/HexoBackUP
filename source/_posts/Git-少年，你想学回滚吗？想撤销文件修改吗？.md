
---
title: Git-少年，你想学回滚吗？想撤销文件修改吗？
date: 2018-03-11 22:40:01
tags:
---
哎呀呀，夏天，哪里凉快滚哪里，冬天，哪里暖和滚哪里

<!-- more -->

# 写在前面

林俊杰有首歌《可惜没如果》,道尽后悔的遗憾，但是万幸，在 Git 中你可以拥有如果,用 `reset`、`checkout` 和 `revert` 可以用来撤销当年那些错误的决定。


# 带着 Git 三大区的概念去阅读

来，看下面这张图复习复习 Git 三大区的概念，这个概念即将贯穿今天这篇文章，理解很重要，不太理解的小伙伴可以先去我的另一篇博客 [《 Git三大特色之Stage(暂存区)》](http://blog.csdn.net/qq_32452623/article/details/78417609)学习下先，等你哦。

![git 数据流程图示意图](http://oriwplcze.bkt.clouddn.com/2429e4d2661e60027537aea0077f6e40.png)

# 吃第一颗栗子，理解一下什么叫做回滚

有一个Android 开发工程师，他叫小明，他正在 feature 分支上开发，他的 feature 分支历史如下图所示，一切都很正常，按照计划进行，这时他接到一个需求变更通知，说“用户积分模块，这个版本不要了”，这真是一个悲伤的消息，小明要赶紧改回来。

![](http://oriwplcze.bkt.clouddn.com/4ba9d150f15cad53e1e7c03f85b29ba1.png)

如果小明不会回滚历史，需要人肉操作去找有关 “用户积分模块” 的代码，然后一个个注或删除释掉，这个更悲伤了，我猜小明可能会哭。

![]()
 <img src="http://oriwplcze.bkt.clouddn.com/a2f4dacda721f1076b50ec57f9c53eeb.png" width="30%" style="text-align:center;"alt="还在路上，稍等..."/>


如果小明用的是 Git,也知道怎么回滚，他只需要找到回滚的终点提交，然后一个命令就可以开始回滚操作。

![](http://oriwplcze.bkt.clouddn.com/eef7ad56aeb25780f56ca174817e9754.png)

![](http://oriwplcze.bkt.clouddn.com/630aded19c1e67485a03058b1a550712.png)

如果小明是个老手，他会先新建个分支做备份，然后再回滚，那也只需要两条命令：

![](http://oriwplcze.bkt.clouddn.com/d55b818b1c8b91db730067767416ce06.png)

以上完美解决此时的需求变更，小明很开心。

![](http://oriwplcze.bkt.clouddn.com/da57beece1716966443daaedd14b8544.png)


# 拿起你的栗子，来认识 reset 的三种模式

上面的例子中，我们使用的命令是 `git reset --hard  <commit-id>`，其实 reset 有三种模式：
- --hard
- --mixed（默认模式）
- --soft

三个参数对应的是不同的作用范围，这个范围和开篇说的 Git 三大区息息相关，因为这个三个模式就是因为有三个分区才存在的。

![](http://oriwplcze.bkt.clouddn.com/493ba44224a5e1b32703cf063a7f5979.png)

实际使用中，你可能会遇到各种千奇百怪需求的的撤销操作，理解好这三种模式的区别，使用起来倍爽。下面列两三个你肯定会遇到的场景，然后用 reset 的不同模式。可以轻松解决问题。

> 小明写代码ing;   
  同事说“小明，你快拉取一下仓库的最新代码，blabla...”    ;  
  小明开始“git pull” ;  
  git pull failed ,原因是暂存区有未提交的修改，这些修改可能会被覆盖;    
  小明确定不会被覆盖，不想创建一个新提交，还想保存修改内容 ;   
  请问：该怎么办?

```
//将暂存区的内容与提交历史同步
git reset

//然后再拉取                               
git pull xxx  

```
> 小明刚创建了一个 commit；    
  然后发现那个 commit 中有个小错误；        
  但是为了维护美好的分支历史，不想因为这个小修改再创建一个提交；        
  请问：该怎么办?


```
//将此提交撤销，但是不修改暂存区和工作区的内容
 git reset --soft HEAD^  

//修改好小问题

//重新提交                      
git add .
git commit -m 'xxx'

```

其实，这是使用 `--soft `的解决方案，还有一种更方便的方法，不用撤销也能完成，`git commit --amend`追加提交：

```

//修改好小问题

//重新提交                      
git add .
git commit --amend`

```


上面的例子，希望给帮你更加清楚的理解 reset 三种的模式的区别，在使用 `reset --hard`的时候，要小心一点，因为这个操作会彻底地重置工作区，放弃修改，使用之前，要十分确定未保存的修改要放弃，不然重置后有可能找不回来了哈。

下面给小伙伴们介绍另外一种撤销方式 `revert`,是一个神奇的命令。

# revert 是一种抵消式撤销

revert,翻译是“反转”，一个比较难理解的词，快来解开非凡想象力的封印。

想象一下，提交中添加了一个A.txt, revert 创建了一个删除 A.txt 的新提交，两个提交同时存在，于是，A.txt 等于从来没有存在过，等于撤销了原来提交。

这像一个互为正负的关系 `1+（-1）= 0`。

第一个例子，小明如果用 `revert` “撤销积分模块”，就要这样用了
```
//将 head^^^到head 范围内的提交反转
git revert head^^^..head

```


![](http://oriwplcze.bkt.clouddn.com/7d4913f29fc3f0d1c08a37f60ef28264.png)

`git log` 看一下操作后的分支历史，不仅没少，反而多了，增加几个反转提交。

![](http://oriwplcze.bkt.clouddn.com/9fe25ca56846ed14eb28ff9aab67ae78.png)

这是 revert 第一个例子中场景的使用表现，也许你会疑问：
>有了 reset,为什么还要有 revert ？

这是因为，在下图情况下，分支顶端还有要保存的提交，reset 无法使用，只能用 revert 将中间的提交反转。

![](http://oriwplcze.bkt.clouddn.com/d5ca3d2e97cebf297f0b07463522c226.png)

如果不想要新增的反转提交污染分支历史，你也可以选择发射你的黑魔法 `rebase -i` 将中间的提交施加 `drop` 操作,Git 真的无所不能，只要你会。


# reset VS revert ,Ready? GO!

由于 `reset ` 和  `revert` 的作用都是撤销提交，所以很多人很自然而然地将两者对比。我就是那很多人之一，而且我还特别无聊的，画了对比图，如下：


![](http://oriwplcze.bkt.clouddn.com/6d6245216ee25a6f6872679d63a82b5a.png)

可以很明显的看出两者对分支历史改变区别：

- `reset` 回退了历史
- `revert` 创建反转提交，增加了历史

从数据安全上角度，`revert`比`reset`安全，因为它的操作可以回溯，反转了还可以倒回来。`reset`比较彻底，是直接丢弃了,不过可以考虑想第一个例子中创建一个备份分支来保证安全。

从分支历史的长期维护角度，`reset`的历史比较干净，`revert`的反转提交没多大意义，毕竟很少有需求让你滚来滚去的。

在被撤销提交，不再分支顶端的场景上，`reset` 无法使用，`revert`可以做到,。

以上对两者的区别讨论结束，没有总结，看你个人喜好和需求场景使用。

# 撤销的不是提交，而是文件
上面给大家介绍的都是关于 commit history 提交历史的撤销，但是有时候会出现撤销单个文件修改的场景，例如，我就经常写了很多代码之后发现某个想法不可行，那个时候已经写了很多代码了，`Command+Z`都撤不完了，手动改太麻烦还容易出错，怎么办？

这个时候就需要撤销文件的修改了，下面就给大家介绍一下，如何进行文件撤销。

## checkout 的第二命令格

`checkout` 这个命令，我们对它太熟悉了,切换分支，检出提交，检出 Tag 标签等等各种对 HEAD 指针花式操作，但是看下，[git-checkout](https://git-scm.com/docs/git-checkout) 官网文档的定义：

![](http://oriwplcze.bkt.clouddn.com/ac2e827db1cb494f943043c2c1b948b2.png)


是不是很惊讶，还可以被用来还原工作区的文件！  

其实这个命令就近在我们眼前,运行 `git status`,会看到工作区和暂存区的修改，如果工作区有修改存在，Git 就会提示,可以通过 `git add <file>` 来加入暂存区以备下次提交，如果放弃修改，就执行 `git checkout -- <file>`。

![git status中的 checkout](http://oriwplcze.bkt.clouddn.com/7210fbe7a8d398710dab5d59977d3298.png)


所以，要是哪次想撤回修改，恢复到上次 add 时内容 ，除了手动 `Command+Z` 或  `Ctrl+Z` ，还可以是使用 `git checkout -- <file>`，改动较多时，简直一步到位！


## reset 的新操作

接着上面，工作区`1.txt`的修改我想下次提交，于是执行了 `git add .` 加入了暂存区,这个时候运行 `git status`，又发现 reset 命令的新操作 `git reset HEAD <file>`。

![git status 中的 reset](http://oriwplcze.bkt.clouddn.com/bff8db5752af1613d5e2953870c60c56.png)

看注释，这个操作可以将某个文件的修改从暂存区拿出去。在错误 add 不想提交的文件时，这个命令还是很有用的。


# 写在后面

好了，以上是本博客的全部内容，希望能帮助大家理解和学会提交历史的撤销和文件文修改的撤销。

下篇博客会讲一个特别治愈的命令 reflog，和 reset 配合可以被用来恢复数据，包括已经撤销的，敬请期待，并欢迎关注我的专栏 [《帅气的 Git 操作》](http://blog.csdn.net/column/details/17721.html)，里面有开始学习 Git 到现在的全部文章。

see you next blog！


---

刚刚开通了个人微信公众号，最新的博客，好玩的事情，都会在上面分享，欢迎关注，让我们一起学习和成长。

<div  align="center">    

![微信公众号](http://oriwplcze.bkt.clouddn.com/qrcode_for_gh_e8f891ce77fb_258.jpg)

</div>
