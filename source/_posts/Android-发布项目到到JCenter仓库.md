---
title: Android-发布项目到到 JCenter 仓库
date: 2018-02-01 17:03:01
tags:
---

Maven 是什么？JCenter 是什么？为何上传？如何上传？
<!--more-->


# 写在前面

阅读这个博客，你会知道

 - Maven 的概念是什么？
 -  为什么要将代码上传到 Maven 仓库 ？
 -  Maven 仓库的地址是从哪里来 ？
 -  JCenter 和 Maven Central 是什么？
 -  如何将项目发布到 JCenter 仓库？

# Maven 是什么？

[Maven 官网](https://maven.apache.org/index.html) 上的介绍：

> Apache Maven is a software project management and comprehension tool. Based on the concept of a project object model (POM), Maven can manage a project's build, reporting and documentation from a central piece of information.

Maven 是一个软件项目管理工具和构建工具，基于 POM 的概念，Maven 可以参与项目的构建过程，还可以通过一些信息生成项目报告和文档。

总结来说，Maven 的功能可以分成三个方面：  
- 优秀的构建工具

  通过简单的命令，能够完成清理、编译、测试、打包、部署等一系列过程。同时，不得不提的是，Maven 是跨平台的，无论是在 Windows、还是在 Linux 或 Mac 上，都可以使用同样的命令。

- 依赖管理工具

  项目依赖的第三方的开源类库，都可以通过依赖的方式引入到项目中来。代替了原来需要首先下载第三方 jar，再加入到项目中的方式。从而更好的解决了合作开发中依赖增多、版本不一致、版本冲突、依赖臃肿等问题。

  具体是怎么实现的呢？Maven 通过坐标系统准确的定位每一个构件，即通过坐标找到对应的 java 类库。

- 项目信息管理工具

  能够管理项目描述、开发者列表、版本控制系统地址、许可证等一些比较零散的项目信息。除了直接的项目信息，通过 Maven 自动生成的站点，以及一些已有的插件，还能够轻松获得项目文档、测试报告、静态分析报告、源码版本、日志报告等非常具有价值的项目信息。


我们的开发过程中， Maven 是作为一个依赖管理工具的角色，我们把三方库上传到 Maven 的远程仓库中，项目构建的时候，通过 build.gradle 中的配置，去 Maven 仓库中找到对应的库，就开始下载对应的 aar 或 jar，并依赖到项目中。


# Maven 仓库是什么？有哪几种？

当搜索`上传 aar 到 Maven 仓库`，会看到很多相关博客，其中上传的代码都大同小异，但是目标仓库却有很多种，大概可以分为以下几类：

- JCenter  
  标准的Maven仓库，由 Bintray 公司维护，上传这个仓库，需要注册 Bintray 账号，上传 library 到自己账号下的库地址下，然后同步 JCenter 中央仓库。  
  JCenter 现在是 Google 官方默认的中央仓库。新项目创建时，RootProject 的 build.gradle 的 `repositories` 默认已经添加 `jcenter()` 仓库地址。

- Maven Central   
  同 Jcenter 一样是标准的 Maven 仓库，由 Sonatype 维护。上传这个仓库，需要注册 Sonatype 账号，上传 library 到自己账号下的库地址下，然后同步 Maven Central 中央仓库。

- Nexus  
  Nexus是一个 Maven 仓库管理器，用来搭建私有仓库服务器。公司几乎都会创建一个 Nexus 私有 Maven 仓库，用来存放公司内部的三方库，实现各个模块间的共享。另外好处就是便于管理，节省公网带宽，利用内网下载依赖项速度快。


因为有的童鞋没有私有 Nexus 仓库管理地址，所以我们这里用 jCenter 仓库做演示。


# 发布开源项目到到 JCenter 仓库

JCenter 的用户资源很多并且十分活跃，而且是 Google 官方默认的中央仓库，如果想开源一个你的项目， 上传到 JCenter 不失为是一个好想法。

上传步骤也很简单，总结来说只有四步：

1. 注册一个 Bintray 账号，创建一个远程的库。
2. Android Studio 本地准备好开源的项目，请注意是 Library Module。
3. 将本地 Library Module 和第一步创建的远程的库连接起来，将 Library Module 打包成 的aar 和 pom 文件等上传到远程的库。
4. 将远程库同步到 JCenter 中央仓库。


# Step 1 : JCenter 远程库准备

1.去 [Bintray 网站](https://bintray.com/) 注册一个账号。Bintray 账号有个人账号和企业账号之分，这里切记，要注册个人号。看下图箭头所指：

![注册](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/986b3b4ca8a1e4bc24522d6baa95d237.png)


2.登录账号 ，点击 `Add New Repository` ,创建一个存储库，用来托管我们的代码。Name 可以任意，但是请填写 `maven`，后面会解释原因，Type 选择 Maven,剩下的是可选项，可以不填。

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/02cc40cbea454246e0d177a85c14a038.png)

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/6ed762c5f37863a69112c35c82146567.png)


