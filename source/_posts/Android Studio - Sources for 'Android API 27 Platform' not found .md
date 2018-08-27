---
title: AndroidStudio-Sources for 'Android API 27 Platform' not found
date: 2018-08-17 14:20:00
tags:
---


今天是七夕啊，难道要和源码一起度过了吗？

<!--more-->


# 问题描述

今天从 Android Studio 中点击SDK中的类，发现查看不了源码，并有如下提示：

![](http://oriwplcze.bkt.clouddn.com/AndroidStudio-SourceCode1.png)

大概是因为前几天我清理磁盘空间，不小心把已经下载的源码给清理了。


# 解决方法


###Step1.下载源码

 通过 SDK Manager 可以查看和下载源码包。

Android Studio 会根据 compileSdkVersion 的值去加载对应版本的源码包。所以，源码包选择下载的版本和编译版本 保持一致。

![](http://oriwplcze.bkt.clouddn.com/AndroidStudio-SourceCode2.png)

###Step2.关联源码

找到 jdk.table.xml,找到源码相应版本的 < sourcePath>标签,把源码路径写进去就可以了。


![](http://oriwplcze.bkt.clouddn.com/AndroidStudio-SourceCode3.png)


- 源码路径：    
~/your-sdk-path/sources/

- jdk.table.xml 地址：
  - **Mac:**              
     ~/Library/Preferences/AndroidStudio{version}/options/jdk.table.xml
  - **Windows:**   
 C:/Users/AndroidStudio{version}/config/options/jdk.table.xml

	其中 AndroidStudio {version} 要换成具体版本。

	例如我的是: ~/Library/Preferences/AndroidStudio3.0/options/jdk.table.xml


### Step3.重启 Android Studio

最后的一步，交给万能的重启。哈哈，重启是为了让 Android Studio 重新加载配置。





---
<center>
<font color=gray>欢迎关注博主的微信公众号，快快加入哦，期待与你一起成长！</font>
<img src="http://oriwplcze.bkt.clouddn.com/qrcode_130.png" width="130" height="130" />
</center>
