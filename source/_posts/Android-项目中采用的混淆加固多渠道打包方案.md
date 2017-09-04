
---
title: Android-项目中采用的混淆加固多渠道打包方案
date: 2017-08-23 19:56:44
tags:
---


![用打包礼物的心情打包 apk ](http://oriwplcze.bkt.clouddn.com/image/%E5%8D%9A%E5%AE%A2%E5%B0%81%E9%9D%A2/package.jpeg)

# <font color="#008000">前言</font>

当我们万分努力将项目开发完成之后，提交最后一行代码后，会怎么样？
长舒一口气，终于完成了，给自己放一天假，休息一下吧？

不，还没完，作为一个 Android 开发者，我们接下来做的事情还有不少呢。

<!-- more -->

**第一步 ：混淆和加固应用**

实不相瞒，我之前从未对我的应用加固过，因为我感觉用户量不是太大，也不会有人有兴趣来反编译或者攻击。直到我看到这一句话：
> 当用户使用你的应用的那一刻起，作为开发者，你就有责任去保证应用使用整个过程中的用户设备和信息的安全。

回想之前，觉得自己真的好烂，从现在开始，我想成为一个负责任的开发者。


**第二步：打各种渠道包，看呐，这百花齐放的应用市场。**

因为伟大的墙，没有了 Google Play ,但是我们收获了一大片森林，多少个渠道包，取决于我们的运营是有多大的野心，一般情况下，几十个是少不了的。



# <font color="#008000">混淆和加固的区别</font>

首先要明确的，混淆和加固是两个非常不同的概念。（明确的原因，在于我之前就傻傻的以为混淆和加固是一回事，掩面羞愧逃~ ）

混淆，是一种类似障眼法的作用，让反编译后的代码阅读难度增加，本质上来说，并非是防止了反编译，而是增加了阅读难度。例如将要混淆类名和函数名，替换为无意义的短名称，（OrderUtils. createOrder() -> A.b() ）。

加固，可以理解为，将APK的外层加了一层壳，如果想反编译，必须突破这层壳的保护。加固后的APK，反编译出来，看到的只是外面那层壳的代码。加固涉及到的技术手段就很多了，同时也非常的深奥,例如dex 文件加密和字节码变形等。


# <font color="#008000">混淆</font>


### 开启混淆很简单

说到混淆吧，没用过混淆的人，看到混淆的那些关键字，估计都和我之前一样，简直一脸懵，但是混淆过的我，告诉你们，完全不要害怕，混淆的道理真的很简单。

混淆，主要包括三个部分，资源压缩，代码混淆和代码压缩。


 - 资源压缩
主要压缩的是对象是项目 res 和 asset 文件下未被引用的资源文件。这个工作是通过 Gradle 的 Android 插件去实现，默认资源压缩是关闭的，我们在 build.gradle 中可以通过 ` shrinkResources true` 来开启。

 - 代码压缩和代码混淆
上面针对的是资源，这个针对的就是代码文件了。这个方面，有一个在专门做 Java 字节码混淆的工具 ProGuard ，使用起来非常方便，是不是很开心？更开心的是 Android Studio  2.0 之后已经集成 ProGuard，我们再次可以通过在 build.gradle 中通过 ` minifyEnabled true ` 就可以开启混淆了。


所以 build.gradle 中添加短短的三行代码，项目打包 release 包就已经是混淆后的包了。

``` java
buildTypes {
        release {
            minifyEnabled true  //开启混淆和代码压缩
            shrinkResources true //开启资源压缩
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
```

这里注意 shrinkResources true 在 minifyEnabled true 的前提下，才会生效。因为需要代码压缩，释放掉无用资源的引用，资源压缩才能正常工作。


### 思考->为什么需要自定义规则，不能自动混淆好吗？

这是上面混淆三行代码的中最长的一句：

```java
//混淆规则的文件
 proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
```

上面代码中有涉及到两个文件,其中
 proguard-android.txt 是 Android SDK 提供的默认混淆文件,里面有 Android 应用通用的一些混淆规则，位置在 sdk/tools/proguard/ 中，这个文件不需要我们修改，默认就好。

 proguard-rules.pro 是项目的混淆文件，提供给开发者，用来编写自定义的混淆规则，这个文件，才是我们做混淆工作的主战场。

写之前，需要明确的是，我们为什么要在 proguard-rules.pro中自定义规则?

这个需要明白两个问题

**1.ProGuard 是怎么做到混淆的？**

压缩: 移除无效的类、属性、方法等
优化: 优化字节码，并删除未使用的结构
混淆: 将类名、属性名、方法名混淆为难以读懂的字母，比如a,b,c

**2.ProGuard 混淆的范围？是全部的类吗？**

ProGuard  是一个优化 Java 字节码的工具，也就是说，只要是Java文件都会被混淆。
但是这并不是我们想要的结果，因为有些类，如果被混淆就会出现问题，举个例子，布局中有一个 button 的 android: onClick 引用了 Activity MainActivity 中的方法 buttonClick()。混淆之后，布局文件是 .xml 文件不会被混淆，保持不变，Activity类是 .java 文件，会被混淆 ，MainActivity 被混淆成 A，buttonClick()被混淆成 b()。然后，让我们点击 button ，触发 onClick ，找不到 MainActivity.buttonClick(),是不是就会报错了，干脆利落地 Crash 了。

造成这个结果的原因是，ProGuard 并不是专门用来优化 Android 应用的一个工具，所以它并不知道什么该混淆，什么不该混淆。

混淆规则的制订，更多的是一种保护，告诉 ProGuard 某些类某些方法不用混淆。

 proguard-android.txt 中默认的一些规则，就是针对所有的 Android  项目都适用的混淆规则。

例如：
```java
#包名不混合大小写
-dontusemixedcaseclassnames

#不跳过非公共的库的类
-dontskipnonpubliclibraryclasses

#混淆时记录日志
-verbose

#关闭预校验
-dontpreverify

#不优化输入的类文件
-dontoptimize

#保护注解
-keepattributes *Annotation*

//等等，全部内容请自己去看sdk/tools/proguard/proguard-android.txt

```

由于每个项目的情况都不一样，所以 项目中 proguard-rules.pro 的文件，就是留给开发者根据自身项目情况，去设置混淆规则的。可谓是非常人性化。


### 混淆的99%工作->编写自定义混淆规则

自定义混淆规则文件 proguard-rules.pro 的内容可以分为三个部分：

 - 一些前辈们总结的，一般项目都会用到的混淆规则
 - 第三方库的混淆规则 ，这个第三方文档上都有。
 - 如果有使用ORM类型的数据库，例如greenDao,需要保护映射数据表的实体类不被混淆
 - 保护 JNI 中调用的类不被混淆。
 - 保护 WebView 中 JavaScript 调用的方法不被混淆
 - 保护 Layout 布局使用的View构造函数、android:onClick等不被混淆。

关于怎么保护，这个按照 ProGuard 制订规则去保护了，关键词很多，而且视项目不同，混淆文件一般也不一样，我也没法给你提供一个模板什么。

你可以看这篇文章  [写给 Android 开发者的混淆使用手册](https://www.diycode.cc/topics/380)，写的超级详细超级棒，我就是看这个学会的混淆。

# <font color="#008000">加固</font>

加固这方面的技术要求太高了，没有特别的安全需求的话，都会选择第三方加固平台，公司项目采用的是[梆梆加固](https://www.bangcle.com/)。

梆梆加固的使用方式很简单，上传 apk 包，点击加固，等待加固完毕，当加固成功后，会得到一个评估报告，要仔细阅读一下，查看是否由代码上可修改的漏洞等，如果有，修改后再加固，然后，下载加固后的加固包即可。


# <font color="#008000">多渠道打包</font>

只要是需要分发到各大市场的应用，多渠道的统计必不可少，因此多渠道打包必不可少，公司项目采用多渠道打包工具是 PackerNg。

PackerNgV2 提供 gradle 集成打包和脚本打包，因为我们需要加固，所以选择脚本打包方式。具体步骤可以看 [PackerNg的文档](https://github.com/mcxiaoke/packer-ng-plugin)


# <font color="#008000">完整打包流程</font>

基本的打包流程如下：

![这里写图片描述](http://img.blog.csdn.net/20170821154914784?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzI0NTI2MjM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

<font color="#008000">注意看，除了上面介绍的部分，多了一个重新签名的步骤。</font>

因为当加固之后的 Apk 没有签名，需要我们重新签名。自动签名的工具有很多，包括梆梆加固都有提供再签名的工具，使用这些签名之后PackerNg 脚本打包后会出现 `Error: Invalid Signature` 错误。原因是现在提供的工具大多只支持了 V1 签名，而项目集成的 PackerNg 最新版本需要 V2 签名，所以我们要给 Apk 重新签 V2 签名。


命令如下：
```
zipalign -v 4 <apk_path> <after_apk_path>//对齐
apksigner sign --ks <keystore_path> <after_apk_path> //重新签名

//以下命令可用来验证对齐和再签名的结果
zipalign -c -v 4 <apk_path>//验证是否对齐
apksigner verify --verbose  <after_apk_path>  //查看签名信息，用来验证是否是 V2 签名
```
其中
apksigner 是Android SDK 自24.0.3开始提供的官方签名工具，位于：Android SDK/build-tools/对应版本/apksigner。

zipalign 是 Android SDK 提供代码对齐工具, 位于 Android SDK/build-tools/对应版本/zipalign。

如果已经配置好 sdk/build-tools/的 Path，直接在 Terminal 中，任何路径下就能使用，如果没有可以选择是配置一下路径或者切换到 sdk/build-tools/ 对应版本／ 中操作。



# <font color="#008000"> 最后</font>

真的很感叹，我也终于自己完整的走通了一个项目到发布市场的一整套流程，现在做完之后，看起来一切都很简单，步骤也不是很多，但是对于上周的我来说，因为对混淆和加固的知之甚少，真的胆战心惊的去做这件事情，混淆之后安装测试，签名之后安装测试等等，因为约了百度的首发，上线时间有限制，真的害怕搞出来自己短时间内无法解决的问题。

我仔细想想，当时的紧张，都是对未知的恐惧。例如，签名遇到问题了，才知道 Android 7.0 新出了 V2 签名机制等，因为没用过，也测了又测，直到放心。

在这个知识的海洋里，我知道的真是太少了，努力加油吧，为了以能够自信多一点点。

---

刚刚开通了个人微信公众号，最新的博客，好玩的事情，都会在上面分享，欢迎关注 (*^o^*)。

<div  align="center">    

![微信公众号](http://oriwplcze.bkt.clouddn.com/836a36d6a91d859428783f8ea2ce85d7.png)

</div>