3.进入刚才创建的 Repository，点击 `Add New Package`  ,创建 Package。Name 任意,Licences 选择 Apache-2.0,Version control 填入版本管理的地址，我填的是测试项目的 GitHub 仓库地址，其他项是选填，可以不填。


![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/ac265e711423bb4ba52d766666219d00.png)

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/1bbe57c5a28be401b5ebbb025f4202ef.png)

# Step 2 : Android Studio 本地项目准备

此步骤，就是准备好一个要上传的 Android Library Module 就好啦！

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/98723597c6ff020bec3d53fe27bc2cc7.png)

这里我新建了一个空的库项目，在其中新建了一个 Java 类和 Activity,用来后面测试这个库好不好使。

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/e19bd3a1e573ff06f64ff7b1a86585df.png)

测试无所谓，实际开发的话，还请知悉 [Android 库项目开发官方文档](https://developer.android.com/studio/projects/android-library.html?hl=zh-cn),里面有给出开发注意事项，要好好听谷爹的话。


# Step 3 : 本地和远程对接，开始上传

Bintray 公司提供了一个 Gradle 插件 [gradle-bintray-plugin](https://github.com/bintray/gradle-bintray-plugin) ,用来将项目上传到 Bintray 和 jCenter。

配置的步骤有很多，对于不太懂 Gradle 的童鞋，例如我，就算是 Copy & Paste 都很心累，而且粘贴复制后代码也不是很美观。

[bintray-release](https://github.com/novoda/bintray-release) 的出现解决了以上问题，我们直接使用 bintray-release 快速配置上传。

##  rootProject 的 build.gradle 添加 bintray-release 依赖

我使用的时候，bintray-release 的最新版本是 0.8.0.

```
buildscript {
    repositories {
        jcenter()M
    }
    dependencies {
        <!-- classpath 'com.novoda:bintray-release:<latest-version>' -->
        classpath 'com.novoda:bintray-release:0.8.0'
    }
}

```

## moudle 的 build.gradle 配置上传参数

```

apply plugin: 'com.android.library'
apply plugin: 'com.novoda.bintray-release'//添加


//生成源文件
task sourcesJar(type: Jar) {
    from android.sourceSets.main.java.srcDirs
    classifier = 'sources'
}

//生成Javadoc文档
task javadoc(type: Javadoc) {
    source = android.sourceSets.main.java.srcDirs
    classpath += project.files(android.getBootClasspath().join(File.pathSeparator))
}

//文档打包成jar
task javadocJar(type: Jar, dependsOn: javadoc) {
    classifier = 'javadoc'
    from javadoc.destinationDir
}

//拷贝javadoc文件
task copyDoc(type: Copy) {
    from "${buildDir}/docs/"
    into "docs"
}

//上传到JCenter所需要的源码文件
artifacts {
    archives javadocJar
    archives sourcesJar
}

//解决 JavaDoc 中文注释生成失败的问题
tasks.withType(Javadoc) {
    options.addStringOption('Xdoclint:none', '-quiet')
    options.addStringOption('encoding', 'UTF-8')
    options.addStringOption('charSet', 'UTF-8')
}


//发布到 Bintray
publish {

    userOrg = 'duanruirui'           //bintray.com 注册的用户名
    groupId = 'com.jcenterlibrary'   //以后访问 jcenter上此项目的路径，一般和库项目的包名一致
    artifactId = 'JCenterDemo'       //bintray.com 创建的 Package 名
    publishVersion = '1.0.13'         //版本号
    desc = '这是一个不认真的版本说明' //版本说明，随意
    website = 'https://github.com/DRPrincess/DR_MavenDemo'    //关于这个开源项目的网站，随意
}


```

关于 publish 的参数，以上只是一部分，其实还有很多，具体看 [配置publish闭包说明 ](https://github.com/HailouWang/bintray-release/wiki/%E9%85%8D%E7%BD%AEpublish%E9%97%AD%E5%8C%85)，需要解释一下，bintray-release 默认发布到  maven 仓库，这也是上一步 ` Add New Repository `时，Name 建议填写 `maven` 的原因。如果有需要，需要自定义 Publication，详看 [自定义 Publication](https://github.com/HailouWang/bintray-release/wiki/%E8%87%AA%E5%AE%9A%E4%B9%89-Publication)。


上一步，我们 ` Add New Package ` 创建的 Package 的名字这里也用到了，原因在于，当三方库上传到 Maven 仓库后，Gradle 的引用路径是：

```
compile groupId:artifactId:version
```
groupId    ： 以后访问 jcenter上此项目的路径，一般和库项目的包名一致。
artifactId ： 就是 ` Add New Package `创建的 Package 的名字 。   
version    ： 三方库的版本号。




## 一句 Gradle 命令完成上传

以上两部配置准备完毕，在 rootProject 的路径下执行这句命令即可完成上传。
```
./gradlew clean build generatePomFileForReleasePublication bintrayUpload -PbintrayUser=BINTRAY_USERNAME -PbintrayKey=BINTRAY_KEY -PdryRun=false
```

这行命令的中两个参数 `BINTRAY_USERNAME`和`BINTRAY_KEY`，需要换上你自己的bintray.com 账号信息，分别是用户名和 API Key。用户名很好得到，API Key 通过下图方式，可以拿到：

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/4a6010dbd5af15e6d3290a3091ad9f83.png)


![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/522c5e2603c7e795538c3d0b0f0f50fe.png)


如果命令执行成功，会看到 `BUILD SUCCESSFUL ` 字样。

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/6642c2472d06bc7dafe4755cf79ee6cb.png)

然后去 [Bintray 网站](https://bintray.com/) 登录后，就可以看到上传的的项目了，右侧可以看到上传的版本，我测试过很多回，截止到我截图的时间已经到 1.0.6了。

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/cebfe19247a8c10949fc53d55f552510.png)

点击 Files,可查看上传的文件，如下是 1.0.6 版本上传的文件：

|文件|意义|
|----|----|
|JCenterDemo-1.0.6-javadoc.jar|生成的 java 注释说明文档|
|JCenterDemo-1.0.6-sources.jar|项目源码，有这个之后，引用该库的时候，可以点进入看到源码|
| JCenterDemo-1.0.6.aar|Android 库项目的归档文件，这是必不可少的文件
|JCenterDemo-1.0.6.pom |Maven的基础。它是一个XML文件，包含了 Maven 用来构建项目所需要的项目配置的信息,aar 是不能将库的依赖打进去的，那些依赖会记在 pom 文件中。|

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/7a516c58657112fc7395153047111d9f.png)


### 上传过程中遇到的问题

上传过程中，我祝福你不会遇到错误的情况，但是出现了，也不要烦躁，坑这个东西踩着踩着就平了，遇到错误去 [bintray-release](https://github.com/novoda/bintray-release) 的 issues 中查一下，或者问问谷爹，基本都能找到解决方法。

以下我遇到的问题：

- 01:只上传了`aar` 文件，没有生成 pom 文件

  [bintray-release](https://github.com/novoda/bintray-release) 的文档上给出的命令 `./gradlew clean build  bintrayUpload` 只上传了 `aar` 文件,于是添加了一个 `generatePomFileForReleasePublication`,解决了这个问题，最终版就是上面示例的命令。

- 02:其他三个文件都上传了，但是没有上传`aar` 文件

  这个问题，一般是项目构建失败导致的，我是因为资源文件名字重复，很奇怪，构建失败，但是其他文件上传上去了。遇到这个文件，仔细看一下构建的输出原因，总能解决的。

- 03:Could not upload to 'xxx': HTTP/1.1 409 Conflict [message:Unable to upload files: An artifact with the path 'xxx' already exists]

    Maven 不能重复同一个版本，如果上传新版本，版本号要修改以下，一般是 +1 递增。

- 04:Javadoc 文档生成失败，导致 build Failed

   错误信息：
   ```
   FAILURE: Build failed with an exception.

   * What went wrong:
   Execution failed for task ':jcenterlibrary:javadoc'.
   > Javadoc generation failed. Generated Javadoc options file (useful for troubleshooting): '/Users/admin/AndroidStudioProjects/DR_MavenDemo/jcenterlibrary/build/tmp/javadoc/javadoc.options'

   ```

   是因为中文注释的原因导致，修改 JavaDoc 的编码，就可以正常生成和显示了。解决方法是 Library Module 的 build.gradle 中添加下列代码:
   ```
   //解决 JavaDoc 中文注释生成失败的问题
  tasks.withType(Javadoc) {
      options.addStringOption('Xdoclint:none', '-quiet')
      options.addStringOption('encoding', 'UTF-8')
      options.addStringOption('charSet', 'UTF-8')
  }

   ```


# Step 4 : Add to JCenter 中央仓库

最后一步，上传到 JCenter 中央仓库

未曾接触过这个的我，对这最后一步表示有很大的疑惑：  

因为在上一步，我们已经将库项目已经推送到远程了，那为什么要有最后这一步呢？

在第三步项目成功上传后，已经开始使用 Gradle 依赖该开源库，在 Package 页面点击 Gradle 可以看到对应的依赖命令。

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/f980db82916a62d7aa9b83cfcead6b18.png)

然后我试了下，发现要使用的话，会报找不到库的错误(1.0.8 的时候忘记截图了，用 1.0.14 测试的，道理是一样的，报错原因是因为找不到这个三方库)，如下图：

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/fb41c7bfdd46c91aebf37f628455737f.png)


如果想正确引用，需要将远程库所在 Repository 地址告诉 Gradle,这个地址从 Bintray 网站可以拿到，如下图：

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/c1d708664f98e3c4cdc02317d2d259b4.png)


