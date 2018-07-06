---
title: Android-巧妙利用CompoundDrawables
date: 2018-07-05 18:18:18
tags:
---

对于提示性的小图标，View&View.setCompoundDrawables()的实现方式，明显优于 View+ImageView。

<!--more-->

# 这是很方便的一个操作

给控制设置附加图片，这类需求在实际开发中使用频率很高，例如下面：

1. 用RadioGroup 方式实现需求是最方便的，图片可以用 RadioButton 的 DrawableTop 添加。

![](http://oriwplcze.bkt.clouddn.com/e01f1827481a88152f4f338cb893daf9.png)

2. 放大镜小图标通过 EditText 的 DrawableLeft 方式实现。
![](http://oriwplcze.bkt.clouddn.com/d56c74ce052bab62dac4972c99a4fdc5.png)

以上的需求有多种方式可以实现，但在我看来，对于提示性的小图标，View&View.setCompoundDrawables()的实现方式，明显优于 View+ImageView。



# 操作步骤

#### View 添加 CompoundDrawables

一个View 可以添加四个 CompoundDrawables
```
 android:drawableLeft=""
 android:drawableRight=""
 android:drawableTop=""
 android:drawableBottom=""
```

代码中可以也有对应的方法：

```
//拿到图片
Drawable drawable = getResources().getDrawable(R.drawable.xxx);
//设置大小，注意默认的是 px, UI 图上的 dp 单位需要转换
drawable.setBounds(0, 0,  width, height);
//给View左边添加一个图片
view.setCompoundDrawables(drawable,null, null, null);
```


#### View 和 CompoundDrawable 之间的间距控制
xml 布局中设置：

```
 android:drawablePadding=""
```

代码中设置：

```
//注意默认的是 px, UI 图上的 dp 单位需要转换
view.setCompoundDrawablePadding();
```


#### CompoundDrawable 的大小控制

CompoundDrawable 的大小在布局上没有相关的属性，只能通过代码设置：

```
drawable.setBounds(0, 0,  width, height);

```

# 最后

一个非常基础，但是很有用的知识点，分享给你们，大家有什么好用的小技巧，热烈欢迎评论分享出来呐！

好啦，我们下篇文章见。喜欢不要忘记给作者点个赞，或者分享给你的小伙伴哦！


---

刚刚开通了个人微信公众号，最新的博客，好玩的事情，都会在上面分享，欢迎关注，让我们一起学习和成长。

<div  align="center">    

![微信公众号](http://oriwplcze.bkt.clouddn.com/qrcode_for_gh_e8f891ce77fb_258.jpg)

</div>
