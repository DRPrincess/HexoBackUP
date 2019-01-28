
---
title: Git-rebase 黑魔法之打磨 commit 颗粒度
date: 2018-02-27 18:25:01
tags:
---

 又是一个 rebase 黑魔法篇
<!--more-->

# 写在前面

今天的主题是 rebase 的第二个黑魔法-交互式 rebase,与 rebase 用做两个分支见的遍及合并不同，交互式一般用于同一个分支中的提交整理。从命令上看，两者是 ` rebase ` 和 ` rebase -i `的区别。

需要特别说明的是，` rebase -i `的 GET 也会让你的帅气值+10～

# rebase -i 开启黑暗世界

`rebase -i`叫交互式 rebase ,那它交互的是什么呢？是提交。

`rebase -i` 的第一步，就是选定交互的提交，或者说提交们，这是一个范围。

 例如下面的命令，就是选定[当前提交,当前往前数三个提交)，这是一个左闭右开的集合。
```
git rebase -i HEAD^^^
```

一个 enter 按下去，就开启了黑暗世界,进入了 rebase 交互的页面，如下图：

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/83501cee8c23ee4b71e878eed2fcb2d9.png)

从这张图中，你可以分两个部分看：  
1. 主要内容：选定的提交们，以及对它们的处理。  
2. 带`#`的注释内容：关于对这些提交你能操作的命令，理解了它们，就可以开心地为所欲为了。

先看注释内容，理论知识大概掌握先：

**p,pick**   
使用该提交，也是默认操作，这个从上面的编辑区域可以看出。这个命令的含义是拿到这个命令，但是什么都不做。  

**r,reword**  
拿到提交，修改提交的提交信息。

**e,edit**   
拿到提交，修改这个提交的内容。使用这个命令的时候，rebase 操作会停在操作提交处，等待修改完毕，使用`git add .`和 `git commit --amend`修改提交，`git rebase --continue`继续 rebase 进程。

**s,squash**   
这个命令很厉害的，可以将使用这个命令的提交与它的父提交融合为一个提交。

**f,fixup**   
和 squash 命令的作用一样，不同的是，squash 命令会把融合的提交的提交信息都保存融合后的提交信息中，但是 fixup 会放弃被融合的提交。

**d,drop**  
删除提交

**x,exec**  
这个不常用，我不是很懂，你若是有兴趣，可以自行研究。


知道了命令，现在看怎么用：

第一步：编辑操作命令  

确定要操作的 commit,确定操作命令后，将目标提交的 `pick` 修改成成对应的操作命令。不需要处理的默认 `pick`就好，不要删除，删除的意思是要移除这个提交，和 `drop` 是一个意思。

需要明白的是，编辑区默认的提交顺排列序是按提交历史上的顺序排列的，由前到后，顺序执行，所以顺序不要颠倒。不然你想想，改完最后一个提交之后，再修改第一个提交，历史都散开不能串成一串了。

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/23085c5bc1ef65b12f3799eb74e1ae14.png)

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/fbdcb9e57e16df5c30b84888ba7076d8.png)


第二步：确定执行  

保存退出交互页面就开始执行 rebase 了。顺利的话，直接执行成功，发生冲突，解决之后，执行 `git rebase --continue`继续进行，直至执行成功。

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/ce9168ec8f5e99dde4b8c6773c6d908d.png)


# 小例子：用 reword 和 fixup 打磨提交颗粒


关于 commit 的颗粒度，提交频率高，就细一点，例如修改了一个文件就打个提交；提交频率低，就粗一点，例如这整个模块完成了，才有一次最终版本的提交。关于颗粒度的粗细把握，见仁见智，这里不做讨论。我想说的是，如何去形成你的颗粒度。

因为真实开发中，我看到太多没有一点意义提交了，放张我刚翻出来的截图，这是远程仓库中一个项目的部分提交历史。

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/8aaa34dd181cc78e312824a7bc4808c2.png)

我几乎能想象出当初娃子写推送的烦躁感，虽然很好玩，但是这个提交毫无意义，看的人，根本不知道你提交了些什么，非常不利于后期维护。合理的做法是，推动在远程之前，现在在自己的本地分支上把提交历史理清楚。


例如下图，就可以用 `reword `和 `fixup` 命令完成从前到后的变化。

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/1dc954ff48cc92c871c8baa574c8211c.png)

下面开始构建测试场景
```
// 切换到GitDemo目录下,并初始化Git
cd .../GitDemo  
git init  

//创建初次提交 C1
touch rebase.txt
git add .
git commit -m '初次提交'  


//创建提交 C2
git checkout -b develop
touch A.txt
git add .
git commit -m "详情页布局完成一半"


//创建提交 C3
touch B.txt
git add .
git commit -m "详情页布局结束"

//创建提交 C4
touch B.txt
git add .
git commit -m "修复了2个bug"

```

分析：
1. 改动范围有 C2，C3，C4，确定使用 `HEAD^^^`
2. 要达到如上场景，要把 C2 和 C3 合并为一个提交 C5，`fixup` 和 `squash`满足要求，但 C5 的提交未保存 C3 的信息，那确定 C3 使用  `fixup` 操作。
3. C5 的提交信息 C2 和 C3 都不同，那么 C2 需要先把提交信息改成合并后的提交信息， C2 需要 `reword` 操作。
4. C4 的提交信息修改，C4 需要 `reword` 操作。

```
//开始 rebase -i
git rebase -i HEAD^^^
```

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/3d90dfdc1505939e060f788c69d1bef3.png)
![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/7540658dc8ed1940d6f43c6735020635.png)

执行成功，现在通过 `git log`查看操作分支的历史，和操作前的对比，确认已经成功整理了 commit 历史，大功告成！同时也注意到 `rebase` 后的提交 SHA1 值发生变化，证明是一个新的提交。

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/505fca382c3e617cf411e0045fd53776.png)


# 施黑魔法的时机是什么？

上面演示了 `reword` 和 `fixup`，其他的你可以自己试试，漫漫开发中生涯中，说不准什么时候就用到了，施黑魔法的时机有很多，例如：

- 之前的提交信息有个错字，你的强迫症犯了（reword）
- 发现之前有个提交中的修改，有点问题，但是不想打个比较没意义的补丁提交（edit）
- 等等

更多的场景等你发现。


# 写在后面

其实 rebase 的用法还有很多，而且个个都是堪称为黑魔法级别的操作，例如还可以将一个 commit 分离为俩的，真是跪服了！建议没有需求就不要骚操作了，特别是在工作项目上，有点危险。汝若有兴趣，可轻移莲步到 [git-rebase 官方文档](https://git-scm.com/docs/git-rebase)。

合并为线性历史和整合提交历史，是 rebase 比较实用两个功能，可以保持一个清晰的提交历史，并让每个提交都有意义，在代码的长期维护上有好处。

我的做法是：在私人的功能开发分支使用 `rebase -i` 保持颗粒粗细合适提交，使用 `rebase`合并到 develop 分支，保持简洁线性历史，但是 develop 合并到 master,使用 `merge`，并加上 `no-ff` 可以看到合并操作的记录。暂时的功能开发分支没必要保持合并记录，长期分支合并操作记录还是非常有必要的。


---

刚刚开通了个人微信公众号，最新的博客，好玩的事情，都会在上面分享，欢迎关注，让我们一起学习和成长。

<div  align="center">    

![微信公众号](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/qrcode_for_gh_e8f891ce77fb_258.jpg)

</div>
