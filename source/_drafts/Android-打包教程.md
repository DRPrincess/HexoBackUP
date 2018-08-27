

# 基本流程

1. 修改版本号 build.gradle 中的 VersionName 和 VersionCode,然后sync同步


2. 同步完成，点击Android Studio 右侧栏-gradle，选择打包任务

测试环境：app-tasks-build-assembleDebug
正式环境：app-tasks-build-assembleRelease


3. 打包完毕，apk包的位置在

app/build/outputs/apk/xxx.apk

# 如果二方库加载失败,需要将私服远程依赖替换为本地

1. 将app/build.gradle 中的二方库给注释掉
```
//compile 'com.chemao.android:chemao-lib:1.0.8@aar'
//compile 'com.chemao.android:chemao-sdk:1.2.1@aar'

```
2. app/lib 文件添加以上两个库的 aar 文件

3. 主项目的 build.gradle 中添加如下代码：


```
repositories{
    flatDir {
        dirs 'libs'
    }
}

dependencies {
  compile(name:'chemao-lib-1.0.8', ext:'aar')
  compile(name:'chemao-sdk-1.2.1', ext:'aar')
}

```
