
---
title: Android-7.0系统安装异常之解析包错误
date: 2017-12-27 11:20:00
tags:
---


如果你像我一直怀疑自己是 bug 体质，那么只能说，我们都会的太少了。

<!--more-->

# 关于这个毛茸茸的小错误

最新在开发一个新的 APP ，自己手动写了版本更新，测试发现，覆盖安装的时候，在 Android 7.0 系统上出现解析包错误。

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/dc88dc9d5f71ecbff76d988e82228397.png)



报错信息：


![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/8285ee6fa3afa5ecf2e5fd5809127c03.png)


核心报错信息：

```
java.lang.SecurityException: Permission Denial:
opening provider android.support.v4.content.FileProvider from ProcessRecord{cc3ad2316425:
com.android.packageinstaller/u0a21} (pid=16425, uid=10021) that is not exported from uid 10340
```


关于安装失败，案发地点的代码：


```
/*******************错误版***************************／
/**
 * 安装 apk 文件
 * @param apkFile apk 文件
 */
private void installApk(File apkFile){
    Intent intent = new Intent(Intent.ACTION_VIEW);
    Uri apkUri = null;
    //判断版本是否是 7.0 及 7.0 以上
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.N) {
        apkUri = FileProvider.getUriForFile(getActivity(), BuildConfig.APPLICATION_ID + ".fileProvider", apkFile);
        //添加这一句表示对目标应用临时授权该Uri所代表的文件
      intent.addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION);
    } else {
        apkUri = Uri.fromFile(apkFile);
    }
    //由于没有在Activity环境下启动Activity,所以设置下面的标签
    intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);

    intent.setDataAndType(apkUri,
            "application/vnd.android.package-archive");
    startActivity(intent);
}

```


当发现是 7.0 系统上才会出现的问题之后，再联系报错信息，很容易就想到 FileProvider 的权限问题，然而并没有什么用，我还是不知道怎么回事。对比之前实现的版本更新的代码，定位到 `intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);` 这句代码，当把这句话放在前面，就是正常的。

```
/**
 * 安装 apk 文件
 * @param apkFile apk 文件
 */
private void installApk(File apkFile){
    Intent intent = new Intent(Intent.ACTION_VIEW);
    //放在此处
    //由于没有在Activity环境下启动Activity,所以设置下面的标签
    intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);

    Uri apkUri = null;
    //判断版本是否是 7.0 及 7.0 以上
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.N) {
        apkUri = FileProvider.getUriForFile(getActivity(), BuildConfig.APPLICATION_ID + ".fileProvider", apkFile);
        //添加这一句表示对目标应用临时授权该Uri所代表的文件
        intent.addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION);
    } else {
        apkUri = Uri.fromFile(apkFile);
    }

    intent.setDataAndType(apkUri,
            "application/vnd.android.package-archive");
    startActivity(intent);
}

```

# 错误原因

看一下核心报错

> java.lang.SecurityException: Permission Denial: opening provider android.support.v4.content.FileProvider from ProcessRecord{cc3ad23 16425:com.android.packageinstaller/u0a21} (pid=16425, uid=10021) that is not exported from uid 10340

 这个是权限不足的提示,Android 7.0 之后通过 FileProvider 共享文件，因为 exported为 false，所以需要给目标应用，授予临时权限，加上 Intent.FLAG_GRANT_READ_URI_PERMISSION 参数。

 很明显，我已经加了，但是很不幸，我们在添加之后， `intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK)` 把添加的给替换掉了，因此实际上还是未给权限。

 这是因为对 setFlags() 和 addFlags() 认知不清，错误使用导致的，最后，将 setFlags()操作放在 addFlags() 之前解决了这个问题。

### intent.setFlags() 和 intent.addFlags() 的区别

 setFlags():为intent 设置特殊的标志，会覆盖 intent 已经设置的所有标志。

```
 public Intent setFlags(int flags) {
     mFlags = flags;
     return this;
 }

```

 addFlags():为intent 添加特殊的标志，不会覆盖，只会追加。

```
  public Intent addFlags(int flags) {
      mFlags |= flags;
      return this;
  }

```




# 拓展知识补充


### Android 7.0 行为变更，通过 FileProvider 在应用中共享文件

Android 7.0 除了提供诸多新特性和功能外，还对系统和 API 行为做出了各种变更。其中有一条，对日常开发的适配影响是非常大的。传递软件包网域外的 file:// URI 可能给接收器留下无法访问的路径。因此，尝试传递 file:// URI 会触发 FileUriExposedException。解决方法是使用 FileProvider 将 file:// URI 转换为 content:// URI 。

FileProvider 的使用方法网上有很多，这里不多说了，附上 Google 官方文档 [FileProvider](https://developer.android.com/reference/android/support/v4/content/FileProvider.html?)


### Intent.FLAG_GRANT_READ_URI_PERMISSION & android:grantUriPermissions &  context.grantUriPermission(）三者的区别

 刚开始用 FileProvider 的时候，并不是很懂它，只是 copy & paste，所以对这三个 grantUriPermissions 很是傻傻分不清楚。遇到这么问题专门去查了一下。



#### android:grantUriPermissions

 这个是定义在 FileProvider 中的属性，作用是允许对 ContentProvider 通信的目标应用临时授权。

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/e7cf3fc2a041720157312d412856b50b.png)

这个属性默认是 false，但是要求必须设置为 true，否则直接报错 `java.lang.SecurityException: Provider must grant uri permissions`

我们可以在 FileProvider 源码中看到对这个值的限制：

```
  public void attachInfo(Context context, ProviderInfo info) {
    super.attachInfo(context, info);
    if(info.exported) {
        throw new SecurityException("Provider must not be exported");
    } else if(!info.grantUriPermissions) {
        throw new SecurityException("Provider must grant uri permissions");
    } else {
        this.mStrategy = getPathStrategy(context, info.authority);
    }

  }

```


