---
title: Git-移动记录仪 & 贴心小棉袄 reflog
date: 2018-03-12 23:15:07
tags:
---

reflog 真是个贴心小棉袄
<!--more-->

# 写在前面

上篇写的是数据删除，这篇的主题，就是数据恢复。学会了这俩，可以更放心大胆的去耍了。

# reflog 是什么？

reflog,可以分为两个单词，Reference log，引用日志。当本地仓库中的引用发生移动时，reflog 都会记录下这个移动的行为，跟部移动记录仪差不多。

关于引用是什么？引用的移动是什么？这哥们又是怎么记录这种移动的呢？这些问题先放着，我们先在我的测试项目中，执行下 `git reflog` 来瞅瞅。

![HEAD的 reflog](http://oriwplcze.bkt.clouddn.com/3ab4a43030d044c8a8689a86b601d271.png)

因为做过很多操作，有很多条记录，数据太多，你可能一下看不懂，所以来化繁为简分析一下，单条记录中每个数据都代表着什么？

![](http://oriwplcze.bkt.clouddn.com/695448407a69cfea6916a1ef83b8f65a.png)

1. `41b9778` 是 commit 的 SHA1 值,需要特别注意，它是移动后的 commit。
2. `HEAD@{2}` 是标识这是 HEAD 指针2个移动前的指向内容（从当前往回数第3个操作），也就是上面的`41b9778` 。
3. `commit` 是表示造成这个移动的原因，是进行了 commit 操作。
4. `创建11.txt文件` 表示操作的内容，commit 操作对应的就是 commit message。

单条记录明白之后，再看整体的截图，一目了然。很轻松就知道，我用 Git 到底对这个项目做了什么。

- 引用是什么？
- 引用的移动是什么？
- 这哥们又是怎么记录这种移动的呢？

现在你对这三个问题，有答案了吗？

在上面那副图中，引用指的就是 HEAD 指针；引用的移动就是 HEAD 指针的移动；通过记录操作的命令，操作的次序，操作的内容，操作后的提交信息，来记录这整个操作引起的指针移动行为。

现在我要再问几个问题：
- 引用除了HEAD 指针，还有其他的吗？
- 如果有，它们是什么？会被 reflog 记录吗？
- 如果能被记录，怎么查看？

> Reference logs, or "reflogs", record when the tips of branches and other references were updated in the local repository.

这句话是 [git-reflog 官方文档](https://git-scm.com/docs/git-reflog) 上对 reflog 的描述，很明确的表达记录的是 “branches and other references（分支或者其他引用）”的更新变化。

所以很明显，引用指的不仅仅是 HEAD 指针，还有分支指针。

```
git reflog <ref>

```
 `<ref>` 默认的是 HEAD 指针，如果我们想查看其他的引用，换成对应的内容就可以。


```
 //查看 master 分支指针的移动记录
 git reflog master

 //查看 feature/test 分支指针的移动记录
 git reflog feature/test
```


![feature/test 的 re flog](http://oriwplcze.bkt.clouddn.com/3364f839284078b340e69e84af81209a.png)

![master 的 re flog](http://oriwplcze.bkt.clouddn.com/1fee4f3118e50a25c3c13ebf28171ea8.png)


# reflog 的治愈力

上篇博客讲过，这是一个比较治愈的命令，为什么呢？因为在误操作丢失数据之后，大多能通过它找回来。
例如：
- 误删了未合并的分支
- `reset --hard ` 的时候一不小心` HEAD^`写成 ` HEAD^^^`，历史倒退太多了
- 等等，各种失误 & 只是任性的只是想恢复了

这些涉及到指针移动的都可以，全部通过 reflog 找回来。

恢复的流程基本都一样：

第一步：`git reflog`找回错误操作前的 commit。   
第二步：视你自己的需要，对这个提交做点什么。

下面模拟一下，reset 操作和误删分支的恢复流程，感受一下对误操作造成的心灵创伤，reflog 具有的强大治愈力。

- **恢复`reset --hard `**

1.先模拟一个`reset --hard `操作。
![](http://oriwplcze.bkt.clouddn.com/e6799f4cdea46f40017618e7801336e7.png)

2.找到 `reset` 操作前的 commit
![](http://oriwplcze.bkt.clouddn.com/75a5472eb7af18c62236c66fa282e899.png)

3.`git reset --hard  <commit>` 直接恢复操作前的历史。这一步使用 `git reset --hard  HEAD@{1}`也可以，都是一个意思。
![](http://oriwplcze.bkt.clouddn.com/35dd19b2cdcc0bd6af6ee9e94aa37b2c.png)


- **恢复`git branch -D <branch>`**


1.先模拟一个`git branch -D <branch `操作。
![](http://oriwplcze.bkt.clouddn.com/528915ac59d7c49abe344f137da7fe18.png)

2.删除操作不会导致 HEAD 指针变化，而且删除之后  feature/test 指针也没了，怎么办呢，这个就要从 HEAD 指针的记录里面翻翻看，找到上次在这个分支上的操作记录。删除分支如果是最新操作过的，一般很好找到。

找到 checkout 的操作，很容易分析出，feature/test 的指针指向的提交是 `41b9778`。
![](http://oriwplcze.bkt.clouddn.com/40d5389362a84341d091c31cb6b43f24.png)

3.`git checkout <commit> -b <branch>` 将该提交检出为一个新分支，新分支起名为 feature/test ，就相当于误删分支的恢复了。
![](http://oriwplcze.bkt.clouddn.com/dfe3f32d08cc59163db566b9eb8b4b34.png)


# 写在最后

以上就是本博客的全部内容了，记录的是我自己对 reflog 的理解和使用，希望能带给你们帮助， 如果想知道 reflog 更多的使用方法，小伙伴们自己去看 [官方文档](https://git-scm.com/docs/git-reflog) 呐，我就不一一说了。

另外，在学习 reflog 的过程中，我有了一个疑问：

>丢失的 commit 真的丢了吗？

显然是没丢的，不然谈何恢复，但是它在哪存放着呢？我会一直存在吗？我怎么知道它还在不在呢？

这个疑问，下篇博客解决，敬请关注，see you next blog～
