---
title: Build Tools & Platform Tools & Tools & platform & Gradle 貌似复杂的依赖关系。
date: 2018-01-11 17:58:00
tags:
---

由 Tools 里面都是啥？ Build Tools & Platform Tools & Tools 三个货区别是啥？一个小疑问促成一篇博客的到来。
<!--more-->


# 写在前面

这篇博客的主题不是很明显，但是等你看完，可以明白两个问题：

-  Build Tools & Platform Tools & Tools 的区别。
-  Build Tools & Platform Tools & Tools & Platform & Gradle 这几个货版本到底是怎么相互依赖对应的。

# 开篇

在安装和配置 Android Studio 的时候，有一个很重要的步骤，就是配置 SDK 路径，那么 SDK 是什么呢？

SDK（Soft Develop Kit），按照我硬翻的风格是是软件开发工具箱，其实，正确的理解是一整套开发工具。例如 Android SDK，就是 Google 提供的和 Android 开发相关的一整套开发工具，不仅包括编译，运行，打包这些必须的，还有很多其他小工具。了解和学习它们，对开发工作很有帮助。

然后就说到这三个文件夹，tools,build-tools,platform tools,首先它们仨都是 Android SDK 提供的文件夹，名字都像，常常傻傻分不清楚。

![](http://oriwplcze.bkt.clouddn.com/6f3b9cc628e905e453bbfc9c2a4cd756.png)

其实我也不知道弄懂它们有什么意义，但是我不想每次都一脸懵逼，于是就有了这篇博客。

# Build Tools

Build-Tools 的内部结构如图。

![](http://oriwplcze.bkt.clouddn.com/026ad2cc7bddb749806e0353b6dd9994.png)


没想到的是，我目前使用的 SDK 中有这么多版本的 Build-Tools。那么问题就来了，

- Build-Tools 是干嘛的？为什么要专门放一个文件夹？
- Build-Tools 为什么需要这么多版本的？只有一个行不行？

这个时候，就要去看谷爹的官方介绍说明了，我认为那里是最准确的描述。

![](http://oriwplcze.bkt.clouddn.com/48d618645de75387457ed5b44d1d28d4.png)


上面说，Build-Tools 是 Android 应用编译和创建过程中所必须的一套工具集合。

开发的时候，我们应该时刻保证我们的 Build-Tools 是最新版的。而且从 Android Studio 3.0.0 开始，创建项目的时候，会根据 Gradle 插件的版本，默认使用版本支持范围中最低版本的 Build-Tools。当然，如果需要更改的话，通过 build.gradle 中修改 buildToolsVersion 参数可以修改。

```
android {
    buildToolsVersion "26.0.2"
    ...
}

```

以上是官方说明。从中，我们可以找到之前问题的答案：

- *build Tools 是干嘛的？为什么要专门放一个文件夹？*  
build tools 是 Android 应用编译和创建过程中所必须的一套工具集合，放一个文件夹的意义，就是我猜是为了好分类吧。

- *build Tools 为什么会有这么多版本的？只有一个行不行？*  
官方建议是最好及时更新，保持最新版本。从这句话可以看出，build tools 只需要保持一个最新版本就好。从 Android 系统整体都是向后兼容的特性考虑，一个最新版本也是足够的。

  至于问什么会有这么多版本，是因为我使用的时候，不是最新版，然后用了不同的版本的 SDK，每个 SDK 有对应的 Build-Tools 版本范围，所以下了很多。


#### Gradle plugin & Build Tools

而且 从我们谷爹说的话中，可以知道 Gradle plugin 和 Build-Tools 版本之间有着不同寻常的关系。

Gradle 是目前比较流行的构建工具之一，Android Studio 中集成的就是 Gradle,并针对 Android 应用开发了插件 Gradle plugin 。在我看来，编译和构建环节本就密不可分，由此猜测二者之间会不会有版本兼容的考虑，查看文档果然如此。

Gradle Plugin 的版本说明中，对 Gradle 和 Build Tools 的版本都要最低要求。

![](http://oriwplcze.bkt.clouddn.com/c87d861724e11a954a582754d75b394e.png)

把最新的几个版本总结了如下表格：
|Android Gradle Plugin	|Gradle|Build Tools
|:----|:----|:----|
|3.0.0	|	4.1+|26.0.2+|
|2.3.0+	|	3.3+|25.0.0+|
|2.2.0+|2.14.1+|	23.0.2+|



[Android Plugin for Gradle Release Notes
](https://developer.android.com/studio/releases/gradle-plugin.html)


# Platform Tools

Platform Tools 的内部结构如图。

![](http://oriwplcze.bkt.clouddn.com/8161680a135e9082a9626a89093dd366.png)

本来我以为会像 Build-Tools 一样有很多版本，然而并不是，只有一个。

看一下谷爹的说明：

![](http://oriwplcze.bkt.clouddn.com/14d1b8cba7288104fac9be7f36a52596.png)

Platform-Tools 是 Android SDK 的一个组件 。内容主要包含与 Android 移动平台交互的工具，例如 adb(android 调试桥，用来和应用通信的),fastboot（线刷，一种刷机模式） 和 systrace(通过这个，可以查看分析系统运行中的所有数据)等。 这些工具堪称 Android 应用开发之必备工具。如果想解锁手机开机程序或者刷机的话，也需要它们。

尽管这些工具中的一些新功能仅适用于最新版本的 Android，但是因为这些工具也是向后兼容的，然后只需要一个版本 Platform-Tools 的就好了。

我们应该尽量保证是最新版本，前几天我的 adb 总是连接出错，升级了版本就好了，应该是新版本已经发现问题并修复了。

### Platform-Tools & Platform

博客最上面的图片中，可以看到 sdk 文件夹内部组成中，有这样两个文件夹，platform tools 和 platform 。从名字上看，就感觉俩货之间肯定是有事。
Platform  内部结构如图。

![](http://oriwplcze.bkt.clouddn.com/deb63b9b1a6ba160d82927ccd16e9952.png)

很明显，这就是不同版本 Android 系统们，以 API 为序号。用户下载了几个版本，里面就有几个版本。build.gradle 中 `compileSdkVersion` 参数对应的就是这其中的版本。

```
android {
    compileSdkVersion 26
    ...
}

```

查看 Platform 的官方介绍，发现 Platform 的每个版本都对 Platform tools 有最低版本要求。

![](http://oriwplcze.bkt.clouddn.com/542bff136f69eb2fc349699f5b48f849.png)

从版本号看，基本保持一致，还是比较好记。例如 platform 25 就要求 platform tools 25.0.1+。

同时，我们也可以看到对 Build-Tools 的版本也要求，列出表格如下：

|Android Version	|API Level	|Platform-tools	|Build tools|
|:----|:----|:----|:----|
|Android 6.0	|23	|23+|24.3.4+|
|Android 5.1	|22	|22+|23.0.5+|
|Android 5.0	|21	|21+|23.0.5+|


[SDK Platform Release Notes
](https://developer.android.com/studio/releases/platforms.html)

# Tools

Tools 的内部结构如图。

![](http://oriwplcze.bkt.clouddn.com/9f2bb4942e0326d97a4f306907cb27f4.png)

Tools 在我的环境中只有一个版本，所以按照这种规律，很明显，开发环境中应该也只需要一个版本的就足够的。

以下是我们的谷爹对它的一句话介绍：

![](http://oriwplcze.bkt.clouddn.com/14034b086a3edf8837900e18554e4dcc.png)

Tools 也是 Android SDK 的一个组件，包括一套完整的 Android 开发和调试工具，Tools 也包含在 Android Studio 中。


### Tools & Platform-Tools

翻翻 Tools 的版本记录，发现 Tools 和 Platform 之间也有对应关系。例如下图，26.0.0 的 Tools 就依赖 Platform-Tools 24+ 。

![](http://oriwplcze.bkt.clouddn.com/8f64b7484cdd07d02aa5905132e43e7a.png)


总结如下：

|Build Tools|	Platform-tools|
|:----|:----|
|24.4.0	|24+|
|24.3.4|23+|
|24.3.3|19+|

[SDK Tools Release Notes
](https://developer.android.com/studio/releases/sdk-tools.html)

# 总结

写到这里，上面已经出现 Tools,Build-Tools,Platform -Tools,Platform,Gradle,Gradle Plugin，名字大多相似，关系也有点交错，很容易晕，总结如下：

1.Gradle Plugin 基于 Gradle,Build-Tools 生成的。
2.Platform 基于 Platform-Tools,Build-Tools 生成的。
3.Tools 基于 Platform-Tools 生成的。

看图更清晰一点：

![](http://oriwplcze.bkt.clouddn.com/dd2642f03b10eb86cb0ff28d0435940f.png)


# 写在最后：

这篇博客的初衷是上篇博客中受启发，想特意学习一下 Tools 文件夹想法的开始的，然而没想到牵扯出来这么多，想来还是因为会的太少了。

写完后，我突然明白了，之前遇到导入一个 Github 上拉取的新项目，然后就各种报错各种需要下载的情况，版本不兼容报错，解决方法是下载对应版本或者改成兼容的版本。

为了避免出现这种情况，要定期更新 Build-Tools ,Platform-Tools 和 Tools,保持较新的版本。

然后，下篇博客见。


---

刚刚开通了个人微信公众号，最新的博客，好玩的事情，都会在上面分享，欢迎关注，让我们一起学习和成长。

<div  align="center">    

![微信公众号](http://oriwplcze.bkt.clouddn.com/qrcode_for_gh_e8f891ce77fb_258.jpg)

</div>
