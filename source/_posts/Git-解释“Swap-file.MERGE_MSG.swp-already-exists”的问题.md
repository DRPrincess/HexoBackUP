---
title: Git-解释“Swap file .MERGE_MSG.swp already exists”的问题
date: 2017-10-30 11:36:00
tags:
---

不规范总是让我遇到一个常人遇不到毛茸茸的小 bug 。
<!--more-->

# 常见又不常见的问题：

合并后，执行 git pull & push & merge 命令操作的时候，都会出现了以下错误提示页面，表示懵到天际。

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/95e1e0d779e7824420350bfbc96019a1.png)



# 问题根源


**问题原因：**    

问题出现的原因很好发现，因为在错误提示中已经说明，但是因为英文太渣，习惯性忽略。。。这真的不是一个好习惯。

当打开 git/MERGE_MSG 文件的时候，发现有 git/MERGE_MSG.swp 文件的存在，并且从时间上来看， MERGE_MSG 比 MERGE_MSG.swp 要更新。

造成这种情况的原因有以下两种可能


（1）另外一个应用正在打开此文件。这种情况，要小心修改，防止出现同一个文件，两种修改版本的出现。完了之后可以选择退出或者更加小心的继续 。

（2）编辑该文件的时候，该文件不正常关闭了。这种情况，如果需要，可以选择 ":recover" or "vim -r project-path/.git/MERGE_MSG" 命令来恢复文件关闭前的修改。

上述两种情况确认完毕后，删除 MERGE_MSG.swp 文件，就可以避免该错误提示。


**关于 MERGE_MSG.swp 文件的说明：**  

 .swp 文件和 git 无关，在使用 VIM 开始编辑某文件时，都会产生该文件对应的 .swp 文件。正常的退出，VIM 会自动删除此类型文件，非正常退出情况下， VIM 不会删除 ，.swp 文件会作为文件编辑状态的内容备份。

 其实多次打开多次不正常关闭，会一直产生 .sw* 文件
![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/2ce2ee83271af9fb6b44b8da5396d546.png)



**问题解决：**  


第一步：回到合并前状态
 ```
 git merge -abort  // 中止合并
 rm .git/.MERGE_MSG.sw* //删除vim 非正常关闭产生的文件

 ```

第二步：重新合并   
合并提交信息页面，使用 `:wq!` 或者 `:q!` 正常退出 VIM ，就能正常合并啦。


PS：   
如果 .git/MERGE_* 文件中 只有 MERGE_MSG 文件的话，不用执行 `git merge -abort` ,直接删除 .MERGE_MSG.sw* 文件就好。


# 错误重现

我这人有点轻微的强迫症，一旦遇到某个没遇到的问题，然后又找到解决方法后，有条件的话，为了安心，我会选择重现一下。


因为我是合并时出错，于是我构造了一个会触发策略合并的分支合并，合并的时候会弹出下面的commit msg 编辑页面（MERGE_MSG文件）。这个页面，我使用 ctrl+z 快捷键非正常关闭。再次执行合并操作，就会引发错误。

![MERGE_MSG文件 编辑页面](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/9a65e381933b29e2d6dd12bcaa50dccf.png)

![MERGE_MSG文件 编辑页面非正常退出](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/e35b85c92442f6e09f10e0ef811bb7cd.png)

![MERGE_MSG.swp 文件报错](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/bdb7654fd17bdf4f5dc3c449e9ee30d2.png)


多次操作的话，会出现的情况：
![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/f09890625aa245fd127eb6482d60e8c3.png)

执行下面的命令，然后再次执行合并,这一次使用 `:wq!` 或者 `:q!` 退出 VIM 就能正常合并啦。

```
git merge -abort  // 会删除 merge_* 文件
rm .git/.MERGE_MSG.sw* // 会删除 .MERGE_MSG.sw* 文件
```


---

刚刚开通了个人微信公众号，最新的博客，好玩的事情，都会在上面分享，欢迎关注，让我们一起学习和成长。

<div  align="center">    

![微信公众号](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/qrcode_for_gh_e8f891ce77fb_258.jpg)

</div>
