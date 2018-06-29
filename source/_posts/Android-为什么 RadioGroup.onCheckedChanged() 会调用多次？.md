---
title: Android-为什么 RadioGroup.onCheckedChanged() 会调用多次？

date: 2018-05-27 11:52:00

---

可能你自己都不知道你踩过这个坑。
<!-- more -->


# 有这么一个坑

同学，你有没有遇到 `RadioGroup.onCheckedChanged()` 莫名其妙调用多次的情况？

你是怎么解决的？


使用`setChecked()` 替代 `setChecked()` 是不是？

是的，这是一个有效的解决方法，那你知道为什么吗？


# 先自己挖坑做个实验

事实上，这个坑的隐藏属性还是不低的，因为不出问题掉下去摔一下，你可能真的不知道路不平，哈哈哈。

作为一个摔出经验的人，我决定写个Demo，带你们去看下这个坑长啥样。

首先构建一个如下的布局：

![](http://oriwplcze.bkt.clouddn.com/1995f827c402788af7566e7b0c7b9514.png)


代码中，给 RadioGroup 设置监听事件，事件中打印日志。

```
rgSex.setOnCheckedChangeListener(new RadioGroup.OnCheckedChangeListener() {
           @Override
           public void onCheckedChanged(RadioGroup group, int checkedId) {

                Log.w(TAG, "RadioGroup onCheckedChanged: "+checkedId );
           }
       });

```

另外添加的两个按钮，分别演示 `RadioGroup.check()` 和 `RadioButton.setCheck()` 这两种切换方式。

```
findViewById(R.id.button1).setOnClickListener(new View.OnClickListener() {
         @Override
         public void onClick(View v) {

             Log.d(TAG, "onClick: check()");
             rgSex.check(R.id.rbtnFemale);
         }
     });


findViewById(R.id.button2).setOnClickListener(new View.OnClickListener() {
   @Override
   public void onClick(View v) {
       Log.d(TAG, "onClick: setChecked()");
       rbtnFemale.setChecked(true);
   }
});

```

先默认选中“男”，然后使用两个按钮，从“男”切换到“女”选中。

下面是打印的结果：

```
//check()切换状态
onClick: check()
RadioGroup onCheckedChanged: 2131165276  
RadioGroup onCheckedChanged: 2131165275
RadioGroup onCheckedChanged: 2131165275  


//setChecked 切换状态
onClick: setChecked()
RadioGroup onCheckedChanged: 2131165275  

//手动点击 radioButton 切换状态
RadioGroup onCheckedChanged: 2131165275  

```


现在，如果遇到这个坑的猿们，有没有一股熟悉的气息铺面而来？

日志说明三个事实：

1. `RadioGroup.check()`的确会导致`RadioGroup.onCheckedChanged()`调用多次。


2. 代码中，使用 `RadioButton.setChecked()` 方法替代 `RadioGroup.check()`是个解决方法。

3. 这个坑的隐藏属性的确很高，注意到虽然调用了多次，最后的一次调用传递参数是正确的。


那存在一个问题：

> 既然最后一次是对的，调用多次的问题是否可以忽略？


如果你的回答是"可以忽略"，那我肯定你还没在上面摔过，至少是没摔疼。

举个小例子：

假设在 `RadioGroup.onCheckedChanged()` 中启动了页面 A 。那么多次调用会导致页面 A 会重复创建。

假设监听事件中，有网络请求逻辑，多次调用，会给服务端发多次同样请求。

此处省略很多假设....

所以，以防万一，做好细节处理，能改就改。


# 为啥会有这个坑？看源码呐。

坑是填平了，但是脑海中仍有几个疑问：

> 这个问题为什么会出现？是 Android 系统设计的 bug 吗？

> 这么明显的问题，Google的程序员会不知道吗？

> 如果不是，那为什么这么设计呢？

这样的问题，答案就隐藏在源码之中。


于是，从上面的测试结果看，应该从 `RadioGroup.check()` 这个方法切入，寻找事情的真相。

```
public void check(@IdRes int id) {
      // don't even bother
      if (id != -1 && (id == mCheckedId)) {
          return;
      }

      //如果上一个有选中的，先把上一个选中的给取消选中
      if (mCheckedId != -1) {
          setCheckedStateForView(mCheckedId, false);
      }

      //再将现在当前点击给选中
      if (id != -1) {
          setCheckedStateForView(id, true);
      }

      //调用自己的监听方法
      setCheckedId(id);
  }

```


现在集中到  `setCheckedStateForView`  和 `setCheckedId`  方法。方法中很明确地看到 `onCheckedChanged()` 方法的调用,因此，可以认为，只要调用了该方法，就触发了一次监听方法。

```
private void setCheckedId(@IdRes int id) {
     mCheckedId = id;
     if (mOnCheckedChangeListener != null) {
         mOnCheckedChangeListener.onCheckedChanged(this, mCheckedId);
     }
     final AutofillManager afm = mContext.getSystemService(AutofillManager.class);
     if (afm != null) {
         afm.notifyValueChanged(this);
     }
 }

```

 `setCheckedStateForView()` 方法是用来切换 RadioButton 的选中状态的，里面没有看到直接的监听方法的调用，但有调用 radioButton 的 `setChecked ()`方法 。


```
private void setCheckedStateForView(int viewId, boolean checked) {
      View checkedView = findViewById(viewId);
      if (checkedView != null && checkedView instanceof RadioButton) {
          ((RadioButton) checkedView).setChecked(checked);
      }
  }
```


于是继续跳，会跳到 RadioButton 的父类 CompoundButton 的
` setChecked()`的方法中。



```
 @Override
   public void setChecked(boolean checked) {
       if (mChecked != checked) {
           mCheckedFromResource = false;
           mChecked = checked;
           refreshDrawableState();
           notifyViewAccessibilityStateChangedIfNeeded(
                   AccessibilityEvent.CONTENT_CHANGE_TYPE_UNDEFINED);

           // Avoid infinite recursions if setChecked() is called from a listener
           if (mBroadcasting) {
               return;
           }

           mBroadcasting = true;

           //这里触发自己的监听
           if (mOnCheckedChangeListener != null) {
               mOnCheckedChangeListener.onCheckedChanged(this, mChecked);
           }

           //这里触发 RadioGroup 的 onCheckedChanged 监听
           if (mOnCheckedChangeWidgetListener != null) {
               mOnCheckedChangeWidgetListener.onCheckedChanged(this, mChecked);
           }
           final AutofillManager afm = mContext.getSystemService(AutofillManager.class);
           if (afm != null) {
               afm.notifyValueChanged(this);
           }

           mBroadcasting = false;
       }
   }

```

 方法有两个监听事件的触发，`mOnCheckedChangeListener.onCheckedChanged()` 和 `mOnCheckedChangeWidgetListener.onCheckedChanged（）`,前者是 RadioBuuton 自己的，后者我们猜测是父布局的，但是不确定，所以继续找。

 CompoundButton 中的 `mOnCheckedChangeWidgetListener` 是通过以下方法设置。

```
void setOnCheckedChangeWidgetListener(OnCheckedChangeListener listener) {
    mOnCheckedChangeWidgetListener = listener;
}

```


在 RadioGroup 中内部接口类的 `onChildViewAdded() `找到了该方法的调用，这个内部类接口是 ViewGroup 初始化阶段 `addView()` 时设置，由此我们可以知道是在向 RadioGroup 中添加 RadioButton 的时候设置的 `mOnCheckedChangeWidgetListener`。
```
private class PassThroughHierarchyChangeListener implements
        ViewGroup.OnHierarchyChangeListener {
    private ViewGroup.OnHierarchyChangeListener mOnHierarchyChangeListener;

    /**
     * {@inheritDoc}
     */
    @Override
    public void onChildViewAdded(View parent, View child) {
        if (parent == RadioGroup.this && child instanceof RadioButton) {
            int id = child.getId();
            // generates an id if it's missing
            if (id == View.NO_ID) {
                id = View.generateViewId();
                child.setId(id);
            }


            //这里给 RadioButton 设置了监听
            ((RadioButton) child).setOnCheckedChangeWidgetListener(
                    mChildOnCheckedChangeListener);
        }

        if (mOnHierarchyChangeListener != null) {
            mOnHierarchyChangeListener.onChildViewAdded(parent, child);
        }
    }
  }

```

接下来，找到 `mChildOnCheckedChangeListener` 是在 RadioGroup 初始化方法中被赋予了值，是 `CheckedStateTracker`类的实例化。

```
private void init() {

    //初始化监听
    mChildOnCheckedChangeListener = new CheckedStateTracker();
    mPassThroughListener = new PassThroughHierarchyChangeListener();
    super.setOnHierarchyChangeListener(mPassThroughListener);
}
```


CheckedStateTracker 是 `CompoundButton.OnCheckedChangeListener` 的子类，实现了 `onCheckedChanged()`
的抽象方法。这就是我们上面在方法找 `mOnCheckedChangeWidgetListener.onCheckedChanged(this, mChecked)` 这个方法的内容了。

```
private class CheckedStateTracker implements CompoundButton.OnCheckedChangeListener {
    @Override
    public void onCheckedChanged(CompoundButton buttonView, boolean isChecked) {
        // prevents from infinite recursion
        if (mProtectFromCheckedChange) {
            return;
        }

        mProtectFromCheckedChange = true;

        //这里通知子控件 RadioButton 切换选中状态
        if (mCheckedId != -1) {
            setCheckedStateForView(mCheckedId, false);
        }
        mProtectFromCheckedChange = false;

        int id = buttonView.getId();

        //调用了监听方法
        setCheckedId(id);
    }
}

```

方法内部，发现调用 `setCheckedId(id)`, 触发了监听事件，找到这里，一切真相大白啦。

因为`check()` 方法中调用了两个方法 `setCheckedStateForView`  和`setCheckedId`, 其中`setCheckedId` 会直接触发 `onCheckedChanged()`方法，而`setCheckedStateForView` 中的逻辑会最终调用 `setCheckedId`，所以 `onCheckedChanged()` 会被调用多次。



# 整体分析

以上连续跳来跳去的源码探寻结束，让我们简单梳理一下思路。

![](http://oriwplcze.bkt.clouddn.com/dd1641408ffa2ace4d98f8cc84e52473.png)

得出一个小结论：   
check() 方法中调用了两个方法 `setCheckedStateForView`  和`setCheckedId`,两者都会触发一次 `onCheckedChanged()`。

所以再次看下 check() 方法的逻辑：


```
public void check(@IdRes int id) {
      // don't even bother
      if (id != -1 && (id == mCheckedId)) {
          return;
      }

      //如果上一个有选中的，先把上一个选中的给取消选中
      if (mCheckedId != -1) {
          setCheckedStateForView(mCheckedId, false);
      }

      //再将现在当前点击给选中
      if (id != -1) {
          setCheckedStateForView(id, true);
      }

      //调用自己的监听方法
      setCheckedId(id);
  }

```


虽然有三次调用，但是是否调用的逻辑判断由 `mCheckedId` 和 `id `来决定，所以不同的情况，`onCheckedChanged()`调用次数是不一样的。

**触发3次：**

条件：mCheckedId != -1;id != -1; mCheckedId != id;

对应情况：在选中一个值的的情况下，选中另外一个值。

例如：“男” 选中时，check(“女”的ID)。


**触发2次：**

条件1：mCheckedId = -1;id != -1 ;mCheckedId != id;

对应情况：在未选中的情况下，选中其中一个值。

例如：“男”“女” 未选中时，选中 “男”。


条件2：mCheckedId != -1 ; id == -1 ; mCheckedId != id;

对应情况：在选中一个值的情况下，使用 check(-1)。

例如：“男”选中时，调用 clearCheck()或者 check(-1)。


**触发1次：**

条件：mCheckedId = -1; id == -1; mCheckedId == id;

对应情况：在未选中的情况下，使用 check(-1)。

例如：“男”“女” 未选中时，调用 clearCheck()或者 check(-1)。

**触发0次：**

条件 mCheckedId == id; id != -1 ; (防止连续触发)

对应情况：同一个参数ID 的连续调用。

例如：“男”选中时，调用check(“男”的ID)。


其实 `check()`方法中的逻辑步骤是：

1. 如果上一个有选中的，先把上一个选中的给取消选中。
2. 再将当前点击给选中。
3. 调用自己的监听方法。

1，2 的步骤是合理的，我原先有一个疑问:
>为什么有第 3 个步骤?看起来完全没必要。

后来做完上面的分析时，考虑 **为什么会有触发1次的需求时** 的情况，突然明白了点什么。

**是我们错误理解了 `onCheckedChanged()` 这个监听事件！**

> RadioGroup 中 onCheckedChanged 并不是只有选中的 RadioButton 会触发，而是 RadioGroup 内选中状态值变化时都会调用.         

> RadioButton中 并不是只有选中会触发，而是选中状态值变化时都会调用.

所以 `onCheckedChanged()` 多次调用的核心点，不是调用 `check()` 和 `setChecked()` 的选择问题，而是这两个监听的理解问题。

在 `onCheckedChanged()` 中处理逻辑时要非常明确这一点，我现在已经开始担心我之前的写的那些代码了，真是掩面而泣。


另外，我回头看了源码中 `onCheckedChanged`的注释，其实说的很清楚。那么我是为什么要转个一大圈才长记性呢？真的是值的深思一分钟的问题啊，以后要仔细看注释，不要错误理解了方法的作用以及参数的意义。

```


    /**
     * <p>Interface definition for a callback to be invoked when the checked
     * radio button changed in this group.</p>
     */
    public interface OnCheckedChangeListener {
        /**
         * <p>Called when the checked radio button has changed. When the
         * selection is cleared, checkedId is -1.</p>
         *
         * @param group the group in which the checked radio button has changed
         * @param checkedId the unique identifier of the newly checked radio button
         */
        public void onCheckedChanged(RadioGroup group, @IdRes int checkedId);
    }

```



# 结语

类似这样的细节小问题，技术上没啥要求，遇到完全看缘分，走着走着，就踩到了点什么。若你一直在开发这条路上走着，十有八九会遇到的。

愿我们小问题多踩坑，大事情少出错。

与君共勉，下篇博客见。


---

刚刚开通了个人微信公众号，最新的博客，好玩的事情，都会在上面分享，欢迎关注，让我们一起学习和成长。

<div  align="center">    

![微信公众号](http://oriwplcze.bkt.clouddn.com/qrcode_for_gh_e8f891ce77fb_258.jpg)

</div>
