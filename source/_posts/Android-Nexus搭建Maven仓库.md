---
title: Android-Nexus 搭建自己的 Maven 仓库 & Gradle 上传依赖包
date: 2018-02-27 10:38:00
tags:
---


搭建 Nexus 私服，并从一个 Android 开发者的角度去上传依赖到 Nexus 私服。

<!-- more -->

# 前言

这是一篇旨到弄清以下问题的博客：

- Maven 仓库分类？
- Nexus 仓库是什么？
- Nexus 和 Maven 的关系？
- 为什么要创建 Nexus 仓库？
- 如何搭建 Nexus 仓库？
- 为什么仓库 release 和 snapshot 之分？
- snapshot 仓库要怎么使用？

我不知道也未曾料想过，我会从一个 gradle 依赖引用的 `@` 符号开始，思路分散的如此的地步，我的脑子是个神奇的东西。


# Maven 的仓库分类

从 Maven 的依赖下载管理角度来看，Maven 仓库的可分为两类，远程仓库和本地仓库。

本地仓库是电脑硬盘上的一个目录，远程仓库是在指放在远程服务器上电脑硬盘上的 Maven 仓库目录，使用需添加远程仓库的地址，才能正常连接下载依赖。

Maven 的远程仓库分为中央仓库和私服仓库。中央仓库存放了世界各地用户上传的依赖包，比较出名的是 JCenter 和 Maven Central，开源的第三方依赖一般都会上传到这两个中央仓库，这样我们只用添加这两个中央仓库的链接地址，就可以下载各种我们需要的依赖了。

由于每次构建都需要从中央仓库下载依赖，对于中央仓库的服务器来说，压力很大，间接导致下载速度很慢。并且，上传到中央仓库的依赖都是开源的，只要知道名字，用户就都能下载，虽然开源精神是值得倡导的，但是一些公司内部的核心依赖包，出于一些考虑，无法直接开源。于是，私服仓库就诞生了。在公司的局域网，搭建一个 Nexus 仓库，把公司内部不想开源的依赖包上传到私服仓库中，这样下载依赖的需满足两个条件，私服仓库的地址和在公司局域网内，一定程度上有个保密和安全性。


# 私服是一种特殊的远程仓库

私服是一种特殊的远程仓库, 它设在局域网内, 通过代理广域网上的远程仓库, 供局域网内的 Maven 用户使用。私服的存在，特殊于，它改变了 Maven 下载依赖的机制。

只有本地仓库和远程中央仓库时，当 Maven 根据坐标寻找依赖包时, 首先会检索本地仓库, 如果本地存在则直接使用, 否则去远程仓库下载.   

但是，当有了一个私服后：   

当检索本地仓库发现不存在的时候， Maven 客户端先向私服请求, 如果私服不存在该依赖包, 则从外部的远程仓库下载, 并缓存在私服上, 再为客户提供下载服务。简单的理解是私服相当于本地仓库和远程中央仓库的中间缓存，因此，私服的存在，可以节省公网带宽，利用内网下载依赖项速度快。

下面是一张本地仓库，私服仓库和远程中央仓库的依赖下载示意图（构件可理解为三方的依赖包，是比较专业的说法）：

