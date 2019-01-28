
---
title: Git-骚操作-批量删除分支
date: 2018-01-10 18:18:18
tags:
---



# 前言

一个业务一个业务开发过去，少的是头发，留下的还有超多的本地分支。


某一天，我的强迫症突然发作了，我就只想保留当前开发的本地分支，该怎么办呢？当然也可以逐条人肉删除，但是我不是很喜欢，不仅累，还显的我不是那么聪明。
于是，去寻找是否有批量删除的命令，果然，它是有的！

![](https://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu3a89e89b155c76c2aa5fefc84bf72bc0.png)

# 批量删除分支命令的 What & Why？

批量删除分支命令具体格式为：

```
git branch |grep 'xxx' |xargs git branch -D

```

有两个使用上要注意的地方：
-  xxx 要替换成分支名称的搜索关键词。
- `git branch -D `删除命令中的 -D 和 -d 参数要合理使用，避免强制删除发生惨剧。

不知你有没有注意到，这条命令的格式很特殊，不是常规的 Git 命令格式。其中含有 Linux 命令 `grep` ，Git 命令 `git branch -D`，还有陌生的 `xargs`，不常见的 `|` ,我开始好奇，上面都是些啥，以及上面的命令是如何做到批量删除的？


为了解决自己的疑问，我去搜索下相关知识，get了不少小技能，现在记在小本本上总结一下。

首先 `grep`， `xargs`，`|` 三个都是 Linux 提供的命令。


## grep

`grep` 很常见，是以上三个命令我唯一认识的，名称是 global regular expression print（全局正则表达式输出)的缩写，是Linux 提供的一个搜索工具，搭配不同参数使用，几乎可以做到搜索任何东西，文件，文件夹，文本内容，搜索结果的总数等。这有篇不错的文章，想了解的同学可以去看下。[14 个 grep 命令的例子](https://linux.cn/article-5453-1.html)


## KISS 和 | 命令

下面两个命令，需要先说明一下Linux 的 KISS 理念。不要多想，不是么么哒，而是 Keep It Simple,Stupid! 表达的意思是每个命令工具都只做一件事情，简单好用。基于这种理念，Linux 的很多命令都是相互独立的。那真实使用场景中，有很多复杂的事情，需要多条命令协作使用，于是 Linux 提供了管道来完成直接的数据传输。管道的操作符就是 `|` 。

- 基本格式
    ```
    指令1 | 指令2 | ...
    ```

- 作用
    用来连接多条指令，前一条指令的输出流向会作为后一条指令的标准输入。
    ![](https://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu4474395d01f283e49560a41805ca1d3e.png)

- 例1：


    ```
     ls | grep  "Android"

    ```

    执行结果：列出该路径下所有名称包含 Android 的文件

    ![](https://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniub68142785f8d8eb5fefa6faf665a1e8f.png)


- 例2：

    ```
     git branch | grep  "feature"

    ```

    执行结果：列出当前项目所有分支中，名称含有"feature"的分支。

    ![](https://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniua52278ed8e724454ba8f2b993e5de176.png)

## xargs

`xargs` 命令配合 `|` 使用，将前一条指令的输出流向会作为后一条指令的参数输入。
- 基本格式
    ```
    指令1 | xargs 指令2 | ...
    ```

- 作用
    命令配合 `|` 使用，将前一条指令的输出流向会作为后一条指令的参数输入。


    ![](https://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniuda7a758287c697b6c3f0d2a889e6467b.png)


- 例1：

    ```
     ls | grep  "Android" | xargs cat

    ```
    执行结果：输出该路径下所有名称包含 Android 的文件的内容

    ![](https://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu249585820abf8f3e747cc81a45558f43.png)

- 例2：

    ```
     git branch | grep  "feature" | xargs git branch -d

    ```
    执行结果： 找出所有分支中，名称含有"feature"的分支，然后删除。

    ![](https://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu7eb38a2aaac5727d43e47a558b66864f.png)

# 总结

大多时候都是搜索到相关命令直接用就没有后续了，也不知道具体的原理。于是，无知的我，还是第一次清楚的理解 Linux 的管道命令。使用管道组合命令实现批量删除的实现很受启发，以后遇到问题也多了一种解决思路。

学无止境，不能懈怠，新知识带来的愉悦感是不可比拟的，希望我们在每一天都有所成长，下篇文章见。

---


<div align=center>
欢迎关注个人微信公众号，最新的博客，好玩的事情，都会在上面分享，期待与你共同成长。
<div align=center>
<img src="https://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniuqrcode_300.png" width = "300" height = "300" />