然后 rootProject 的 build.gradle 中添加如下内容，即可正确引用：

```
allprojects {
    repositories {
        google()
        jcenter()
        maven { url 'https://dl.bintray.com/duanruirui/maven' } //添加

    }
}

```

那为什么使用其他第三方库，不需要加上三方库作者的 maven 地址呢？  

就是因为作者将三方库同步到 JCenter 的中央仓库了。` jcenter()` 就是中央仓库的地址。所以为了方便使用，去掉这个三方库所在 maven 仓库地址的额外引用，我们最好将库同步到 JCenter 中央仓库。


同步的步骤非常简单，Package 详情页面有一个 ` Add to JCenter ` 按钮，点击进入，填写同步的理由，提交申请即可。通过之后会给发消息通知的。


![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/92bc14fe53ecc6a2e423e670cfe8bb7a.png)

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/df37ca78703a1ed8a51debf983963516.png)

审核的速度还是非常快的，上午提交的，下午就收到了通过的站内信，如下图所示：

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/84135d033403bbbff1970b09c1ed3906.png)


现在已经同步到 JCenter 中央仓库，只通过一句代码引用你的第三方库啦～

# 写在最后

以上是本博客的全部内容，博客测试用的 Demo 我放在了 GitHub 上，地址在此：[DR_MavenDemo](https://github.com/DRPrincess/DR_MavenDemo),有问题欢迎提 issue。

之前没接触过这方面的知识，尝试使用的时候，再次感觉到自己的学识浅薄，实现过程中出现一些问题，大多一知半解，不知道问题的本质，希望以后的学习中，能够深入些，了解更多一点。

于是，下篇博客[《Android-Nexus 搭建自己的 Maven 仓库 & Gradle 上传依赖包》](http://blog.csdn.net/qq_32452623/article/details/79385595)，继续出发！


# 参考博客
[《Maven与nexus》](http://blog.csdn.net/liusong0605/article/details/25654811)   
[《How to publish Android Library on Bintray/JCenter》](http://blogs.quovantis.com/how-to-publish-android-library-on-bintrayjcenter/)  
[《放弃JitPack，发布Android Library到Bintray、JCenter》](https://www.jianshu.com/p/9f81d5b5a451)  
[《 Android 快速发布开源项目到jcenter》](http://blog.csdn.net/lmj623565791/article/details/51148825)




---

刚刚开通了个人微信公众号，最新的博客，好玩的事情，都会在上面分享，欢迎关注，让我们一起学习和成长。

<div  align="center">    

![微信公众号](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/qrcode_for_gh_e8f891ce77fb_258.jpg)

</div>
