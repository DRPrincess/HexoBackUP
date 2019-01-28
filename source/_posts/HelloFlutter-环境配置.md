---
title: Hello Flutter! 哎！你环境配了吗？
date: 2019-01-29 18:18:18
tags:
---
小年夜快乐
<!--more-->

# 前言
技术的更新迭代越来越快，一直都有原生开发被取代的声音，作为一个纯原生开发者来说，跨平台开发，是一种新的尝试。为什么要选择 Flutter ，因为毕竟是谷爹的亲儿子, Android 也是亲儿子，说起来也都是兄弟，加深下兄弟之间纯真的友谊，还是很有很有必要的。

# Flutter 是什么？

Flutter 是谷歌的移动 UI 框架，可以快速在 iOS 和 Android 上构建高质量的原生用户界面。 Flutter 可以与现有的代码一起工作。在全世界，Flutter 正在被越来越多的开发者和组织使用，并且 Flutter 完全免费、并且开源，对开发者十分友好。

上面是我从官网上摘抄来的，总结来说，我只记住了三个关键词：
- 热重载
- 跨平台
- UI框架

相信接下来学习中会逐渐体验这几点。接下来开始第一步配置 Flutter 开发环境。

# 准备 Flutter 开发环境
 Flutter 可以选择纯命令方式开发，也可以选择 IDE ，IDE 可以使用 Android Studio，Xcode，VScode 等等，毕竟使用 IDE 开发比较直观，所以我选择了已经安装的 Android Studio。但是无论是那种方式开发，都需要下载 Flutter SDK，所以配置的步骤为：
 1. 下载 Flutter SDK。
 2. 配置 Flutter 环境变量
 3. IDE 安装 Flutter 相关插件

## 下载 Flutter SDK
由于在国内访问有时可能会受到限制，Flutter 官方为中国开发者搭建了临时镜像，大家可以将如下环境变量加入到电脑用户环境变量中：
```
# Flutter镜像 国内用户需要设置
export PUB_HOSTED_URL=https://pub.flutter-io.cn
export FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn
```
## 配置 Flutter 环境变量

```
# Flutter命令行工具
export PATH=<yourpath>/flutter/bin:$PATH

# 注意要将 <yourpath> 换成自己的地址，例如我的：
export PATH=/Users/duanrui/Documents/youzan/flutter/flutter/bin:$PATH

```

## Android Studio 安装插件。

需要安装的插件有两个：flutter 和 dart，安装之后需要重启Android Studio 使插件生效。
![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/5dc2fee074376aa3174b59e15ff85942.png)

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/dbdc84147afb9bccf7ba7305d8fd1c52.png)

安装之后需要重启 Android Studio 使插件生效，生效后开始页面出现 New Flutter project 选项,
![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/747081c96f66bab83b825ba1806dc03b.png)


# 从 Hello World 开始

环境配置好了，现在可以开始编写我们的第一个 Flutter 项目了。点击 New Flutter project ,起个你喜欢的名字，下一步，直到项目创建成功。

项目结构如下：
![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/f9a8300fb2886a9e8d4c1c26ea3ce125.png)

暂时不要管它是为啥这样，运行起来先。

- 选择一个设备，我选择创建了一个 Android 模拟器
- 选择 main.dart
- Just Run！

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/76f6e852c7db140316242eb8811709a6.png)


运行成功后，会在模拟器上看到这样的画面：

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/a5c83b707214a4b5e1111053b23795e0.png)

如果失败了，没有关系，成功过程中不可避免会遇到种种磨难，下面记录了很多人都遇到的几个问题：

 **1.一直在Initializing gradle... ,仿佛卡住了。**

 原因：伟大的墙阻隔了 gradle 的下载。
 解决：将项目依赖的 gradle 版本换成本地已下载的版本。
![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/3506624c0f909a9fda66ad704b5486b3.png)

如果不记得本地已下载的 Gradle 版本是什么，可以通过以下的方法查看：

