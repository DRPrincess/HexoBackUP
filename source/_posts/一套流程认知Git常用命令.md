---
title: 一套流程认知Git常用命令
date: 2017-10-17 17:47:09
tags:
---
# 写在前面
如果只解释命令的用法的话，我想，是非常枯燥，而且没人愿意去看，看了也学不会，学不会就用不了，用不了就.....就没有然后了，所以，我准备模拟一个项目的建立和完整的流程，来介绍一些 git 的一些常用命令。

# 准备工作

还记得，上篇文章说的 GitHub 吗？就是那个全球最大的同性交友社区，不说错了，是最大的代码开源社区。因为Github 是一个用git做版本控制的项目托管平台，所以没有远程服务器的情况下，我们学习Git的话，可以借助 [GitHub ](https://github.com/) 一下。

1.首先，你要有一个 GitHub 的账号～,注册地址[GitHub ](https://github.com/)

2.New repository，创建一个新项目仓库，选择 public类型,其他默认。

![create new repository](http://oriwplcze.bkt.clouddn.com/b8160439ebeaf1c8f2c5b5305908f5f5.png)

3.添加SSH keys，给你的 Github账号添加SSH秘钥对，免密码登录，这步可以省略，但是建议不要。

创建仓库后，会得到两个仓库地址，两个地址的区别是一个使用 https，另一个使用 ssh 协议。

![https](http://oriwplcze.bkt.clouddn.com/121a84ccd2fc9ca7956673dec55270f2.png)

![ssh](http://oriwplcze.bkt.clouddn.com/01373111472fb642f2928485350f755a.png)

两个地址都可以，但是使用 https 地址上上传和下载代码的时候，都会让输入 GitHub 的代码，太繁琐了。这里推荐使用 ssh 协议地址，使用 ssh ，需要在你的电脑设备和 GitHub之间配置SSH秘钥对,公钥放在 Github 上，私钥放在你的电脑本地上，这样使用有私钥的这台电脑，操作 GitHub 就不用输入密码了。

 第一步输入 ssh 命令，看电脑是否安装 ssh 协议，如果没有，去网上搜索下载安装，如果已经安装，会得到下面的结果  
![](http://oriwplcze.bkt.clouddn.com/9f108962b663333ae883eb17f38c107e.png)


然后使用 `ssh-keygen -t rsa -b 4096 `命令，生成私钥对
![](http://oriwplcze.bkt.clouddn.com/bec6a19da8909eae98190bacd95646c7.png)


命令执行完毕，会看到本地生成了两个文件，私钥`id_rsa`和公钥`id_rsa.pub`。

将 `id_rsa.pub` 的内容复制粘贴到 GitHub 中（Setting-SSH and GPG keys-New SSH key）

![](http://oriwplcze.bkt.clouddn.com/0566ff7e724c0d59a72ca13d03961ac4.png)



# Git 命令一日游出发

接下来，我们会在本地电脑上创建一个空文件夹，在其中一个文本文件，并推送到 GitHub 上面新建的 GitStudyDemo 中，一起看看，途中会遇到哪些命令吧。



### git init

本地创建一个空文件夹，名字什么都可以，我的叫 GitDemo，然后命令行进入到这个文件夹内，执行 git init 命令：

![git init](http://oriwplcze.bkt.clouddn.com/b4a3748d2b26c37eba69d7563f05022d.png)

执行完毕，GitDemo 中会生成一个 .git 的隐藏文件，生成这个文件，就说明 GitDemo 已经是个 Git 仓库了。（Mac系统快捷键 command+option+. ，Windows 勾选显示隐藏文件可以显示隐藏文件）

git 对项目版本控制的所有信息，都存储在这个 .git文件中，现在这部分略过，以后运用熟了会逐渐意识到。

###git remote

在 GitHub 上面创建了远程仓库 GitStudyDemo ,在本地创建了 GitDemo 本地仓库，现在我要把它们两者连接起来，需要 git remote add 命令，将 GitStudyDemo 仓库的远程地址 添加到本地仓库中。

![git remote](http://oriwplcze.bkt.clouddn.com/a4736ea2200276760122d785a238a834.png)

添加成功后，使用 git remote -v 命令可以验证一下。同时如果你有兴趣对比下命令执行前后的话，会发现在 .git/config 中已经添加了远程仓库地址。


###git status

接下来，在 GitDemo 中新建一个文件 test.txt,执行一下 git status ，看一下会发生些什么。


![git status](http://oriwplcze.bkt.clouddn.com/39f037df98cb8fe24485562d75afb524.png)


提示信息说 test.txt 是Untracked file（未跟踪文件），很清晰的建议我们执行 git add \<file>


git status 这个命令顾名思义就是查看状态，这个命令可以算是使用最频繁的一个命令了，建议大家操作关键命令之前，都先查看一次，降低误操作概率。

###git add
git add 这个命令的意思是将修改的内容添加都暂存区，等待被提交，打个比方的话，就是已经加入了买票的排队中。
执行 git add 后 再次执行了 git status 来查看仓库的状态，以便和执行前做对比。

![](http://oriwplcze.bkt.clouddn.com/46395e69c9da6765730e900aec242758.png)


提示信息发生了变化，提示Changes to be committed，也可以使用
 git rm --cached 退出暂存区。这里我选择去提交

###git commit

git commmit -m \<your commit message>, 这个命令的意思是，将暂存区的内容，设置一个提交的信息，然后添加到提交历史内容区中，等待被推送到远程仓库。

提交完执行 git status ,显示工作区没有文件需要提交。
![git commit](http://oriwplcze.bkt.clouddn.com/e0d99801ab68c5508a3d6825a1d118b5.png)


###git push & git pull

现在我们要开始把本地的仓库 GitDemo 推送到远程仓库 GitStudyDemo了，使用的命令是 git push 。

![git push origin](http://oriwplcze.bkt.clouddn.com/12074415e0d569e7c2aa26e0ffdca5c1.png)

这里注意，因为我们是第一次创建，远程仓库中什么都没有，所以直接 push 就可以了，但是真实的项目开发中，push 之前都要先操作一下 git pull 动作,把远程仓库中的内容拉取下来，再操作 git push。


###git branch   

分支，是 Git 的核心特色，在团队合作的时候，发挥的尤其出色。一个开发者一个分支，然后在留一个主分支做树干，每个开发者完成之后，将自己分支的内容汇聚到主分支上。关于分支有很多种玩法，接下来开始稍微体验一下。

首先查看一下当前项目有多少个分支：

![git branch](http://oriwplcze.bkt.clouddn.com/1dbf39f2b2b79725fc4aa2322397d253.png)

git branch 查看本地分支，加上 -a 参数可以查看项目所有分支，包括远程分支。 分支名称前 `*` 表示是当前分支。

然后创建一个分支：

![git branch new](http://oriwplcze.bkt.clouddn.com/f24e86d97887d4a13c4cf5f8b5156c99.png)


###git checkout

创建完分支后，切换分支，使用的命令是 git checkout ，注意执行后 `*`号 跑到 test 分支前面了。

![](http://oriwplcze.bkt.clouddn.com/c061c219d5ef1c694bfecf882e9c668b.png)

###git merge
 然后我们在当前分支中在 test.txt 中添加一句话 'Hello Git !' ,然后执行 git add &git commit .   
再使用 git checkout 切换回 master 分支。查看test.txt，发现没有'Hello Git !' 中的内容。再切换回 test 分支中，查看test.txt，发现有'Hello Git !'，是不是很神奇？  
分支用起来就是这么好玩，如果想让 master 中的test.txt 中也有这句话的话，可以选择合并 test 分支。

![git merge](http://oriwplcze.bkt.clouddn.com/f124be55a6bc3462a506d8b3c183be33.png)


# 一日游结束，今天解散，明天见

 上面基本是日常的工作中，出现频率非常高的 git 命令，但这不是全部，因为还有很多很多好用好玩的，在下面几篇文章中，等待着我们，下篇文章见～
