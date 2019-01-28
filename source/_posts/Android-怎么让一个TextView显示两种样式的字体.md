---
title: Android-如何让优雅地让一个TextView显示两种样式的字体
date: 2018-04-20 19:55:04
tags:
---

分享优雅写代码的一个小技巧～
<!--more-->

# 前言

这是一个很常见的需求，一般出现在有单位的数据展示上面。例如下面的两个例子，来源于我司项目的某一个页面。

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/3f26803d70493844071c3c3c29ec0bdb.png)


![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/35ed97810a0d59a5a0decfd8d06924a0.png)


**如果让你实现图一的`3.07万元` 和图二的 `您的估价低于 80% 车主的估价`，你会怎么布局?**


如果是以前的我:

图一的`3.07万元` 会换成 `3.07` 和 `万元` 两个 TextView 显示，因为俩大小不一样，这个理由尚能接受。

图二，会换成 3 个 TextView 显示，因为颜色不一样，而且颜色不一样的还在中间。嗯，这个说出来就感觉有点丢脸了。

然而现在我想说，事实上，这两者都可以只用一个 TextView 展示。

而且方式还不只一种：

- TextView 显示 Html 样式
- SpannableString

下面分别用这两种方式实现一下。


# Html 方式

有这么一个方法：

```
Html.fromHtml()

```
该方法可以将一段 html 字符串显示为TextView 可以显示的样式文本。


于是，首先根据需求编写好 html ，通过该方法转换文本，拿给 TextView 显示即可。

如下：

```
tv1.setText(Html.fromHtml("<font color=\'#217aff\' ><big>3.07</big></font><font color=\'#217aff\' ><small>万元</small></font>"));

tv2.setText(Html.fromHtml("<font color=\'#656565\'>您的估价</font><font color=\'#f48421\'>低于</font><font color=\'#656565\'>80%车主的售价。</font>"));
```

显示效果：

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/f98cf72aed2017b2529ac219a40c7756.png)


另外，大家注意到上面 `3.07万元`中两个字体大小不同，我是通过 `<big>3.07</big>` `<small>万元</small>` 去控制的。这个是因为 Android 中只支持 <font> 标签的 color 和 face 标签，不支持 size 标签。所以像下面的样式，无法显示 size 大小的。

```
"<font color=\'#217aff\' size=\'18px\' >3.07</font><font color=\'#217aff\' size=\'10px\'>万元</font>"

```

想控制大小只能通过 `<big>` 和 `<small>` 标签，但是两者的大小比较固定，没办法精确控制。如果遇到需要控制字体大小的需求，可以考虑用下面的 SpannableString 来实现。


# SpannableString 方式

SpannableString 实现了 CharSequence 接口，所以无须转换， TextView 可以直接通过 setText() 方法显示。

先使用 SpannableString 实现上面的例子感受一下：

```
// “3.07 万元”的实现方式
SpannableString s1 = new SpannableString("3.07万元");

s1.setSpan(new AbsoluteSizeSpan(18, true), 0, s1.length()-2, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
s1.setSpan(new AbsoluteSizeSpan(10, true), s1.length()-2, s1.length(), Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);

tv3.setTextColor(Color.parseColor("#217aff"));
tv3.setText(s1);

// “80%车主的估价”的实现方式
SpannableString s2 = new SpannableString("您的估价低于80%车主的估价");

s2.setSpan(new ForegroundColorSpan(Color.parseColor("#656565")), 0, 4, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
s2.setSpan(new ForegroundColorSpan(Color.parseColor("#f48421")), 4, 6, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
s2.setSpan(new ForegroundColorSpan(Color.parseColor("#656565")), 6, s2.length(), Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);

tv4.setText(s2);
```

显示效果：

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/44a6b98fba6ea90260c1095c05462e65.png)


是不是很赞，来看一下实现过程：

第一步:创建一个SpannableString。

```
SpannableString spannableString = new SpannableString("3.07万元");

```

第二步：使用 setSpan() 给 SpannableString 设置样式。

```
spannableString.setSpan(Object what, int start, int end, int flags);


```
参数的意义是：

- what：对SpannableString进行润色的各种Span；
- start：需要润色文字段开始的下标；
- end：需要润色文字段结束的下标；
- flags：标志位，决定操作的范围是否包含开始和结束下标，有四个参数可选,为什么有四个？因为分别代表着 start 至 end 的四种集合开闭模式。
  - SPAN_INCLUSIVE_EXCLUSIVE：[start,end) 包括开始下标，但不包括结束下标
  - SPAN_EXCLUSIVE_INCLUSIVE：(start,end] 不包括开始下标，但包括结束下标
  - SPAN_INCLUSIVE_INCLUSIVE：[start,end] 既包括开始下标，又包括结束下标
  - SPAN_EXCLUSIVE_EXCLUSIVE：(start,end) 不包括开始下标，也不包括结束下标

总体是第一个参数决定效果，后三个参数决定范围，显而易见，第一个参数是非常的需要去好好认识，把酒言欢一下的。

SpannableString 提供了很多 Span 样式供开发者们选择，有用来设置大小，颜色，下划线，甚至还有点击事件的。我找了它们的继承结构图，截来给大家看一下 Span 们的家谱。

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/193e91761cf0d3a37d52402dfef6e51b.png)


上面例子中只用到了两个样式， ForegroundColorSpan 设置字体前景颜色，AbsoluteSizeSpan 设置字体大小，非常的简单。其实 Span 们功能远不止这些，但是这里不赘述了，大家可以自己多去尝试，下面是我觉得比较好的文章链接分享。


[Android UI——SpannableString详细解析](https://juejin.im/post/5a37d0645188252582277e29)    
[一个实现微博那种#话题，@好友 符号表情的例子](https://blog.csdn.net/u011102153/article/details/52487049)


# 另外一个新 Get 的小知识点

真实的项目开发中，想 `3.07万元` 这种字符串不可能是固定好的，肯定是有服务端返回的，但是有的只是纯数字。那么除了用 `+` 号拼接，还可以这样使用：

```
 String s = String.format("%s万元",3.07);
```
关于这个 `String.format()` 方法也是最近才发现的一个小技巧，使用上感觉比`+`要更方便，也更直观，关于它的更多信息可以直接看[官方文档](https://developer.android.com/reference/java/util/Formatter.html),大概了解掌握其功能，记住几个最常用的，其他的可以随用随查。






# 最后

哈哈哈，觉得自己之前好笨拙，可能现在也是，不过有这个感觉也证明现在变的好一点了吧，虽然是一个很小的知识点。荀子老人家说了：“不积跬步，无以至千里；不积小流，无以成江海。”，希望我们都越来越好。

下篇博客见。


---

刚刚开通了个人微信公众号，最新的博客，好玩的事情，都会在上面分享，欢迎关注，让我们一起学习和成长。

<div  align="center">    

![微信公众号](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/qrcode_for_gh_e8f891ce77fb_258.jpg)

</div>
