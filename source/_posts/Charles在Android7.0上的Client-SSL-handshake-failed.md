
---
title: Charles 在 Android 7.0 上的 Client SSL handshake failed
date: 2018-01-09 15:50:00
tags:
---

# 成功触发隐藏 BUG

打 Release 包提测后，用 Charles 代理项目，发现在某些设备上会代理失败。而且很无语的是，我的小伙伴们都没有出现这个问题，只有我总是代理失败。这种感觉有种莫名的熟悉，我知道我可能又要触发一个隐藏 BUG 了。


看下代理失败的具体表现：


![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/bdea180e72ebe2d254a69aa8f81067bb.png)

Client SSL handshake failed: An unknown issue occurred processing the certificate (certificate_unknown)


- Android 6.0 debug 和 release 版本表现正常
- Android 7.0 debug 版本代理正常，release 版本失败
- Android 8.0 debug 版本代理正常，release 版本失败



然后经过网络搜索和项目的特色配置等各种排查，我发现了一丝线索，首先在 AndroidManifest.xml 中的 application 标签多了平常没有看到过的一个属性 networkSecurityConfig：

```

<application
       android:networkSecurityConfig="@xml/network_security_config"
       //...其他属性省略
     >

    //....
</application>

```

res/xml/network_security_config.xml 的内容如下：

```
<?xml version="1.0" encoding="utf-8"?>
<network-security-config>
    <debug-overrides cleartextTrafficPermitted="true">
        <trust-anchors>
            <certificates src="system" />
            <certificates src="user" />
        </trust-anchors>
    </debug-overrides>
</network-security-config>

```

其中 **debug-overrides** 在我眼中几乎是大写加粗般的效果，猜测 release 不能代理十有八九是因为这个原因。

然后我就把它给注释掉，换成 `<base-config>`,  重新打 release 包试了一下，果然它可以正常代理了。

解决问题之后，心中还存在几个疑问：

- networkSecurityConfig 是什么？
- 为什么Android 6.0 正常, Android 7.0 就不行了呢？


这些疑问在[
官方文档-网络安全性配置](https://developer.android.com/training/articles/security-config)上都得到了很好的解释，下面是我的答案。


# networkSecurityConfig 的意义

官方文档对它的解释是：

> 网络安全性配置特性让应用可以在一个安全的声明性配置文件中自定义其网络安全设置，而无需修改应用代码

networkSecurityConfig 是网络安全性配置特性，而res/xml/network_security_config.xml 就是相关的自定义配置文件。

在自定义配置文件中可以做这四种事：
- 自定义信任锚：针对应用的安全连接自定义哪些证书颁发机构 (CA) 值得信任。

    对应的标签是 `<trust-anchors> `，上文中也有出现，中间包含信任的证书。支持的证书可以分三类，system，user,raw/xxx,分别是设备系统证书，设备用户添加证书，应用 raw 文件夹配置的证书文件。

- 仅调试重写：在应用中以安全方式调试安全连接，而不会增加已安装用户的风险。

    这个就是上文中的罪魁祸首 `<debug-overrides>`，用上了这个，就能保证只在 `debuggable = true ` 的调试模式下，信任标签内的证书了。

- 明文通信选择退出：防止应用意外使用明文通信。

   就是上文出现的`cleartextTrafficPermitted="true"`，含义是是否允许明文传输，例如 https 通信中突然出现了 http 明文通信，可以用这个属性决定是允许继续通信或者直接
    退出。

- 证书固定：将应用的安全连接限制为特定的证书。

   使用的标签是 `<pin-set>`,不知道什么情况下会用到这个，有兴趣的可以自己看[文档](https://developer.android.com/training/articles/security-config)



# 为什么 Android 7.0 开始就需要特殊处理？

出于网络安全的角度考虑，默认情况下，面向 Android 7.0 的应用开始只信任系统提供的证书(system)，且不再信任用户添加的证书(user)。

默认配置如下：

 ≥ Android 7.0：

```
<base-config cleartextTrafficPermitted="true">
    <trust-anchors>
        <certificates src="system" />
    </trust-anchors>
</base-config>

```


< Android 7.0:

```
<base-config cleartextTrafficPermitted="true">
    <trust-anchors>
        <certificates src="system" />
        <certificates src="user" />
    </trust-anchors>
</base-config>

```


Charles 代理 https 的时候，需要在手机上安装对应的 charles-ssl 证书，这个证书属于用户级别的证书。所以，同样的情况下，Android 6.0 可以被代理成功，而Android 7.0 及以上都显示 lient SSL handshake failed。所以如果想代理 Android 7.0及以上版本，需要手动设置 application的 networkSecurityConfig 属性。

很明显，这个问题已经被其他小伙伴发现，但是不知道出于什么考虑 ` <debug-overrides> ` 限制，导致了上述发生的问题。经过和相关他同事沟通，没有加这个限制的必要，已经去掉了。



# 写在后面

最近换了新的工作，因为要重新熟悉项目代码和工作流程和节奏，博客和公众号很久都没更新了，很感谢小伙伴们还没有取关我，并且还收到几条让人非常感动的私信，其中还有个非常有爱的妹纸，但是看到的时间比较晚，不在24小时内就没法回复了，非常遗憾。

总之谢谢朋友们，接下来博客和公众号会正常更新的，欢迎点赞，留言，私信交流哦！

最后，万圣节快乐！


---


<center>
<font color=gray>欢迎关注博主的微信公众号，快快加入哦，期待与你一起成长！</font>
<img src="http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/qrcode_130.png" width="130" height="130" />
</center>
