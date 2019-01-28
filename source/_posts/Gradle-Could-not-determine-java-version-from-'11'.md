---
title: Gradle-Could not determine java version from '11'
date: 2018-10-24 18:18:18
tags:
---

又是一个版本兼容的问题
<!--more -->
# 问题描述

因为换工作，需要新配置的环境，所以遇到了各种问题，例如下面这个：


![Could not determine java version from '11' 错误表现](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/acda272ff79ad83cee4826db1ad0947d.png)


发生错误的相关环境配置

- JDK 11
- Gradle 4.4



如果不知道自己安装的 Java 版本，可以根据一下命令查看：

Mac :

```
#查看版本
java -version

#查看安装位置
/usr/libexec/java_home -V

```

Windows:

```

#查看版本
java -version

#查看安装位置
java -verbose

```


项目的 Gradle 版本，可以根据下面方式查看：
1. <project-path>/gradle/wapper/gradle-wrapper.properties 文件查看：

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/b3a70757eb26c6b68b986da527773185.png)


2. Android Studio->Project Structure 查看：

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/8ea05f8ce520d573ff30eecf0233d29d.png)

# 问题解决

问题核心原因是 Grade 和 JDK 的版本不兼容，因此有两个解决方法：

-  升级 Gradle

   [Gradle 官网](https://gradle.org/releases/) 下载了最新版本 4.10.2,可以解决这个问题。

    ![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/28f52896a26df34e15d76ca1b1cd0afc.png)

-  降级 JDK

   [ORACLE 官网](https://www.oracle.com/technetwork/java/javase/downloads/index.html) 下载 JDK 1.8.0 ,也可以解决这个问题。

    ![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/a5a9be004a93e4b0264a175b59a80b3c.png)


事实情况下，我使用 Gradle 的目的就是管理 Android 项目，而且项目的 Gradle 版本一会轻易修改，所以我采用的是降级 JDK 的方法。大家可以根据自己的情况选择。


<center>
<font color=gray>欢迎关注博主的微信公众号，快快加入哦，期待与你一起成长！</font>
<img src="http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/qrcode_130.png" width="130" height="130" />
</center>
