---
title: Git三大特色之Branch
date: 2017-10-26 14:14:09
tags:
---

这是一篇写了好几天的诚意之作
<!-- more -->

# 我习惯每篇博客都有个开篇

还记得 Git 系列第一篇 Git 自我介绍的话吗？其中有 Git 自己都赞同的三大特色
> cheap local branching, convenient staging areas, and multiple workflows

轻量的本地分支, 方便的暂存，以及多工作流。其中因为有分支的存在，才构成了多工作流的特色，所以 Branch 不愧为 Git 的王牌特色。这篇博客，主要和大家一起学习一下轻若鸿毛，帅到炸裂的分支儿。

# Branch 的概念

分支的概念，在我看来，分两个版本：

**从使用场景上解释，是这么个概念**

Git 的分支，就是开发过程中，要选择的一条路，你可以选择和其他小伙伴一起走同一条路，也可以自己走一条路，路与路之间相互没有影响，作为路的主人，你也随时可以让两条路合并。  

![简笔画 Git 分支](http://oriwplcze.bkt.clouddn.com/5ed60a497b23ce3d7c07f58be2101119.png)

**深入一点的话，是这么个概念**：  

Git 的分支，其实本质上仅仅是指向提交对象的可变指针，这个可变指针，指向路的终点。同时，还有一个比较特别的 HEAD 指针，用于记录当前工作的位置，借用上面的例子，这个 HEAD  指针等于在路上走的你自己，你在哪，指针就在哪，你在哪个分支，HEAD 指针就指向哪个分支的指针。
![深层次的分支概念图](http://oriwplcze.bkt.clouddn.com/f299bd7aeadd9d4b6a57dd5134b8b69a.png)

实际上，当我们使用 Git 的时候，我们就使用了分支，因为 Git 的默认分支名字是 master，如果你有心的话，会发现执行 `git init`后，命令行的输出头部已经默认在 master 分支了。 但是这个时候，还并未创建 master 分支，只有当有一个提交的时候，才会创建 master 分支。原因在于，分支的指针要指向提交的呀，突然明白了，当初看 Android Studio 的教程，为什么每个都让有一个初步提交了呢。

无图无真相，不信的看下面:

![git 默认分支 master](http://oriwplcze.bkt.clouddn.com/394ad5a842d71bf61fef4f5d856106d0.png)




# 玩转 Branch 必备技能

有关分支的命令不多，无非是换着花样的增删改查，掌握好以下基本的命令，以后就可以在 Branch 的草原上策马奔腾潇潇洒洒啦

### 创建分支

```
 git branch <name>
```
这个命令看起来，似乎简单到你只需要想个分支的名字就好了。  
但是在创建分支的时候，要想下，是否要从当前分支的内容基础上去开辟一条新分支。


### 查看分支

三个命令，让你想看什么分支就看什么分支，就是这么方便
```
 git branch //查看本地分支
 git branch -r //查看远程分支
 git branch -a //查看本地和远程的所有分支
```



### 删除分支

当本地分支删除后，推动到远程仓库后，远程仓库并不能自动删除远程分支（原因，下回分解）。所以，分支的完全删除是分两个部分的，一个是本地，一个是远程。

本地删除操作需要加上 `-d`或者 `-D` 参数，参数的名称来自英语 `delete`的缩写，Remember it so easy!

两者的区别在于`-D`比`-d`要粗暴一点。当被删除分支有新内容没有被合并的时候，使用`-D`，会直接删除， 使用`-d`，会提示该分支有新内容没有被合并，不执行删除。删除需谨慎，建议非特殊情况下，使用温柔的`-d`要好一点，以免小手一抖，眼泪长流。

```
 git branch -d <name>
 git branch -D <name> //强制删除

```

删除远程分支需要 push 操作。

```
git push origin :<name>
```


### 重命名分支
其实，这是个伪命题。  

但是有很多人，包括我，都有过对分支名称不满意想该修改一下的想法，所以，就有了这个伪命题的存在。

事实上，分支不存在重命名，没有 rename 的这个命令。如果你起的名字不满意，想重新起的话，那只要创建一个和要修改分支内容一样的分支，起上你喜欢的名字，然后再把之前的给删掉。


### 检出分支

这个检出分支的“检出”二字，算是个关于 Git 分支的专业术语了,你可以理解为切换当前分支。   
本质上， checkout 操作是移动 HEAD 指针，将 HEAD 指针指向要切换的分支的指针处。   

使用场景有两个：   
1. 已经存在的分支，现在要切换过去。
```
 git checkout <name>
```   
2. 创建一个新分支，并切换到新分支，这个一步到位的话需要 `-b ` 参数。

     以当前分支为基础，创建一个新分支

    ```
    git checkout -b <branch name>
    ```
    以指定的某一个提交，创建一个新分支

    ```
    git checkout -b <branch name> <SHA1>
    ```


### 合并分支

以上，是分支的增删改查独立操作，但是 Git 创造这个分支，并不只是为了让它们自个儿和自个儿玩的，还需要它们之间的相互协作和配合。  就像写项目的时候，分好开发任务，你和你的小伙伴新建了两个分支，你写你的 Anglela，他写他的 baby,到开发完成之后，肯定要合在一起，才能成就 Anglelababy。合的这个动作，就涉及到了分支合并的概念。

分支合并使用到的命令是

```
git merge <branch name>
```

分支合并相对分支的其他操作，是相对要复杂一点的，怎么说也是多分支操作，至少要对得起它一听就比较高端的名字吧，于是我决定把分支的合并作为一个大标题。

# Branch 合并是大事
#### git  的两种合并模式
分支的合并是非常智能的，目前有两种模式，两种模式的选择，不需要我们参与，而是 Git 根据分支情况不同，自行判断选择的。在我使用 Git 的过程中，执行分支合并，有时需要输入提交信息，有时不需要，作为小白的我懵的不知所以然，后来才知道是因为合并模式的问题。

两种模式是：

1. Fast-Forward（快进式）（PS:这个名字是官方的）
2. Recursive Strategy Merge（策略合并式）（PS:这个名字非官方，我自己起的，有时也叫三方合并式）

- **Fast-Forward（快进式）**  

![](http://oriwplcze.bkt.clouddn.com/324432d185358af6518b4a650bc981bd.png)


如图，有两个分支，master 分支和 feature 分支。当这两个分支处于上面的关系时，当进行合并操作时，就会出现 fast-forward。

原因是；由于当前 master 分支所指向的提交是 feature 分支的直接上游，所以 Git 只是简单的将指针向前移动。 换句话说，当你试图合并两个分支时，如果顺着一个分支走下去能够到达另一个分支，那么 Git 在合并两者的时候，只会简单的将指针向前推进（指针右移），因为这种情况下的合并操作没有需要解决的分歧——这就叫做 “快进（fast-forward）”。

合并后的分支指针位置如下：

![](http://oriwplcze.bkt.clouddn.com/d0ab779b06b91838f5d9c508fc0b5de0.png)




- **Recursive Strategy Merge（策略合并式）**   

这个合并方式，是为补充 fast-forward 而出现的，因为你知道，在项目开发过程中，很多人开发的情况下，出现 fast-forward 的情况并不是很多，很多是类似下面这种。提交历史是分叉的，无法满足执行 fast-forward 的条件：



![](http://oriwplcze.bkt.clouddn.com/27598a97785cab054c679bc3e8d19a48.png)


 因为，master 分支所在提交并不是 feature 分支所在提交的直接祖先，Git 不得不做一些额外的工作。 出现这种情况的时候，Git 会使用两个分支的末端所指的快照（C4 和 C5）以及这两个分支的工作祖先（C3），做一个简单的三方合并,生成一个新的提交（C6）。

![](http://oriwplcze.bkt.clouddn.com/dc48051b9659fb4f5ec7d097f75ef033.png)




#### 实战演练一下

说起来就是一堆理论，我自己都记不住，找个例子演示一下:

```
//创建一个文件夹，并初始化 Git
mkdir GitDemo
git init

//初次提交，创建 master 分支
touch master.txt
git add.
git commit -m '添加master文件'

//从master分支末尾，创建并切换 featureA 分支，并创建一个提交
git checkout -b featureA
touch A.txt
git add.
git commit -m '添加A文件'

//从master分支末尾，创建并切换 featureB 分支，并创建一个提交
git checkout master
git checkout -b featureB
touch B.txt
git add.
git commit -m '添加B文件'

//切换 master 分支
git checkout master

//master 合并 featureA 分支
git merge featureA

//master 合并featureA 后再合并 featureB 分支
git merge featureB

```

博主不想给你说话，并向你投掷了一大串命令，快去敲敲看啊，会看到两种合并模式的！


master 分支合并 featureA 时，是快进式合并：

![](http://oriwplcze.bkt.clouddn.com/43bf2b61c937e26dc9eb3c824ae35bc4.png)


master 分支合并 featureA 后， 再合并 featureB  时，已经不满足快进式条件了，此时合并会触发一个三方合并，产生一个新的提交。所以，执行合并命令，会跳到下面的页面，让我们编辑这个新提交的提交信息，默认提交信息是“Merge branch 'branch name'”. 按 `i `编辑提交信息, `:wq!`保存并退出页面。

![](http://oriwplcze.bkt.clouddn.com/37aceb9e7535ffd8fb58f9a9c30eccb0.png)

合并成功后的提示信息：

![](http://oriwplcze.bkt.clouddn.com/79a77b7ee3730d15c2f081aa2cb559a6.png)



画出上面小例子的分支合并，示意图，如下：


![master 合并 featureA](http://oriwplcze.bkt.clouddn.com/ca195af0af9d30e162c4d9a3a96a68eb.png)




![master 合并 featureB](http://oriwplcze.bkt.clouddn.com/8197401e03adab46deb4c3b33566ced6.png)


# 和平解决 Branch 合并冲突

有人在的地方就有江湖，有分支在的地方，就有冲突。有时候合并操作不会如此顺利。 如果你在两个不同的分支中，对同一个文件的同一个部分进行了不同的修改，Git 就没法干净的合并它们，于是就会发生冲突。

如下，分别在 master 和 featureA ，在 master.txt 文件第一行添加一句话，然后两个分支合并，就会发生冲突。
![](http://oriwplcze.bkt.clouddn.com/75b4d6e9d33af945fd96e4e7a8ef1fe7.png)

冲突提示信息中，指明冲突文件为 master.txt。同时，也可以通过 `git status` 命令，查看冲突的详细信息

![](http://oriwplcze.bkt.clouddn.com/cfb0429b5ff5b85998abc8dc509412fa.png)


需要说明的是，如果遇到冲突的话，git 就无法自动合并了，接下来要靠我们自己手动解决冲突，方法是：
1. 查看造成冲突的文件，修改冲突部分
2. 对修改后冲突文件，执行 `git add `操作
3. 创建一个修改冲突的提交。

先知道一下思路，有个简单的概念在脑子里，接下来，一步一步仔细看～

#### 第一步：查看造成冲突的文件，修改冲突部分

冲突文件 master.txt 如下，git 虽然无法解决冲突， 但是已经帮我们帮到最后了，使用简单的三个符号，标明了冲突的地方，以及冲突的两个分支在该地方发生冲突内容。

![](http://oriwplcze.bkt.clouddn.com/9a217024b190f163bf51dc70475c7a6e.png)


|符号|意义|
|--|--|
|=======|分隔符|
|<<<<<<< HEAD 至 =======  |master 分支中该地方的内容|
|======= 至 >>>>>>> featureA| featureA 分支中该地方为内容|


接下来编辑 master.txt  文件,完成合并，确认之后，把 git 冲突标识符号给删除掉即可。

#### 第二步 & 第三步：修改后冲突文件，add &&  commit
![](http://oriwplcze.bkt.clouddn.com/5b9ee07c5dbcc1fb3d637bbdee9b047a.png)



# 分支回滚， 有后悔路可以走

现实中，难免有些时候，你会有后悔的念头。例如每天我迟到的时候，都会后悔为什么第一遍闹钟响的时候没有起床，但是这个世界，没有后悔路可以走，我只能努力做到明天早起。

但是，Git 中有！因为神奇的 reset 和 revert 命令～，两个命令都可以达到回滚的效果，两者之间的区别以后会专门说，这里我们使用 reset 。

**提前声明：回滚之前，不要忘记做好备份，有备无患呐。**


#### 本地分支回滚：

确定回滚到哪个提交，找到该提交的 commit id,执行以下命令，就好了

```
git reset --hard commit id
```

#### 远程分支回滚

依旧是个伪命题。远程分支不存在什么回滚，要想达到回滚的效果，就是删除之前的远程分支，然后把本地回滚好的本地分支，push 到远程。



```
git reset --hard commit id //本地分支回滚
git push origin :<name> //删除远程分支
git push origin <name> //用回滚后的本地分支重新建立远程分支
```

# 我习惯每篇博客都有个结束语

这篇博客用了我不少洪荒之力，希望对大家理解 Git 分支有所帮助，不对之处还请指出。   
最近 Git 系列走起，下篇博客见！


---

刚刚开通了个人微信公众号，最新的博客，好玩的事情，都会在上面分享，欢迎关注，让我们一起学习和成长。

<div  align="center">    

![微信公众号](http://oriwplcze.bkt.clouddn.com/qrcode_for_gh_e8f891ce77fb_258.jpg)

</div>
