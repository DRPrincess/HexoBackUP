---
title: ADB偶尔的抽风异常-ADB server didn't ACK
date: 2018-01-09 15:50:00
tags:
---


强迫症必须解决 adb 偶尔的小bug,否则浑身难受。
<!--more-->

# 关于错误

使用 adb 命令的过程中，有的时候正常，有的时候会出现以下错误，而且很奇怪的是，有的时候命令行不能用，但是用 Android Studio 还能安装应用也是神奇了。

```
daemon not running.    
starting it now on port 5037    
ADB server didn't ACK   
failed to start daemon  
error: cannot connect to daemon

```

尝试的方法，但是失败了，例如：

使用 `adb kill-server` `adb start-server`均无效。

使用 `netstat -an | grep "5037"` 查看占用，也只有 Android Studio 一个进程。强制关闭重启也无用。

# 原因 & 解决方法

后来查到有人说，是因为  platform-tools 25.0.4 版本有 bug，升级或者降级一下即可。

尝试从 25.0.4 升级到 27.0.1 后，问题解决。虽然无法确认是否是根本原因，但至少到目前为止，adb 错误没有再次出现过。

如果你想要升级 platform-tools ，有以下几种方法：

 1. 使用 Android Studio 中提供 SdkManager 图形化工具升级（推荐使用）。   
 2. 使用 sdk 提供的 sdkmanager 命令行更新。
 3. 手动下载 platform-tools 包，然后去 sdk 目录中自行替换。



### Android Studio 升级 platform-tools

1.Tools > Android > SDK Manager 或点击工具栏中的 SDK Manager 。

2.SDK Tools 中可以看到现在使用 platform-tools 版本。


   - 如果有更新版本，左侧复选框中会显示短划线。选中将复选框变成对勾，就会出现绿色的下载图标。
   - 如果没有新版本，复选框中会显示对勾。 选中取消对勾的话，会出现出现红色的卸载图标。

   因为我目前使用的已经是是最新版本，所以是对勾，但是我在下面的 SDK Tools 中有新版本可以更新，我特意点出来绿色的下载图标，大家可以看一下。


3.如果点击绿色小图标，就可以点击下载更新了。


![](http://oriwplcze.bkt.clouddn.com/f07026cf95ef82c9a36caded91c3930b.png)


### sdkmanager 命令行更新 platform-tools

sdkmanager 是 Android SDK 提供的一个命令行工具，可以查看，安装，更新和卸载SDK中的安装包。位置在 your-sdk-path/tools/bin 中，[官方命令说明看这里](https://developer.android.com/studio/command-line/sdkmanager.html) 。



首先要进入 your-sdk-path/tools/bin 路径中，才能使用 sdkmanager 命令。

如果想更新 platform-tools 到最新的话，需要使用的命令行是：

```
./sdkmanager "platform-tools"  //只更新 platform-tools

./sdkmanager --update //更新所有 SDK 安装包到最新版本

```


下面用命令更新了 Tools 的最新版本：

![](http://oriwplcze.bkt.clouddn.com/b1ed860e7fda74b21d35d8a42d841a2d.png)


### 手动升级 platform-tools

  1.去官网下载新版本  platform-tools 包。[下载地址在这里呢](https://developer.android.com/studio/releases/platform-tools.html)

  2.去 SDK 文件夹中替换 platform-tools 文件夹。

  ![](http://oriwplcze.bkt.clouddn.com/7a3b38112e57b8e8411a2782758e9435.png)

---

  刚刚开通了个人微信公众号，最新的博客，好玩的事情，都会在上面分享，欢迎关注，让我们一起学习和成长。

  <div  align="center">    

  ![微信公众号](http://oriwplcze.bkt.clouddn.com/qrcode_for_gh_e8f891ce77fb_258.jpg)

  </div>
