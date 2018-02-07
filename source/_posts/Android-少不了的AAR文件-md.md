---
title: Android-少不了的 AAR 文件常识，最好知道的注意事项.md
date: 2018-01-31 10:32:29
tags:
---
# AAR，为 Android 而生。

在使用 Eclipse 开发 Android 的那个时代（其实也就几年前而已），如果想代码打包，只有 `JAR` 包一个方法，但是 `JAR` 只能把 Java 文件代码打包进去，如果要使用一个有布局和资源的库的话，除了将 `JAR` 放入 libs 外,还需要引入相关的资源和配置文件，十分不优雅。

Android Studio 出来之后，出现了一个新的方法，打包 `AAR` 文件 ，这个不仅可以把 Java 文件给打进去，还包含 AndroidManifest.xml 和资源文件等，使用的话，直接引入一个 `AAR`  文件就足够了，非常方便。


# 如何 生成 AAR 文件

AAR，名字来源于 Android Archive，见名知义，是一个 Android 库项目的二进制归档文件，使用 Android Studio ，非常简单可以生成一个  `AAR`  文件。

1.首先创建一个 Android library Module.

2.等待 Gradle 构建完毕， 就可以在`项目名称/模块名称/build/outputs/aar/`中找到这个库项目的 AAR 文件了。

3.如果修改了代码，也可以通过 Build > Make Project 的方式重新生成文件。  

