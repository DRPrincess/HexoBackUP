---
title: 请回答：Git是什么？
date: 2017-10-16 14:14:09
tags:
---

Git 是什么?你看完知道了
<!-- more -->

# Git 是什么？

不卖关子，直接说重点，以下是 Git 官网上的描述：


> Git is a free and open source distributed version control system designed to handle everything from small to very large projects with speed and efficiency.   
Git is easy to learn and has a tiny footprint with lightning fast performance. It outclasses SCM tools like Subversion, CVS, Perforce, and ClearCase with features like cheap local branching, convenient staging areas, and multiple workflows.

Git 是一个免费并且开源的分布式版本控制系统，旨在快速高效地处理从小到大所有项目的版本管理。

Git 非常容易学习，低植入，高性能。因为拥有轻量的本地分支，易用的暂存区，和多工作流的特点，它超越了类似Subversion， CVS，Perforce和ClearCase的其他的 SCM 工具。

简洁来说，Git是一个分布式版本控制系统。


# 为什么要学习 Git？

没有无缘无故的学习，因为要用到，所以要了解要学习。  

Git 是目前最流行的版本管理工具，而且没有之一，就算你的公司使用的不是 Git ,如果你使用 Github 的话，必定要用到Git。如果这里你说你没使用 GitHub，那么就快去用起来，哥们你错过了很多优秀的开源项目啊不能再这样下去了。目前最火的开源社区 Github ,就是基于 Git 版本控制系统，所以掌握 Git 技能很重要。

因为 Git 很火，现在很多 IDE 都集成了 Git,并且提供一些相关的图形化操作。也有很多z很优秀,专门用来简化 Git ca操作的 Git GUI 工具，例如 SourceTree,Tortoise 等。

但是我想说的是，命令行才是Git的王者操作！

原因是，Git Gui的工具底层也是对常用的 Git 命令进行封装实现的，所以，直接Git命令，才是最灵活的操作，学会之后，你，几乎，无所不能。（此处请想象玛丽苏的说话语气）。另外也不建议从 GUI 开始，不是很利于理解 Git 的内部原理。说实话，我刚接触 Git 的时候，就是从 GUI 入手的，Android Studio 集成的 Git 使用图形页面，傻瓜式使用挺方便，但是我用完什么都不懂，从今年开始命令行之后，才敢在简历-专业技能上加上 Git 一项。

命令行很好学，而且使用起来非常非常地帅。



# Git 安装以及环境配置

第一步 首先随便一个 git 命令看你的电脑上是否安装了 Git

Mac ：Terminal 或者 iTerm2
Windows :(Windows+R) cmd

例如：   
![git](http://oriwplcze.bkt.clouddn.com/0746bfbf47dd418ae9e8488867773719.png)

Mac 系统默认下载了git，Windows系统不会，所以 Windows 用户要自己去 [Git 官网](https://git-scm.com/)下载

第二步 配置环境

Windows 用户：

1.安装官网下载来的 git.exe,一路 next 即可。    
2.右键“此电脑”->“属性”->“高级系统设置”->“环境变量”->在下方的“用户变量”中找到“path”->选中“path”并选择“编辑”，将刚才安装git目录中的 bin 文件完整路径添加进去->保存   
3.重复第一步，验证是否配置成功


Mac 用户：

这个时候你可以去接杯水，因为系统为了做好了一切。



# 学习 Git 前的准备

首先，你要有一个 GitHub 的账号～，真的，没开玩笑。[GitHub注册点这里 ](https://github.com/)

很多人（包括我）刚开始的时候，脑海中都会用这个疑惑，Git 和 GitHub 是什么关系以及这俩货有什么区别？
关于Git，上面我们说过了，是一个版本控制系统，那接下来很有必要来介绍一下 Github 了。

准确的来说，GitHub 是一家公司，位于旧金山，于2008年4月创办，然后这家公司在2008年4月10日，正式成立了GitHub，地址：[How people build software · GitHub](https://github.com/) ，主要提供基于git的版本托管服务。一经上线，它的发展速度惊为天人，截止目前，GitHub 已经发展成全球最大的开（同）源（性）社区。

GitHub 上的代码仓库，只支持 Git 做版本管理，只有通过 Git 才能把代码上传到 GitHub 。

以上就是 Git 和 GitHub 的关系。

而且接下来的博客都是以 GitHub 作为我们的代码仓库。

# 专有名词解释

- SCM （Software configuration management）软件配置管理

软件配置管理(SCM)是指通过执行版本控制、变更控制的规程，以及使用合适的配置管理软件，来保证所有配置项的完整性和可跟踪性。配置管理是对工作成果的一种有效保护。


- IDE （Integrated Development Environment）集成开发环境

IDE是用于提供程序开发环境的应用程序，一般包括代码编辑器、编译器、调试器和图形用户界面等工具。集成了代码编写功能、分析功能、编译功能、调试功能等一体化的开发软件服务套。所有具备这一特性的软件或者软件套（组）都可以叫集成开发环境。

- GUI （Graphical User Interface）图形用户界面

图形用户界面（Graphical User Interface，简称 GUI，又称图形用户接口）是指采用图形方式显示的计算机操作用户界面。
