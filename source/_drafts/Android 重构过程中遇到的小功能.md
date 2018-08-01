
1. Android Studio  SelectorChapek

2. 《阿里巴巴Java开发手册》 和对应的 Android  Studio 插件

3. POJO (Plain Ordinary Java Object) 只有 get set 方法的普通纯粹的 Java 类

4. 应用内是一方库，跨应用是二方库，那三方库？

5. Object Oriented Programming，OOP 面向对象编程

6. toString()方法
【强制】POJO 类必须写 toString 方法。使用 IDE 的中工具:source> generate toString 时，如果继承了另一个 POJO 类，注意在前面加一下 super.toString。 说明:在方法执行抛出异常时，可以直接调用 POJO 的 toString()方法打印其属性值，便于排 查问题。

7. Java 集合类
【参考】合理利用好集合的有序性(sort)和稳定性(order)，避免集合的无序性(unsort)和 不稳定性(unorder)带来的负面影响。 说明:有序性是指遍历的结果是按某种比较规则依次排列的。稳定性指集合每次遍历的元素次 序是一定的。如:ArrayList 是 order/unsort;HashMap 是 unorder/unsort;TreeSet 是 order/sort。

8. 卫语句／策略模式和状态模式
动机：条件表达式通常有2种表现形式。第一：所有分支都属于正常行为。第二：条件表达式提供的答案中只有一种是正常行为，其他都是不常见的情况。

这2类条件表达式有不同的用途。如果2条分支都是正常行为，就应该使用形如if…..else…..的条件表达式；如果某个条件极其罕见，就应该单独检查该条件，并在该条件为真时立刻从函数中返回。这样的单独检查常常被称为“卫语句”。

 Replace Nested Conditional with Guard Clauses

  之所以说状态模式是策略模式的孪生兄弟，是因为它们的UML图是一样的，但意图却完全不一样，策略模式是让用户指定更换的策略算法，而状态模式是状态在满足一定条件下的自动更换，用户无法指定状态，最多只能设置初始状态。
9. 【强制】在使用正则表达式时，利用好其预编译功能，可以有效加快正则匹配速度。
Pattern pattern = Pattern.compile(规则);
  https://blog.csdn.net/devil_cpp/article/details/7381662


10. alter+enter 抓取字符串到 String.xml


11.
No signature of method: static org.gradle.api.java.archives.Manifest.srcFile() is applicable for arg
 https://blog.csdn.net/lx6101989/article/details/76198693




12. 组件化Library形式的Module怎么使用BuildConfig的配置信息

主module对library module的依赖都是release依赖


https://blog.csdn.net/mybook1122/article/details/72902437

13. Android Studio快捷键switch case 轻松转换为if else
alt-enter

14. 注释模版

File and code Templates 中有对应的${user}
live Templates $user$中,要设置config 中 userkey值对应的函数。
user() 获取的是Mac 电脑上的用户名，通过下面这个方法可以改变

https://jingyan.baidu.com/article/f25ef254b9acb5482c1b82a9.html

记住重启后有效

who 命令行可以检测。


15. Error:Execution failed for task ':onlinecertcomponent:preDebugBuild'.
Android dependency 'com.android.support:recyclerview-v7' has different version for the compile (25.3.1) and runtime (27.1.1) classpath. You should manually set the same version via DependencyResolution

rootProject中设置 一下代码有效：
```
subprojects {
    project.configurations.all {
        resolutionStrategy.eachDependency { details ->
            if (details.requested.group == 'com.android.support'
                    && !details.requested.name.contains('multidex') ) {
                detailsc.useVersion "27.1.1"
            }c
        }
    }
}
```

解决参考链接：

https://stackoverflow.com/questions/44653261/android-dependency-has-different-version-for-the-compile-and-runtime


16. adb install -r apk安装 apk 出现Failed to install app-debug.apk: Failure [INSTALL_FAILED_TEST_ONLY: installPackageLI]

解决方法: adb install -t


17. Failure [INSTALL_FAILED_CONFLICTING_PROVIDER]
个推注册的服务，gradle 给定的 packagename 都是 com.really.car 是一样的。