![](http://oriwplcze.bkt.clouddn.com/9182de5e5cb2c4919cd72172d73d06a1.png)


其实这个过程，也生成了对应的 JAR 包，文件在`项目名称/模块名称/build/intermediates/bundles/debug(release)／classes.jar`，classes.jar 这个library 对应的 JAR 包。

![](http://oriwplcze.bkt.clouddn.com/357ba7783bf87adec2e0ac83a4685c68.png)

不过在 AAR 文件中，修改后缀名打开后，里面也有 classes.jar ,和这个 classes.jar 是同样的内容，


# AAR 文件的结构

AAR 文件的文件扩展名为 .aar，文件本身是一个 zip 文件，以下内容是必须包含的：

- /AndroidManifest.xml
- /classes.jar
- /res/
- /R.txt

此外，根据打包的 Library Module 情况不同，打包出来的 AAR 文件也可能包含以下内容：

- /assets/
- /libs/名称.jar
- /jni/abi 名称/名称.so（其中 abi 名称 是 Android 支持的 ABI 之一）
- /proguard.txt
- /lint.jar

我们拿到上个步骤中生成的 .aar 文件,手动将后缀名改成 .zip文件，解压之后的样子是这个样子的：

![](http://oriwplcze.bkt.clouddn.com/75a15c1ec3127089eac44e671fe2534d.png)



# 项目中使用 AAR 文件的两种方式

`AAR` 文件使用有两种方式，一种是使用在线的（网上的），一种是添加本地的 `AAR` 文件。

### 本地 AAR 使用

1.把 aar 文件放在目标 module 一个文件目录内，比如就放在 libs 目录内。   

2.在目标 module 的 build.gradle 文件添加如下内容：

```
repositories {  
    flatDir {  
        dirs 'libs'   
    }  
}

dependencies {  
    compile(name:'AAR的名字', ext:'aar')  
}

```
### 远程 AAR 使用

事实上，在工作项目中，远程的 AAR 文件的使用频率比本地高，因为用起来很方便，我们依赖很多的第三方库就是远程一览的方式。

远程的 AAR 使用的前提是有一个远程的库链接，然后通过 Gradle 下载依赖，因此有下面的两个步骤：

1.rootProject 的 build.gradle 中添加远程仓库地址的引用
```
allprojects {
    repositories {
        // google的官方仓库
        google()

        // 出名的公共中央仓库
        jcenter()
        mavenCentral()

        //如果有私库，也要添加链接
        //....
    }
}

```

其中 `google()` 是谷爹的官方库的仓库地址， `jcenter()`  和  `mavenCentral()` 是比较出名的的中央仓库，基本第三方库都会上传到这些仓库。 有时公司也会有自己的一些封装库，不想对外暴露，就放在自己的私有仓库中，仓库的地址也要在这里添加。


2.module 的 build.gradle 中添加具体 aar 的依赖。

```
dependencies {
    implementation 'com.android.support:support-v4:25.3.1'
}
```


# AAR 使用的注意事项

AAR 使用起来爽歪歪，但是以下问题也要注意，否则使用过程中可能引起不适～

- 应用模块的 minSdkVersion 必须大于或等于 Library Module 中定义的版本

  AAR 作为相关应用模块的一部分编译，因此，库模块中使用的 API 必须与应用模块支持的平台版本兼容，所以 AAR 中的 minSdkVersion 要尽量的设置低一点。

- 资源合并问题

 如果 aar 中有资源文件，集成过程中，很容易出现资源冲突，例如两个相同名字的图片什么的。为了避免这个问题，有两种方法，一是制定一个不用冲突的命名规范，二是 library Module 的 build.gradle 中设置资源前缀。第一种，嗯，全靠自觉，维护起来很难，推荐第二种解决方法。
  ```

  android {
    resourcePrefix "<前缀>"
  }

  ```
- aar 不能使用 assets  原始资源

  工具不支持在库模块中使用原始资源文件（保存在 assets/ 目录中）。应用使用的任何原始资源都必须存储在应用模块自身的 assets/ 目录中

- aar 的混淆

  通过对 library Module 的混淆，可以打出一个混淆后的 aar 包，这样的话，就不用要求在依赖此 aar 的同时，还要手动添加对应的混淆规则了。

  具体做法是:   

  1.将混淆规则写入 lib-proguard-rules.txt。  

  2.library Module 的 build.gradle 文件设置 `consumerProguardFiles` 属性，引用第一步创建的混淆的文件。
  ```
  android {
      defaultConfig {
          consumerProguardFiles 'lib-proguard-rules.txt'
      }
      ...
  }
  ```

  以上是默认的构建类型，如果 library Module 需要对不同的 buildType 做特别的处理，还需要专门设置。

  1.library Module 的 build.gradle 中设置 `publishNonDefault = true`，请注意，设置 publishNonDefault 会增加构建时间。

  ```
  android {
      ...
      publishNonDefault true
  }

    ```
  2.app 的 build.gradle 中设置不同构建类型的处理。
  ```

  dependencies {
      debugCompile project(path: ':library', configuration: 'debug')
      releaseCompile project(path: ':library', configuration: 'release')
  }
  ```

  在依赖本身就带有混淆规则的 AAR 的时候，AAR 的 ProGuard 文件将附加至 app 的 ProGuard 配置文件 (proguard.txt)。所以，aar 的混淆规则可能会影响 app,因此，制定 aar 的混淆规则的时候，也要谨慎一点，只包括自己的类即可，不要把范围设置太大。




# AAR 内部三方库依赖的问题

项目的开发过程中，发现一个问题：

	使用 Android Studio 打包出来的 AAR ，不会将其依赖的三方库打包进去。

举个例子，library Test 依赖了 okhttp,打包成了 Test.aar ,app 使用本地方式引用了 Test.aar，但是无法使用 okhttp，为了不报错，app还需要添加 okhttp 依赖。

Google Android Studio 的负责人在 stackoverflow 上解释了 [为什么 Android Studio 不能将多个依赖打包进一个 AAR 文件](https://stackoverflow.com/questions/20700581/android-studio-how-to-package-single-aar-from-multiple-library-projects/20715155#20715155)的原因，是因为将不同的library打包在一起，涉及到资源和配置文件智能合并，所以是个比较复杂的问题，同时也容易造成相同的依赖冲突。

官方虽然不支持，但是开发者的能力是无限的，为了解决此问题，开发出来了一个 Gradle 插件 [android-fat-aar](https://github.com/adwiv/android-fat-aar), 这种方式是抛弃 Android Studio 自带的打包 AAR 的方法，而是自己编写一个生成 AAR 的脚本。也是很厉害了，但是很不幸，目前来看 gradle 2.2.3+ 以上，这个就不适用了。

不过，不要慌，这个问题可以通过使用 Maven 依赖解决。因为 library Module 上传 Maven 后，会生成 一个 .pom 文件，记录 library Module 的依赖。当 Gradle 依赖 Maven 上的这个库时，会通过 pom 文件下载对应依赖。如果不想要对应依赖的话，可以通过下面的方法关闭 Gradle 的依赖传递。

举个例子如下：

```

//正常依赖
implementation 'com.chemao.android:chemao-sdk:1.2.3'

//关闭全部依赖传递-方法1
implementation 'com.chemao.android:chemao-sdk:1.2.3@aar'

//关闭全部依赖传递-方法2
implementation('com.chemao.android:chemao-sdk:1.2.3') {
        transitive = false
}

```

这样就皆大欢喜啦！

# 最后

依赖 Maven 上 Library Module 的前提是 Maven 上有这个库，于是，下篇博客《Android-Library Module 上传 Maven 仓库》，走起～
