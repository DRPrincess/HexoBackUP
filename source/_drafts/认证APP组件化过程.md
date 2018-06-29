
# 第一步：迁移到 Gradle 4.1 (组件化中有用到 gradle 的属性)

Q1:

> Error:(138, 0) Cannot set the value of read-only property 'outputFile' for ApkVariantOutputImpl_Decorated{apkData=Main{type=MAIN, fullName=debug, filters=[]}} of type com.android.build.gradle.internal.api.ApkVariantOutputImpl.
<a href="openFile:/Users/admin/AndroidStudioProjects/rz-android/app/build.gradle">Open File</a>

```
//修改生成的apk名字 “advisor-release-1.7.0(43)-20151212”
applicationVariants.all { variant ->
     variant.outputs.each { output ->
         def appName = 'renzheng'
         def oldFile = output.outputFile as File
         def releaseApkName

         releaseApkName = appName + "-" + variant.buildType.name + "-${defaultConfig.versionName}(${defaultConfig.versionCode})-" + getDate() + ".apk"
         output.outputFile = new File(oldFile.parent, releaseApkName)
     }
}

//修改后

     android.applicationVariants.all { variant ->
              variant.outputs.all {
                outputFileName = 'renzheng' + "-" + variant.buildType.name + "-${defaultConfig.versionName}(${defaultConfig.versionCode})-" + getDate() + ".apk"

              }
          }

```

Q2:支持库的版本不统一问题

![](http://oriwplcze.bkt.clouddn.com/23ad69bac2e3411e72c789efda09f4da.png)

rootProject的build.gradle
```
ext {
    compileSdkVersion = 26
    buildToolsVersion = "26.0.2"
    minSdkVersion = 15
    targetSdkVersion = 25
    versionCode = 165
    versionName = "3.0.0"
    supportLibraryVersion = '25.3.1'
}

```
module的build.gradle
```
compile "com.android.support:appcompat-v7:${rootProject.ext.supportLibraryVersion}"
    compile "com.android.support:design:${rootProject.ext.supportLibraryVersion}"
    compile "com.android.support:support-v4:${rootProject.ext.supportLibraryVersion}"
    compile "com.android.support:recyclerview-v7:${rootProject.ext.supportLibraryVersion}"
    compile "com.android.support:cardview-v7:${rootProject.ext.supportLibraryVersion}"

```

Q3:Error:Execution failed for task ':basiclib:processDebugAndroidTestResources'.
> Error: more than one library with package name 'android.support.test'

androidTestCompile("com.android.support.test.espresso:espresso-core:${rootProject.ext.espressoVersion}", {
       exclude group: 'com.android.support', module: 'support-annotations'
   })

其他版本未经过充分测试，但已发现，3.0.2 会出现这个问题，2.2.2不会

# 第一步：整理 BaseComponent

这一步太麻烦，因为依赖了 chemao-sdk 和 chemao-lib ,相互调用混杂错乱。

两种方案：

方案一： 不整理 chemao-sdk 和 chemao-lib，先做组件化

方案二： 整理 chemao-sdk 和 chemao-lib，方便 BaseLib BaseRes

方案一：
