---
title: JSON解析异常- org.json.JSONException Expected x after a key
date: 2017-11-22 22:46:09
tags:
---


破解了一个全角空格引发的血案

<!--more-->

# 问题场景

后端开发好接口，给发过来接口文档，于是开始开心的使用 GsonFormat 插件 建实体了，然而转换 JSON 时出错。

错误现场截图：

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/4d58d873368d1fd254a1fd2d75bc890b.png)

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/750345e68b1fa64deb718ab973fe6800.png)



# 问题分析

JSON 转换失败一般有以下两个原因：

1. JSON 格式有问题，检查一下格式。

2. 格式没问题，仍然报错，那就是编码问题。例如你的 JSON 文件头里带有编码字符(如utf-8等)，读取字符串时 JSON 串是正常的，但是解析就有异常。



很显然我格式没问题，那就是编码的问题了，但是我怎么能确定编码问题呢，于是有了下面神奇的一幕：

出现了问题，我百思不得其解得时候，我去 GsonFormt 这个插件的 GitHub 上创建一个 issue,把我转换出错的 JSON 字符串附上了，然后提交了上去。为了保险起见，提交之后我验证了一下我的问题描述。于是我复制了 issue 上我提交的那段 JSON 字符串，粘贴转换，发现竟然正常转换了，正常了！！！！ 震惊了我狭窄的认知世界。然而我使用接口文档中的同样的一份JSON,转换仍然失败。

虽然上面发生的事情比较显的我比较蠢，但是至少，可以现在定位问题了。目前有内容看起来一模一样的两份 JSON，一个转换失败，一个转换正常。也是我用 UltraEdit 分别以 Hex 模式打开了两份文本，结果如下：

正常转换的文本：

![image](https://user-images.githubusercontent.com/16207639/33135900-665be874-cf69-11e7-9e4a-29991f0ff107.png)

转换失败的文本：

![image](https://user-images.githubusercontent.com/16207639/33136041-cc643590-cf69-11e7-8aeb-ec6fde8da68a.png)


转换失败的文本，发现多了 `Â` 这个字符，仔细看的话，是所有空格转换成了这个字符，于是定位到空格上面，发现全角空格会转换为`Â`。

现在真相大白了，全角空格就是导致 GsonFormat 转换失败的罪魁祸首。

定位编码问题你可能会用到的链接：  
[用UltraEdit：UltraEdit for Mac 安装包、破解文件和破解教程](http://www.jianshu.com/p/7b79a7ca522f)  
[不想用 UltraEdit：Mac 中使用 vi 查看和修改二进制文件](https://www.zhihu.com/question/22281280)

# 问题解决

知道了真正的原因，问题就引刃而解了。

因为我们的接口是 word 文档给的，于是，我全局替换了全角空格为半角空格，之后再复制粘贴转换就好了。



# 福利推荐

最后，给大家推荐一个好用的Android Studio 插件，就是本文出现的 GsonFormat ,可以通过 JSON 字符串自动转换为实体对象，省时省力。超级方便，放个动图感受一下～

![](https://camo.githubusercontent.com/0d45c79c54ab57f6efe31e9019b11d93974fa039/687474703a2f2f75706c6f61642d696d616765732e6a69616e7368752e696f2f75706c6f61645f696d616765732f3136363836362d666639646333333661663732643764372e6769663f696d6167654d6f6772322f6175746f2d6f7269656e742f7374726970)

[戳这里去看 GsonFormat 的 Github 仓库 ](https://github.com/zzz40500/GsonFormat),上面有详细的使用说明，有问题可以搜索 issue 解决或者创建 issue 求助,千万别学我提这种搞笑的乌龙 issue ，笑哭自己。



---

刚刚开通了个人微信公众号，最新的博客，好玩的事情，都会在上面分享，欢迎关注，让我们一起学习和成长。

<div  align="center">    

![微信公众号](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/qrcode_for_gh_e8f891ce77fb_258.jpg)

</div>