方法一：打开之前的项目，查看该项目的 gradle 版本配置。
![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/4031021a1917a3b3f4bd89bbcc801c0c.png)

方法二：Android Studio 中新建项目成功后会自动下载项目中定义版本的Gradle，存储在制定目录中，去这个目录就可以看到，有的同学可能有很多个版本，选择其中一个就好了，推荐选择其中最新的版本，比较靠谱。
Mac平台默认下载目录： `/Users/<username>/.gradle/wrapper/dists `
Win平台默认下载目录：` C:\Users\<username>.gradle\wrapper\dists `

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/b4471b087e25c6669a862cb0db2b03e4.png)


**2.一直在Resolving dependencies...，仿佛又卡住了。**
原因：是的，还是伟大的墙，这次阻隔的是下载依赖的仓库地址。
解决：将仓库地址换成阿里云的镜像地址。

```
//        google()
//        jcenter()
        maven { url 'https://maven.aliyun.com/repository/google' }
        maven { url 'https://maven.aliyun.com/repository/jcenter' }
        maven { url 'http://maven.aliyun.com/nexus/content/groups/public' }

```

# 开始体验 Flutter

经过上个步骤，第一个 Flutter 项目已经成功运行了，现在来体验下 Flutter 的几个特色功能：

## 热重载

1.修改 main.dart 文件中一个字符串
![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/3481e7b7abd18f488b3cceaee1ded229.png)
2.保存代码或点击热重载按钮 (就是闪电⚡️按钮)，然后发现模拟器上面的页面也自动变化了，而且是保存着我之前操作的状态，数字仍然为 4，不是初始化的 0。
![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/c11ffe56fae1d93e2d19233111acc8b2.png)

从 Console 窗口中的运行日志中，看出以上操作就是热重载过程。
![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/cabb3f56a488a9fe4cc955da60bb0267.png)

##  跨平台

以上是 Android 设备上的展现，在 iOS 设备 iPhone XR上的运行效果是这个样纸的，看下面：

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/55c0f7ec552cce0279fe601e9326102f.png)

基本上是一模一样的，没有改动任何代码，就达到了两个平台UI和逻辑的统一。

MAC OS 上部署到 iOS设备的步骤：
1. 下载安装 Xcode
2. 配置 Xcode 命令行工具

```
sudo xcode-select --switch /Applications/Xcode.app/Contents/Developer
```
3. 运行以下命令，安装 libimobiledevice，这是一个横跨 Mac,Windows,Linux三大桌面平台的非官方版本USB接口协议库，可以用它来管理连接的iOS设备。
```
brew update   
brew install --HEAD libimobiledevice   
brew install ideviceinstaller ios-deploy cocoapods   
pod setup
```
4. 打开一个 iOS 模拟器，如果顺利， Android Studio 的设备列表已经可以看到刚才打开的 iOS 设备了。
已经安装了Xcode，iOS模拟器不需要另外再安装，命令行打开 iOS 设备的步骤是：
```
//列出你安装的所有可用的设备
xcrun instruments -s
//开启指定模拟器
xcrun instruments -w "iPhone XR (12.1)"
```
5. 选择 iOS 设备，运行项目。

过程中如果遇到其他问题，运行命令行  `flutter doctor -v` 和网络搜索基本都能解决。

## UI框架

Flutter 提供了很多组件，可以很轻松实现很多漂亮的 UI 设计，这一点，相信在后面的学习中，可以慢慢体验到。

# 结语
以上就是全部内容了，Flutter 的基础开发环境已经配置完毕，接下来会通过一些简单的页面小 Demo 去了解更多的知识，有兴趣的可以一起学习哈，下篇文章见。


---

<center>
<font color=gray>欢迎关注「浅浅的开发笔记」，期待与你一起成长！</font>

<br/>
<img src="https://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/qrcode_130.png" width="130" height="130" />
</center>