#### Intent.FLAG_GRANT_READ_URI_PERMISSION

 这个是 Intent 的一个 Flag 值，会临时授予目标引用对所传递 Uri 的临时访问权限。  
 这个临时访问权限在setResult()数据接收方 Activity 还在栈中时，都是有效的，如果弹出了，临时访问权限自动取消。


通过 `addFlags()`和`setFlags()` 两种方式添加：

```
 //addFlags()方式
 intent.addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION);

 //setFlags()方式
 intent.setFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION);

```

#### context.grantUriPermission(）

这个方法和使用 `intent.addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION)` 可以任选其一，因为它们的效果是一样的，都是对目标引用临时授权。

区别在于 context.grantUriPermission(）的临时访问权限不会在数据接收页面关闭时自动取消，只有手动调用 revokeUriPermission() 或者整个应用重新启动，才会失效。

 grantUriPermission(String toPackage, Uri uri,@Intent.GrantUriMode int modeFlags)需要传递三个参数：

 |参数名称|参数含义|
 |---|---|
 |toPackage|你想要授权的目标应用，记住是目标应用|
 |uri|传递的 Uri|
 |modeFlags|需要的临时授予的权限们|


使用方法：

```
//判断版本是否是 7.0 及 7.0 以上
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.N) {
       apkUri = FileProvider.getUriForFile(getActivity(), BuildConfig.APPLICATION_ID + ".fileProvider", apkFile);
       //设置 intent 目标应用类型和传递的数据，这句要放在查询之前
       intent.setDataAndType(apkUri,
               "application/vnd.android.package-archive");

       //查询所有符合 intent 跳转目标应用类型的应用
       List<ResolveInfo> resInfoList = getActivity().getPackageManager().queryIntentActivities(intent, PackageManager.MATCH_DEFAULT_ONLY);
       //然后全部授权
       for (ResolveInfo resolveInfo : resInfoList) {
           String packageName = resolveInfo.activityInfo.packageName;
           getActivity().grantUriPermission(packageName, apkUri, Intent.FLAG_GRANT_WRITE_URI_PERMISSION | Intent.FLAG_GRANT_READ_URI_PERMISSION);
       }

   }

```

特别注意的地方：

我第一次测试使用这个方法的时候，报错了，报错信息是
```
java.lang.SecurityException: Permission Denial:
opening provider android.support.v4.content.FileProvider from ProcessRecord{cc3ad2316425:
com.android.packageinstaller/u0a21} (pid=16425, uid=10021) that is not exported from uid 10340
```

原因是我非常蠢地把 intent.setDataAndType() 放在了查询后面，所以 queryIntentActivities 没查到 `com.android.packageinstaller` 这个应用，也就没授权，大家使用的时候要注意顺序。


### Intent.FLAG_ACTIVITY_NEW_TASK 的意义

Intent 的意思是意图，大白话翻译过来就是，定义即将要发生的动作。 使用 Intent ,可以在 Activity（前台页面）， BroadcastReceiver（广播） 和 background Service （后台服务）之间做数据传递和其他交互。Flag 是 Intent 中扮演是辅助者的的角色，作用是添加相应的配置，让不同组件之间的交互更加方便。Android 也为 Intent 提供了很多 Flag， Intent 可以通过 setFlags() 或 addFlags() 添加一个或者多个 Flag，两个方法的区别上面说过了，这里就不啰嗦了。

Intent.FLAG_ACTIVITY_NEW_TASK 的含义是开启一个新的任务栈来装载目标页面。以这种方式启动activity，相当于目标 Activity 的 launchMode 属性设置 "singleTask" 。

注意如果试图从非 Activity 的非正常途径，比如从一个 Receiver 中启动一个 Activity，则 Intent 必须要添加 FLAG_ACTIVITY_NEW_TASK 标记。

如果是从 Activity 中跳转到 Activity ，会有以下效果。

写了一个小 Demo 测试，在 MainActivity 中通过 Intent 打开 SecondActivty，Intent 设置 FLAG_ACTIVITY_NEW_TASK。

```
    Intent intent = new Intent(MainActivity.this,SecondActivity.class);
    intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
    startActivity(intent);

```

测试结果如下：

1.如果 SecondActivity 在 AndroidManifest.xml 没有设置 Task Affinity，创建的 SecondActivity 的栈和 MainActivity 的栈一样。

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/3d684dfddf6c5c459e475cacb30e7a17.png)



2.如果 SecondActivity 在 AndroidManifest.xml 设置 Task Affinity 属性，SecondActivty 才会创建在一个新栈中。

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/d45689d16d59c3c78a29db62c8ef11f7.png)



![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/72d95bb6699504447915adc22a6ef178.png)



Task Affinity 属性导致表现不同的原因是，当使用 singleTask 方式启动一个Activity 时， 系统首先会查找有没有 Task Affinity 相同的栈存在，如果有存在，直接压入栈，没有则会重新创建。当没有设置时，默认 Task Affinity 是 packageName，所有的 Activity 的 Task Affinity 都一样，于是表现和 Standard Mode 没有区别。


# 写在最后

遇到这个问题，觉得很奇怪，有点解释不同，其实归根结底也是自己太菜了而已，很对东西都不知道，copy & paste 在日常工作中是常态，但终究不是学习之道，功能实现完还是要回来找到真正原因才会安心，也能查漏补缺。

与诸君共勉，加油。


---

刚刚开通了个人微信公众号，最新的博客，好玩的事情，都会在上面分享，欢迎关注，让我们一起学习和成长。

<div  align="center">    

![微信公众号](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/qrcode_for_gh_e8f891ce77fb_258.jpg)

</div>
