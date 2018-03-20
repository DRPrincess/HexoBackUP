---
title: Git-叹为观止的 log 命令 & 其参数
date: 2018-03-18 12:12:01
tags:
---

如果你欺负了 log 命令，如果它叫上它的参数过来，然后别硬撑了，直接投降吧，会出人命的给你讲。
<!--more-->

# 写在前面

之前 Git 系列博客中，多次用到 `git log` 去查看分支历史，很多人以为它只有这个用法，事实并非如此，`git log` 只是最基础的用法。

官方文档上对它的描述是：

![](http://oriwplcze.bkt.clouddn.com/ed89fe9d7adbbea2a2a8e46bec0e79db.png)

`git log`的本质是展示提交信息。

但是该命令配合一些参数，可以如同 `git rev-list` 一样控制输出哪些提交和提交的显示方式，也可以如同 `git diff-*` 一样决定怎样显示每个提交的修改内容。

是不是非常惊讶了？另外 `git rev-list`这个命令是什么鬼？我至今未用过，先当作黑盒不管它了。由此可见，关于 `git log` 这个命令我们真的应该重新认识一下它。




# log 这参数啊，真是的是太丰富了。

毫不夸张的说，log 的功能这么牛气，不是没道理的，就这庞大的参数量，不是一般命令能有的。

至于有多少，娃子们可以去 [git-log 官方文档](https://git-scm.com/docs/git-log) 去看下，太多了，我真的截不出来它们的风采。

官方君也知道太多了，也是非常贴心地按功能给分好了类：

- Commit Limiting（过滤提交的）

  提交太多了，不好找，就需要用一些特别的参数帮忙限制一下输出。例如只想找昨天的，只想找小明的，只想修改过A.txt 文件的等等各种只想，只要你敢想，基本都能实现。

- History Simplification （简化提交历史的）

  提交历史是默认显示全部的，如果你感觉没看到全部，不是人没给你，是你的命令行窗口太小了，需要往下翻翻页。如果你只想看某一部分的历史，可以用这一部分的命令。

- Commit Ordering （修改默认提交排序）

  提交历史默认是按时间先后顺序排列的，最新的放在最上面。如果有其他排列需求，或者单纯看不惯，可以用这一部分的参数，去改。
- Commit Formatting （修改默认提交显示格式的）

  每个提交默认的显示信息，就是我们经常看到，有 SHA1 值，提交信息，作者和时间。但是呢，有些人想减去一点，有些人想多一点，有些人想换个显示方式，也是这些参数就诞生了。
- Diff Formatting（控制提交显示文件的差异格式的）

  diff 命令我们之前用过，会显示出修改的具体内容，也就是文件的差异，例如 `--- com/A.txt` `+++ com/A.txt` 。这个类别的命令，就是用来控制提交中显示此类差异的格式的。


看完这些分类的介绍，开心吗？

我是心情很复杂，参数太多，我都有点不太想去学了，看到后面前面的都忘了。于是，这里给大家推荐几个日常工作中比较常用的，提交过滤和提交历史格式化。


# Commit Limiting 用来搜索提交再不过了

## Search-作者

这个命令支持的搜索参数为提交的创建者和提交者，而且是支持正则表达式的，可以发挥的余地很多。

```
//命令格式
git log  --author=<pattern>
git log --committer=<pattern>

//示例
git log --author=“小明”
git log --author=“小明\|小红”
```

这个我的示例项目就我一个，没法试，但是我用公司项目试过，的确可以。




## Search-时间

关于按时间搜索，支持的有很多类型，如下：

```
//某个日期之后
git log --since=<date>
git log --after=<date>

//某个日期之前
git log --until=<date>
git log --before=<date>

```

如果你想要一个具体的时间区间的，可以把这个参数组合起来的,例如下面的命令：

```
//查出 03.12-03.18 期间的提交
git log --since="2018.03.12" --until="2018.03.18"
```

![](http://oriwplcze.bkt.clouddn.com/2822a11f836646aca90c5646d48668d9.png)


## Search-提交信息

这个就厉害了，你可以搜索提交信息,也支持正常表达式

```
git log --grep=<pattern>

//示例
git log --grep='喜欢' --oneline

```
![](http://oriwplcze.bkt.clouddn.com/4eb050fbb8ced1cac878dd4e6c9b68c8.png)

## Search-修改内容



一般情况下，我们想找一个提交，大多是为了某个修改去找，这个修改对应要么是具体的文件，要么是具体的修改的内容。放心，这个条件也支持。

### 文件
```
//文件
git log [\--] <path>…​

//示例
git log --oneline -- 11.txt

```

![](http://oriwplcze.bkt.clouddn.com/16f1a4be8828c0ad6bc232bccd45fa9a.png)


### 修改内容

这一部分其实是 Diff 的参数部分，有很多参数，这里列出两个。

```
//查看某个字符串的变动历史提交
git log -S<string>
//查看某符合某一个正则表达式内容的变动历史提交  
git log -G<regex>


//例子
git log -S"喜欢你" --oneine
```

![](http://oriwplcze.bkt.clouddn.com/a543b009f298e88d21aaf0e782010479.png)

## Search-合并相关的提交 & 文件

工作中，分支之间的合并，往往不是 fast-forword,而是 recursive strategy merge 策略式合并，所以会在历史中出现很多合并提交。运用下面的命令，你可以选择只看合并提交，或者非合并提交。

```
//查看合并提交
git log --merges

//查看非合并提交
git log --no-merges

```

不幸发生了合并冲突，还可以用这个命令，可以快速找到冲突的文件。

```
//查看发生合并冲突的文件
git log --merge
```


# Commit History Formatting  你想要什么格式的你说

## 默认版

这个我们最常用的，显示 commit 的 SHA1 值，创建作者和时间，提交信息。
```
git log

```
![git log](http://oriwplcze.bkt.clouddn.com/65a51c1532526d535bb6f70ae638d374.png)
## 一行情书版

使用 --oneline 参数，only one line !只显示提交的 SHA1 值和提交信息，SHA1 还是缩短显示前几位。

这个命令，在你要根据信息去找提交的时候，比 `git log` 的效率要高点。
```
git log --oneline

```

![git log --oneline](http://oriwplcze.bkt.clouddn.com/3d17a2b90e034dfec01a8819a146b06c.png)


## 毫发毕现版

这个版本,就是上面说的，做到加持 `git diff-*`的效果版本，最常使用的参数是 --stat 和 -p。

```
git log --stat

```
![git log  -- stat](http://oriwplcze.bkt.clouddn.com/daf52822990898b8cb8e760b49fd4742.png)

```
git log -p

```
![git log -p](http://oriwplcze.bkt.clouddn.com/0cb1a5277b74589afb427539d09023f8.png)



## 角色版

这个按提交的创建者分类，因为目前只有我一个操作，就显示了我一个人的。

使用这个命令可以容易地看到谁做了什么，毫无保留的。

```
git shortlog

```
![git shortlog](http://oriwplcze.bkt.clouddn.com/a3b52f009b3a2fa567d0b78fde970f35.png)

## 图文并茂版

这次命令使用三个参数 --oneline， --decorate 和 --graph 。    
--oneline 刚才就是哪个一句话情书的。    
--graph 选项会绘制一个 ASCII 图像来展示提交历史的分支结构。   
--decorate 是用来可以显示出指向提交的指针的名字，也就是 HEAD 指针, feature/test等分支名称，还有远程分支，标签等。

```
git log --graph --oneline --decorate

```

![git log --graph --oneline --decorate](http://oriwplcze.bkt.clouddn.com/0da129d012262c3fee320b1aeccb0593.png)

## 最强推荐版

这个是在 stormZhang 张哥那里学来的：

```
git log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --date=relative

```


![](http://oriwplcze.bkt.clouddn.com/cecbe1dbe030d2420a4ab34c13fb5af6.png)


效果比较炫酷，这个已经属于自定义格式了，git log 支持自定义样式的，有兴趣的娃子可以自己研究下。而且这个命令比较长，娃子们可以通过给这个命令设置别名解决：
```
git config --global alias.lg "log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --date=relative"

```

这样以后直接输入 git lg 就行了。

# 写在后面

这篇博客满足不了您的需求的同学们，自行去官方文档上看，这里再贴一下伟大的官方文档连接：[git-log 官方文档连接在此，您请去吧！](https://git-scm.com/docs/git-log)

最后，see you next blog 。
