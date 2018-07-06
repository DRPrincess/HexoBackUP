
---
title: Android-使用adb和aapt查看应用信息
date: 2017-11-07 11:06:66
tags:
---

又学会一个命令行的使用姿势～

<!--more-->

# 很日常的一个开篇

想知道一个应用的信息，有很多种方式，但是某些时候，你只有一个手机，手机上安装着目标应用，或者你只有一个安装包的时候，我想，一些小巧的查看方式就显的比较亲切了，例如 adb 和 appt。

adb 和 aapt 都是 Android SDK 自带的工具，adb 位于 sdk/platform-tools, aapt 位于 sdk/build-tools/，如果配置了该目录的环境变量，可以在任何路径下都能使用该工具的命令，如果没有配置，那么就需要切换到该命令工具的路径下才能使用对应命令了，切来切去也不是个事，所以还是去配置一下吧。

# aapt-查看 APK 文件信息

```
 aapt dump badging <apk_path>  //获取全部信息
 aapt dump badging <apk_path> | grep XXX //获取XXX信息

```

例如获取版本信息   
![](http://oriwplcze.bkt.clouddn.com/3e84606a33fce1fc91b3b37f87575bd8.png)

# adb-查看手机安装应用信息
```
 adb shell dumpsys package <package_name>  //获取全部信息
 adb shell dumpsys package <package_name> | grep XXX //获取XXX信息

```

试一下，用这个命令获取版本信息    
![](http://oriwplcze.bkt.clouddn.com/c1136cbdab341f6ed40c20e018323e55.png)

这个命令是需要只要应用的 packageName,如果你不知道想查看应用的完整包名，也不用烦躁，下面这个命令可以帮你
打开要查看的应用，执行下面的命令，可以获取当前应用的包名，以及当前页面所在的 Activity
```
 adb shell dumpsys window | grep mCurrentFocus  //查看当前运行的包名和Activity

```

拿微信试一下：
![](http://oriwplcze.bkt.clouddn.com/3b10b5f52f344b2e1cb528dd8735aeb5.png)



---

刚刚开通了个人微信公众号，最新的博客，好玩的事情，都会在上面分享，欢迎关注，让我们一起学习和成长。

<div  align="center">    

![微信公众号](http://oriwplcze.bkt.clouddn.com/qrcode_for_gh_e8f891ce77fb_258.jpg)

</div>
