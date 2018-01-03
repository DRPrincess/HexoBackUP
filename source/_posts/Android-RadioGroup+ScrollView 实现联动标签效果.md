---
title: Android-RadioGroup+ScrollView 实现联动标签效果
date: 2017-12-29 23:15:00
tags:
---

这个布局实现方案，比较适合那种巨长无比的滚动查看页面。

<!--more-->



# 开篇

新的项目中有一个很长的资料提交和资料查看页面，为了方便查看，上方加了 RadioGroup 分类标签，可以快速滑动的相应位置。
实现的效果和下面差不多，其实，蘑菇街的商品详情也是这样实现的。


![](http://oriwplcze.bkt.clouddn.com/ezgif.com-video-to-gif.gif)


# 实现思路
 1. RadioGroup + ScrollView 控件搭配实现

 2. RadioGroup 的 OnCheckedChangeListener 监听事件中拿到 RadioButton 对应的内容 Y 坐标，通过 scrollTo(0，Y)或者 smoothScrollTo(0，Y)  控制 ScrollView 的滑动。
 3. ScrollView 的 onScrollChanged 方法中通过 Y 值判断范围，来确定选中哪个 RadioButton 。





# 始料未及的小问题 & 解决方法

 上面的步骤是不是看起来是不是超级简单,然而事实证明，too young to naive !

 1.OnCheckedChangeListener 和 onScrollChanged 会互相触发呐呐呐，下滑时还好，上滑的时候，标签直接会连续选中到第一个。

 2.被上滑时不受控制的 ScrollView 折磨得几乎要疯的我，发现是 RadioGroup 的锅，这货的 onCheckedChanged 一次触发会调用多次。



#### OnCheckedChangeListener 和 onScrollChanged 会互相触发

实话说，这是非常正常的表现，毕竟是需要相互联动的两个控件，就是联动的时机有误，需要我们来手动控制一下。  

现状：  
点击 RadioGroup ，ScrollView 滑动到固定位置，上滑不会有问题，下滑时由于判断条件不同，ScrollView 滑动到固定位置时会触发 RadioGroup 的监听事件。

需求：
RadioGroup 触发的时候，，ScrollView 中对 RadioGroup 就不要触发。
ScrollView 联动触发 RadioGroup 的时候，RadioGroup 对 ScrollView 的联动不要触发。

简单来说，就是双向同时联动改成双向不同时联动。

修改方法：

1.添加两个变量：

```
boolean isScrollFlag = false ;
boolean isRadioFlag = false;

```

2.RadioGroup 的 onCheckedChanged 中的判断：

```
//拦截 scrollView 中的联动触发
if(isScrollFlag){
    isScrollFlag = isRadioFlag = false;
    return;
}

```

3.ScrollView 的 onScrollChanged 中的判断：

```
//拦截 radioGroup 选中的联动触发
if(isRadioFlag ){
    Log.d(TAG, "onScrollChanged: 拦截");
    isRadioFlag = isScrollFlag = false;

    return;
}

```




#### RadioGroup 调用check()方法 onCheckedChanged 一次触发多次调用的问题。   
 多次触发是 check() 中多次调用了 setCheckedId() 方法，而 setCheckedId() 里面又有多次调用 onCheckedChanged 。以上这句话的具体细节请自行查看相关源码。这里放上我的解决方法：

 在 RadioGroup 的 onCheckedChanged 方法里加入如下代码：

```
mRadioGroup.setOnCheckedChangeListener(new RadioGroup.OnCheckedChangeListener() {
     @Override
     public void onCheckedChanged(RadioGroup group, int checkedId) {

         //防止onCheckedChanged 被调用多次
         View viewById = mRadioGroup.findViewById(checkedId);
         if (!viewById.isPressed()){
             Log.w(TAG, "onCheckedChanged: 阻止");
             return;
         }

         //...
       }
});

```

# 最后

本来想按照实现步骤一步步附上上对应的代码的，后来想想也不是很有必要，还是思路说清楚比较重要，最后附上完整代码链接。

[完整代码在这里](https://github.com/DRPrincess/DR_RadioGroupScrollViewDemo)

发现一个待改进的问题，就是 scrollView 调用 smoothScrollTo(0，Y) 方法其实也是会多次触发 onScrollChanged()。虽然最后实现了效果，但是总是有点不对的，这个问题后面会再优化。

技术粗浅，如果有不对的地方，还请留言告知。