18. transformDexArchiveWithExternalLibsDexMergerForDebug
这个是因为支持库版本不一致问题

19. Android 6.0 权限在 library module 中的使用问题

permissionsdispatcher 这个三方库，要把annotationProcessor的依赖也要放到 library的 build.gradle 中才可以
https://www.jianshu.com/p/71cd3485bad7

20.
Error:Execution failed for task ':carbrandcomponent:transformNativeLibsWithMergeJniLibsForDebug'.
> Unexpected scopes found in folder '/Users/admin/AndroidStudioProjects/AAAA/ReallyCarandroidnew/carbrandcomponent/build/intermediates/transforms/mergeJniLibs/debug'. Required: PROJECT. Found: EXTERNAL_LIBRARIES, PROJECT, SUB_PROJECTS

clean 之后解决，之前 carBrand是 application ,后来改成library,不知道和这个有没有关系。

21. ARouter 不支持 fragment中startActivityForResult.

22. No signature of method: static org.gradle.api.java.archives.Manifest.srcFile() is applicable for arg
Manifest 改为 manifest 即可。这个很奇怪的地方就是，一旦复制，就会自动变大写。
https://blog.csdn.net/lx6101989/article/details/76198693

23. SharedPreferences 的 getStringSet 问题。返回结果不能直接修改，要浅拷贝一下。

24. progressBar 的setProgress  无效。

25. Error:Execution failed for task ':citycomponent:compileDebugJavaWithJavac'.
> permissions.dispatcher.processor.exception.MixPermissionTypeException: Method 'startLocation()' defines 'android.permission.WRITE_SETTINGS' with other permissions at the same time.

- 正常(Normal Protection)权限
- 危险(Dangerous)权限
- 特殊(Particular)权限
- 其他权限（一般很少用到）

特殊权限，顾名思义，就是一些特别敏感的权限，在Android系统中，主要由两个

SYSTEM_ALERT_WINDOW，设置悬浮窗，进行一些黑科技
WRITE_SETTINGS 修改系统设置


26. 命令行运行 Android 项目
1.修改local.properties
2../gradlew init wrapper
3../gradlew build
4.apk 已经生成，找到然后安装
https://blog.csdn.net/angcyo/article/details/50526462


27. PopupWindow 在 Android 7.0 手机上显示全屏占满，showAsDropDown 的原因和解决方法。
https://blog.csdn.net/fxdiql/article/details/55253546/

28. 车猫原有的路由解析中，发现一个问题：
<com.really.car.cardetail.CarDetailActivity>/((cardetail.htmlid=\d+)|(show\d+.html)|((webapp|weixin|wap)/index.phpapp=(show|cardetail)&amp;id=\d+))</com.really.car.cardetail.CarDetailActivity>

老项目：拿到的字符串是：
/((cardetail.htmlid=\d+)|(show\d+.html)|((webapp|weixin|wap)/index.phpapp=(show|cardetail)&id=\d+))
新项目拿到的字符串是：
/((cardetail.htmlid=d+)|(showd+.html)|((webapp|weixin|wap)/index.phpapp=(show|cardetail)&id=d+))

观察得到的区别，新项目xml 解析得到的字符串，转义字符“\d” 变成了“d”

具体原因还没有查到，下面是比较有参考价值的博客
https://www.jianshu.com/p/7eee7b1382a2
https://www.jianshu.com/p/c7aaaa0ea081

29. Gradle 4.3 开始的构建审视功能
https://blog.csdn.net/IO_Field/article/details/79904323

30. gradle 统一三方库版本的一个方法：
https://blog.csdn.net/x1971481259/article/details/78214411

31. Error:Execution failed for task ':app:preDebugAndroidTestBuild'.
> Conflict with dependency 'com.android.support:support-annotations' in project ':app'. Resolved versions for app (26.1.0) and test app (27.1.1) differ. See https://d.android.com/r/tools/test-apk-dependency-conflicts.html for details.

1.查看依赖树的方式
2.解决三方库依赖冲突的方法
https://blog.csdn.net/fei20121106/article/details/54292892
https://developer.android.com/studio/known-issues
https://guides.gradle.org/building-android-apps/
