---
title: Android-使用 SetColorFilter 神奇地改变图片的颜色
date: 2018-04-10 10:57:45
tags:
---

迭代老项目，发现一个小彩蛋～
<!--more-->

# 无意中 Get 一个新技能

公司的移动端应用，最近要换一个UI主题色，在更换一个图片控件的选中与未选中效果时，本以为需要UI配合给新颜色切图的，然而并不是，直接使用setColorFilter()改颜色就好了。

无知的我很开心 get 了一个新技能！

# 这件小事的详情

现在，有一个效果展示是这样的，选中某个车型时，显示选中的颜色，是主题色红色。

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/646D48768CC5BAB2662C9E295FE92C2E.jpg)


现在，我们的产品和UI宝宝决定要把主题色改成蓝色，于是选中效果要像下面这样：

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/2D2974E3BFC803C9392EAF0415A66538.jpg)


看项目代码的时候，然后很惊讶的发现图片的原图，是这个样子的：

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/e53dfe62f49a0ec9c0b712403b91c3b1.png)



样式布局：

```
//UI布局
<ImageView
    android:id="@+id/icon"
    android:layout_width="85dp"
    android:layout_height="38dp"
    android:layout_marginTop="5dp"
    tools:src="@drawable/icon_carlevel_race"
    android:scaleType="fitCenter" />

```


代码控制颜色显示：

```

//定义选中的颜色
int checkColor = context.getResources().getColor(R.color.theme_red);

//当选中该项时，显示选中颜色，否则显示未选中颜色
viewHolder.icon.setColorFilter(selectPosition==position? checkColor :Color.TRANSPARENT);

```

于是针对这个控件改颜色的需求，就只需要修改 `checkColor`  修改成蓝色就好了，这神奇的操作在很明显在于  `setColorFilter` 这个方法，于是我点击进去，看到源码中这个方法的实现。


```
/**
 * Set a tinting option for the image. Assumes
 * {@link PorterDuff.Mode#SRC_ATOP} blending mode.
 *
 * @param color Color tint to apply.
 * @attr ref android.R.styleable#ImageView_tint
 */
@RemotableViewMethod
public final void setColorFilter(int color) {
    setColorFilter(color, PorterDuff.Mode.SRC_ATOP);
}

```

注释说明该方法可为 ImageView 设置着色选项。内部实现是调用了该方法的重载方法，默认参数是 `PorterDuff.Mode.SRC_ATOP`,图形混合渲染模型之一,此参数是图片改变颜色实现的重点，下面简单介绍一下。

# PorterDuff.Mode

PorterDuff，一个陌生的单词，百度翻译和谷歌翻译都查无来处，原因在于它是一个组合词汇，来源于 Tomas Proter（托马斯波特）和 Tom Duff（汤姆达）两个名字。这俩人是在图形混合方面的大神级人物,他们在 1984 年发表了论文，第一次提出了图形混合的概念，也是取了两人的名字命名。

Android PorterDuff.Mode 便是提供了图片的各种混合模式，可以分为两类：

- Alpha compositing modes（由俩大神的定义，包含 alpha 通道因素）
- Blending modes（不是俩大神的作品，不包含 alpha 通道因素）

具体的可以看官方文档对 [PorterDuff.Mode
](https://developer.android.com/reference/android/graphics/PorterDuff.Mode.html)的介绍，我这里只说涉及到的 `SRC_ATOP`。



既然混合，两个图片，源图片和目标图片，如下：

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/4c71c7f8b607af20690371e6c12cc0f7.png)

SRC_ATOP 混合模式效果如下图，只保留源图片和目标图片的相交部分，其他部分舍弃：

![](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/c5f6c1538694ac1057eac8daa4d2109d.png)


按照上面的示例来对应，那么我们的源图就是蓝色，目标图片是小汽车图案。蓝色和小汽车图案的重合部分，只有线条，所以能达到改变颜色的效果。

那么，就算我们的小汽车图片是任何颜色，都能达到不换图片，改选中颜色的效果。

在这种简单的图片颜色上，合理使用 SetColorFilter() ，可以为 UI 小哥哥小姐姐们节省了不少切图，同样能缩小了 APK 的体积。

# 最后

接手老项目的迭代开发，是十分奇妙的旅程，谁知道下面等待我的是一个深坑还是华丽的骚操作。



---

刚刚开通了个人微信公众号，最新的博客，好玩的事情，都会在上面分享，欢迎关注，让我们一起学习和成长。

<div  align="center">    

![微信公众号](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/qrcode_for_gh_e8f891ce77fb_258.jpg)

</div>