![](http://oriwplcze.bkt.clouddn.com/02e031b659e40c3fa8876f5b89b0d180.png)



# Nexus 搭建私服

有三种比较流行的 Maven 仓库管理软件可以创建私服，Apache基金会的 Archiva，JFrog 的 Artifactory ，Sonatypec的 Nexus,本博客的内容是使用 Nexus 搭建私服。

Nexus 的话说的很霸气,自称为 **“世界上第一个也是唯一可以免费使用的通用储存库解决方案”**。


![](http://oriwplcze.bkt.clouddn.com/11d41dde1d7948fa8a2ff8f35b38bec0.png)


Nexus 的 2.x 和 3.x 版本改动很多，我写下这篇博客的时候，Nexus 最新版本是 3.8.0，因此按照 3.8.0 记录。


## Java 环境准备

当前 Sonatype Nexus 基于 Java,要求 Java 8 Runtime Environment（JRE），java 版本不能低于 1.8.0，所以请先自行下载 JDK 最新版本，并配好 Java 环境。

想确认已安装的 Java 版本，可以运行 `java -version` 命令查看。

![](http://oriwplcze.bkt.clouddn.com/8d21c50aa3dedf4ef4c5352cf76b2461.png)


## 下载 Nexus

去[ 官方网站](https://www.sonatype.com/download-oss-sonatype) 下载自己对应平台的 Nexus 安装包。

![](http://oriwplcze.bkt.clouddn.com/09685a7edb962afb1921cb9b45e86079.png)

## 运行 Nexus

将下载的压缩包，解压，会看到如下目录：

![](http://oriwplcze.bkt.clouddn.com/140fe04a1972c2825c6b4a723357fd96.png)

进入 bin 目录，启动 Nexus 。

```
// Windows小伙伴们用这个  
nexus.exe /run

// Unix 和 OS X 小伙伴们用这个
./nexus run

```  


我使用的是 Mac，执行 `./nexus run`，运行结果如下：

![](http://oriwplcze.bkt.clouddn.com/8d69e9e92fb4cc9484c6fc2187cec0d2.png)

看到如上信息，Nexus 已经启动，


## 访问 Nexus 用户页面

Nexus 启动成功之后，就会监听配置的IP地址和接口范围，默认的接口是 8081,在浏览器访问 [http://localhost:8081/](http://localhost:8081/),可以看到启动的本地 Nexus 仓库页面。如果别人想访问这个仓库的话，需要额外配置。

![](http://oriwplcze.bkt.clouddn.com/bdd53e2b682d94314dc759737adc6282.png)


界面上展示了未登录用户可以使用的功能。Nexus 提供了一个完全访问权限的管理用户。 用户名是 `admin`，密码是 `admin123`。 点击界面右上角的按钮登录。



以上，一个本地的 Nexus 服务器就搭建好了。

## 访问 Nexus 仓库分类

打开 Nexus 的 Repository 页面，可以看到 Nexus 默认为我们创建的仓库。

![](http://oriwplcze.bkt.clouddn.com/10de81c706f82e13671a61af59b99773.png)

注意 Type 列，展示了 Nexus 的仓库分类：  

- proxy（远程代理仓库）  
这种类型的仓库，可以设置一个远程仓库的链接。当用户向 proxy 类型仓库请求下载一个依赖构件时，就会先在自己的库里查找，如果找不到的话，就会从设置的远程仓库下载到自己的库里，然后返回给用户，相当于起到一个中转的作用。   

  例如 maven-central 用来存储从 Maven 中央仓库下载过的构件。
- group （聚合仓库）  
在 Maven 里没有这个概念，是 Nexus 特有的。目的是将多个仓库聚合，对用户暴露统一的地址，这样用户就不需要配置多个地址，只要统一配置 group 的地址就可以了。group 仓库的聚合成员可以在仓库设置中添加和移除。

  例如 maven-public 是一个 group 类型的仓库，通过引用这个地址，可以访问组内成员仓库的所有构件。

  ![](http://oriwplcze.bkt.clouddn.com/0b6374cee58f2a3d7896d90c3a3b1bb0.png)



- hosted（宿主仓库）   
我们自己的构件，上传的就是这样的仓库。目前 maven-releases 和 maven-snapshots 是 hosted 类型的仓库。我们可以上传到这两个仓库，也可以自己创建 hosted 仓库。


# 上传依赖包到 Nexus 私库

一个本地的 Nexus 私库搭建完毕，接下来的问题是，如何将构件(依赖包)上传上去。

上传上去的方式有两种：

- 使用 Gradle 的集成的 Maven 插件上传
- 命令行上传([有兴趣点这里了解](https://www.xncoding.com/2017/09/02/tool/nexus.html))



## 使用 Gradle 部署依赖包到 Maven 服务器

Gradle 集成了 Maven plugin,通过简单的配置，就能将构件上传到 Maven 服务器上。


1. 第一步，创建一个 Android Library 项目
2. 第二步，Library Moudle 的 build.gradle 配置上传参数
3. 第三步，上传到 nexus 私服。

步骤和上一篇部署到 JCenter 仓库一样，区别在于上传的服务器地址不同，使用的上传插件也不同。

### 创建一个 Android Library 项目

我创建了一个 Library Module, 命名为 nexuslibrary, 在其中创建几个类，暴露公共方法，方便测试最后的依赖的下载和应用。为了省事，我从从上一篇文章创建过的 jcenterlibrary 中拷贝过来的，然后改改的。

### Library Moudle 的 build.gradle 配置上传参数

在 Library Moudle 中创建一个 `gradle.properties` 文件,记录 Nexus 的私服的用户名称和密码，要上传的目标仓库地址，以及上传构件的信息。

```
//上传构件的信息
GROUP=com.dr
VERSION_NAME=1.0.3
POM_ARTIFACT_ID=nexuslibrary

//上传的目标仓库地址
SNAPSHOT_REPOSITORY_URL=http://localhost:8081/repository/maven-snapshots/
RELEASE_REPOSITORY_URL=http://localhost:8081/repository/maven-releases/

//Nexus 的私服的用户名称和密码
NEXUS_USERNAME=admin
NEXUS_PASSWORD=admin123

```


在 Library Moudle de  `build.gradle` 中添加以下配置，用来生成 `sources.jar`源码包和 `javadoc.jar`方法文档和创建上传的 Gradle Task。

```
apply plugin: 'maven'

def isReleaseBuild() {
    return VERSION_NAME.toUpperCase().contains("SNAPSHOT") == false
}
def getRepositoryUsername() {
    return hasProperty('NEXUS_USERNAME') ? NEXUS_USERNAME : ""
}
def getRepositoryPassword() {
    return hasProperty('NEXUS_PASSWORD') ? NEXUS_PASSWORD : ""
}
def getRepositoryUrl() {
    return isReleaseBuild() ? RELEASE_REPOSITORY_URL : SNAPSHOT_REPOSITORY_URL
}
afterEvaluate { project ->
    uploadArchives {
        repositories {
            mavenDeployer {
                pom.groupId = GROUP
                pom.artifactId = POM_ARTIFACT_ID
                pom.version = VERSION_NAME
                repository(url: getRepositoryUrl()) {
                    authentication(userName: getRepositoryUsername(), password: getRepositoryPassword())
                }
            }
        }
    }
    task androidJavadocs(type: Javadoc) {
        source = android.sourceSets.main.java.srcDirs
        classpath += project.files(android.getBootClasspath().join(File.pathSeparator))
    }
    task androidJavadocsJar(type: Jar, dependsOn: androidJavadocs) {
        classifier = 'javadoc'
        from androidJavadocs.destinationDir
    }
    task androidSourcesJar(type: Jar) {
        classifier = 'sources'
        from android.sourceSets.main.java.sourceFiles
    }

   //解决 JavaDoc 中文注释生成失败的问题
    tasks.withType(Javadoc) {
        options.addStringOption('Xdoclint:none', '-quiet')
        options.addStringOption('encoding', 'UTF-8')
        options.addStringOption('charSet', 'UTF-8')
    }
    artifacts {
        archives androidSourcesJar
        archives androidJavadocsJar
    }
}

```



以上就完毕了。

其实，可以把以上 `build.gradle `中的配置，专门创建一个 gradle 文件，例如 `upload_nexus.gradle`中，然后在 `build.gradle `中引用`upload_nexus.gradle`，和上面的效果是一样的，区别在于 `build.gradle ` 会更加的简洁。

```
apply from: 'upload_nexus.gradle'

```





### 上传到 nexus 私服

Android Studio 中打开右侧的 Gradle 侧边栏，打开 nexuslibrary,可以看到 `uploadArchives`,这就是刚才创建的上传 Task,点击即可完成上传。

![](http://oriwplcze.bkt.clouddn.com/aee1bf2799be2a5aa58fdb7d5513968a.png)

如果 uploadArchives Task 执行成功，在 Nexus 仓库中可以看到上传的构件包们。

![](http://oriwplcze.bkt.clouddn.com/1276aff67738bd0563e27e9ca6ce5cdf.png)

![](http://oriwplcze.bkt.clouddn.com/ece0a6437d223a4d1ba01d5c347d7504.png)



# 使用 Nexus 私服中三方包

刚才已经上传成功，现在可以开始尝试下载了，so excited !

首先在 rootProject 的 build.gradle 中引用私库地址，我引用的是 maven-public 聚合仓库的地址：

```
allprojects {
    repositories {
        //...
        //添加本地仓库地址
        maven { url 'http://localhost:8081/repository/maven-public/' }

    }
}

```

引用该三方库的目标 Module 的 build.gradle 中添加此库的依赖：

```
dependencies {
  //...
compile 'com.dr:nexuslibrary:1.0.0'
}
```

执行 sync gradle 之后，已经成功下载该三方库，可以正常应用该库中的公共类和方法。

![](http://oriwplcze.bkt.clouddn.com/469f1320c5277a19b12af9bec96fbeac.png)



# release 和 snapshot 仓库的区别与正确使用

上个步骤，我们已经将将依赖构件上传到 release 仓库中，如果没有修改版本号，再次上传，就会上传失败，并提示如下错误：

> Error:Execution failed for task ':nexuslibrary:uploadArchives'.
> Could not publish configuration 'archives'
   > Failed to deploy artifacts: Could not transfer artifact com.dr:nexuslibrary:aar:1.0.0 from/to remote (http://localhost:8081/repository/maven-releases/): Failed to transfer file: http://localhost:8081/repository/maven-releases/com/dr/nexuslibrary/1.0.0/nexuslibrary-1.0.0.aar. Return code is: 400, ReasonPhrase: Repository does not allow updating assets: maven-releases.

原因在于，release 仓库不能重复上传同一版本号，版本不能覆盖，只能迭代。

snapshot 仓库允许版本覆盖。当我上传多次上传同一个版本到 snapshot 仓库，会自动在版本号上添加时间戳来区分。

![](http://oriwplcze.bkt.clouddn.com/e9fb5ab00bdeb12b75dc4eedc6f2dc07.png)


那么问题来了，为什么存在这两个仓库？这个区别的存在意义是什么呢？

试想你是一个三方库的开发者，三方库是对外暴露的，1.0.0 已经在为其他程序服务，同时它本身存在问题也在继续开发迭代。此刻正在开发新版本 1.0.1，公司的小伙伴在同步从 Nexus 私库下载 1.0.1 最新改动版本测试。这种情况需要保证：
 1. 对外版本 1.0.0 不受影响
 2. 内测小伙伴始终能拉取到 1.0.1 的最新版本。
 3. 从版本号上，可以区分测试版和对外开放的稳定版

使用 release 和 snapshot 仓库可以很容易达到以上要求。

稳定对外开放的版本，放到 release 仓库。版本不能覆盖，保证外部使用不受版本更新的影响。  

迭代开发的不稳定版本，放到 snapshot 仓库。版本可覆盖，小伙伴测试的时候，不需要修改依赖的版本号，最多需要清除下 Maven 的依赖缓存，就可以下载到最新的版本。


上面提到的 目前 maven-releases 和 maven-snapshots 是 hosted 类型的仓库是 Nexus 默认创建的两个仓库，实际工作中，我们可以自定义创建仓库，自己规定为 release 库和 snapshot 库。下面给出两者的正确使用姿势，方便以后使用：

## release库（发布库）使用规则及场景

场景：

release库是存放稳定版本包的仓库，线上发布的程序都应从release库中引用正确版本进行使用。

使用规则：

- release库仓库名中带有“releases”标识，

- release 库不允许删除版本；

- release 库不允许同版本覆盖；

- release库上传的jar包版本号（version）不能以“-SNAPSHOT”结束（版本号中的SNAPSHOT是release版和snapshot版区别的唯一标识）；

- 第三方包（非公司内部开发）仅可引用 release 版

- 最好提供源码包 sources.jar 和方法文档 javadoc.jar，方便引用方使用。



## snapshot库（快照库）使用规则及场景

场景：

snapshot库是存放中间版本包的仓库，代表该库中依赖包的程序处于不稳定状态。当代码在开发过程中有其他程序需要引用时，可以提供snapshot版用于调试和测试。由于snapshot库的包依然处于测试状态，所以随时可以上传同版本最新包来替换旧包，基于这种不稳定状态，maven允许snapshot库中的包被编译时随时更新最新版，这就可能会导致每次打包编译时同一个版本jar会包含不同的内容，所以snapshot库中的包是不能用来发布的；


使用规则：

- snapshot 库仓库名中带有“snapshots”标识，

- snapshot 库可以删除版本；

- snapshot 库可以实现版本覆盖；

- 第三方包（非公司内部开发）不允许引用 snapshot 版

-  快照库上传的版本号（version）必须以“-SNAPSHOT”结束，并上传至私服后系统将自动将“-SNAPSHOT”替换为时间戳串（本地代码引用时依然用“-SNAPSHOT”结束的版本号，无需替换时间戳），一个快照包线上将存在至少两个版本。


## PS：

### 是否允许版本覆盖

其中是否允许版本覆盖，是否可以删除版本，可以通过 Nexus Repository 设置：

![](http://oriwplcze.bkt.clouddn.com/cfec316f6cf3a46c3974f08079c58869.png)

### 拉取不到最新版本呢？

在 Gradle 引用 snapshot 库版本时,若遇到已经上传最新版本，但是好像没有作用时，不要惊慌，不要失措。请先到项目目录下，使用以下命令清理一下 gradle的缓存，再且试试：

```


//Windows小伙伴们用这个   
gradlew build --refresh-dependencies


//Mac小伙伴们用这个
./gradlew build --refresh-dependencies  

```


# 结语

以上是本博客的全部内容，博客测试用的 Demo 我放在了 GitHub 上，地址在此：[DR_MavenDemo](https://github.com/DRPrincess/DR_MavenDemo),有问题欢迎提 issue。

目前来看，我脑洞突如其来的 Maven 系列，到此算是告一段落了，一系列下来，触到的几乎都是我的知识盲点，每当我开始不知所谓的想飘飘然时，时刻提醒自己，我还只是一井蛙。

最后，年后的第一篇博客，祝大家，狗年快乐呢～



# 参考博客

[《Nexus私服搭建、配置、上传snapshot》](http://blog.csdn.net/buptzhengchaojie/article/details/51683672)

[《私服release库和snapshot库的正确使用方式》](http://www.bijishequ.com/detail/535006?p=)

[《导入第三方Jar包到Nexus私服》](http://blog.csdn.net/qq3516744991/article/details/70836292)

[《强制清除 gradle 依赖缓存》](http://blog.csdn.net/ziwang_/article/details/76383203)



---

刚刚开通了个人微信公众号，最新的博客，好玩的事情，都会在上面分享，欢迎关注，让我们一起学习和成长。

<div  align="center">    

![微信公众号](http://oriwplcze.bkt.clouddn.com/qrcode_for_gh_e8f891ce77fb_258.jpg)

</div>
