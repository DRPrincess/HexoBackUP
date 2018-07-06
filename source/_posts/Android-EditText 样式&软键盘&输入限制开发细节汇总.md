---
title: Android-EditText 样式&软键盘&输入限制开发细节汇总
date: 2018-04-17 18:35:00
tags:
---
实战项目开发，才是最考验细节的，今天就拿 EditText 说吧。
<!--more-->

# 开发小日常

**测试：能不能别一打开页面，就弹出输入键盘？**      
博主：好的,我看下，这点疏忽了。

**测试：能不能别一打开页面，就显示光标?这个可以不用替用户决定顺序**。   
博主：好的

**测试：你看这个光标是不是有点粗？能调色吗？**    
博主：嗯，可以。

**测试：为啥退出这个页面后，这个软键盘还在的？**   
博主：真的吗？来我看看。

**测试：这个可以让它只输入数字吗？**    
**测试：这个可以让它只能输入字母吗？**    
**测试：这个可以让它只能输入字母和数字吗？**    
**测试：可以让用户只能输入两位小数吗？**   
**测试：这个是手机号，可以限制最多 11 位吗？**  
博主：.....


以上是一个真实的故事，感受最深刻的就是，就一个编辑框 ，细节真的不少，撇去导致这个问题的其他因素，今天的博客是一篇以 EditText 为主题 ，记录修改能满足以上各种需求的干货文章。


# EditText 相关知识点梳理

首先做个大概的了解，EditText 作为一个编辑框，UI考虑上可分为以下几点：

- 编辑框本身的样式。例如背景和光标，字体颜色大小等。
- 软键盘。例如弹出或关闭，显示搜索或确定。
- 输入限制。例如限制输入字母或者数字。


# EditText 的样式

这个在写 xml 布局文件的阶段会涉及到，Android 系统原生的编辑框，丑到开发者自己都看不下去，虽然在 Material Design 风格出来之后好了很多，但是实际的项目开发中，Android 要和 iOS 保持统一，所以还是要自定义。

一般情况下是这种

![](http://oriwplcze.bkt.clouddn.com/2a0ba794301e3a0f9508f150ebe39a68.png)

和这种

![](http://oriwplcze.bkt.clouddn.com/27ae9ec6648ac0d7fedafc9873abbaae.png)

以及等等。





设置字体颜色大小基本使用，我就不拿出来侮辱大家智商了，来点实际的：

**EditText 背景**
通过设置 `android:background` 完成。

不需要背景，设置`android:background=“@null”`
需要特殊的背景话，就可以通过这个设置啦。


**EditText 内置的小图标**
例如上图左边的放大镜小图标那种，可以通过 `android:drawableLeft` 设置小图标, `android:drawablePadding`设置小图标和输入字体的间距。

需要注意的是通过这种方式添加的小图标，大小是没法明确设置的，如果有额外要求，还是专门使用一个控件吧。   

**EditText 的光标**

EditText 的光标颜色默认是 `colorAccent` 的颜色， colorAccent不仅仅代表到光标的颜色，还有项目中很多原生控件激活选择时的颜色，所以 UI 上如果对编辑框光标颜色有需求，无法直接修改  `colorAccent` ，或者认为光标太粗了，想改细一点，这类情况都需要设置光标的样式。

例如设置光标为蓝色，宽度为 1 dp,需要先写一个光标样式文件：

```
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="rectangle">
    <size android:width="1dp" />
    <solid android:color="#217aff" />
</shape>

```

然后通过 `android:textCursorDrawable` 引入这个光标样式。
```
android:textCursorDrawable="@drawable/edit_cursor_color"

```
另外：

`android:cursorVisible` 改变光标的可见性   
`setSelection()`可以改变光标的位置。   



# 关于软键盘







编辑框和软键盘的密不可分，如果不做任何设置，页面上EditText 会默认抢占焦点，弹出软键盘。有的页面是这个需求，有的则不是，按需选择。

**EditText 不显示光标，只有在点击时才弹出软键盘**

这个在 xml 布局中给 EditText 的父布局设置触摸获取焦点可以解决,设置属性是 `android:focusableInTouchMode="true"`。


示例：
```
<LinearLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:focusableInTouchMode="true" >
    <EditText
        android:id="@+id/editText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content">
</LinearLayout>

```

经验证，这个属性，并非只有在设置直接父级布局上有效，在间接父级布局上也可以。但是设置在 ScrollView 父级布局上无效。

## 软键盘在页面退出时，不自动关闭

这个情况是，最近发现的一个很搞笑的现象。页面跳转路径是 A->B ，B中有 EditText，在弹出软键盘后，不通过手机自带的返回键，而是按 B 页面中的返回按钮回到 A 时，发现软键盘并未随着B页面自动关闭。

原因是 A 页面设置了 `android:windowSoftInputMode` 设置了 `stateHidden `属性。
```
<activity
            android:name="xxxxxxx"
            android:windowSoftInputMode="adjustPan|stateHidden"/>

```


解决方法是修改A页面的 `windowSoftInputMode` 设置,一下是一个良心网友[@zhangziki](https://blog.csdn.net/zhangziki/article/details/50969743)自测出来的表格，大家自己设置。


|windowSoftInputMode|	键盘是否自动收回|
|-----|---|
|默认不指定|	√|
|stateUnspecified	|√|
|stateAlwaysVisible	|√
|stateUnchanged	|×|
|stateHidden|	×|
|stateAlwaysHidden	|×
|stateVisible	|×|
|stateHidden	|×|
|stateHidden	|×|





**设置软键盘的 enter 键**

默认的键盘的 enter 是换行键，如下：

![](http://oriwplcze.bkt.clouddn.com/c4b90cf405894f77569cdd4cfd15ad35.png)

但是有的时候，产品需求会要求，键盘的 enter 键改为其他文字，最常见的是搜索，点击搜索后出发代码中设置的搜索逻辑。

![](http://oriwplcze.bkt.clouddn.com/8249bebe4b0714390cca8ced2d04b7cf.png)


通过在设置 EditText 的 `imeOptions` 可以改变 enter 键的文案,要着重说明的是，因为将默认的换行键换为了搜索键，所以等于放弃了换行功能，所以需要通知设置 `android:singleLine="true"` 才能使    `android:imeOptions="actionSearch"
` 生效。
```
<EditText
          android:id="@+id/editText"
          android:layout_width="match_parent"
          android:layout_height="wrap_content"
          android:singleLine="true"
          android:imeOptions="actionSearch"/>
```

代码中可以通过这个方法设置监听：

```
mEditText.setOnEditorActionListener(new TextView.OnEditorActionListener() {
            @Override
            public boolean onEditorAction(TextView v, int actionId, KeyEvent event) {
                switch (actionId) {
                    case EditorInfo.IME_ACTION_SEARCH://按下搜索键
                        break;
                    default:
                        break;
                }
                return false;
            }
        });

```

除了设置搜索按钮之外，还有很多选项 `actionGo `,`actionSend `,`actionNext`等，具体的效果，可以自己试试看，道理都是一样的。


# EditText 输入限制

**inputType 设置输入类型，例如只能输入数字,或者输入密码**

inputType 可以通过 xml 设置`android:inputType`属性和代码中 `setInputType()`
方法设置。
API提供了很多种现成的格式供我们选择， 而且这些格式可以自由组合。
```
android:inputType="numberPassword|number"

```

以上设置支持密文输入，并且限制只允许输入数字

**digits 设置具体的支持字符**

这个属性也支持通过代码和布局属性设置，和 inputType 的不同之处在于，可以说这个属性可以自定义具体的支持字符。

例如设置 EditText 只能输入字符和数字：
```
android:digits="0123456789abcdefghijklmnopqrstuvwxyz"

```

**设置 TextWather 监测输入内容**

 TextWather 监听EditText的字符变化， 拿到用户的输入内容，做我们想要的处理。这个处理可以分为两类：
 1. 作为一个输入限制使用。实现 inputType 和 digits 两个属性无法实现的特殊的输入限制需求，例如限制只输入小数点后两位 。
 2. 作为一个事件监听使用。满足某种情况，触发相应的逻辑，例如当输入 16 位身份证号码时自动调用接口验证是否真实有效。


这里给出一个限制只输入小数点后两位的例子。
```

//限制的位数
int digits = 2;

mEditText.addTextChangedListener(new TextWatcher() {
           @Override
           public void beforeTextChanged(CharSequence s, int start, int count, int after) {

           }

           @Override
           public void onTextChanged(CharSequence s, int start, int before, int count) {
             //删除“.”后面超过2位后的数据
                    if (s.toString().contains(".")) {
                        if (s.length() - 1 - s.toString().indexOf(".") > digits) {
                            s = s.toString().subSequence(0,
                                    s.toString().indexOf(".") + digits+1);
                            editText.setText(s);
                            editText.setSelection(s.length()); //光标移到最后
                        }
                    }
                    //如果"."在起始位置,则起始位置自动补0
                    if (s.toString().trim().substring(0).equals(".")) {
                        s = "0" + s;
                        editText.setText(s);
                        editText.setSelection(2);
                    }

                    //如果起始位置为0,且第二位跟的不是".",则无法后续输入
                    if (s.toString().startsWith("0")
                            && s.toString().trim().length() > 1) {
                        if (!s.toString().substring(1, 2).equals(".")) {
                            editText.setText(s.subSequence(0, 1));
                            editText.setSelection(1);
                            return;
                        }
                    }
           }

           @Override
           public void afterTextChanged(Editable s) {

           }
       });

```


**设置 InputFilter 过滤器**

除了 TextWather，自定义 InputFilter 过滤器也能达到我们特殊的过滤需求。例如上面的限制两位小数点后两位。

设置 InputFilter 过滤器需要两步：
1. 自定义 InputFilter, 继承一个合适的 KeyListener， 重写filter()方法，这里面编写你想要的过滤限制。
2. EditText 通过  `setFilters` 设置过滤器。

在我看来，如果实现输入限制这类功能，InputFilter 的实现方式比 TextWather 显的更加优雅一点，而且从方法名称上看也是理所当然的。TextWather 更适合去做监听类使用。

下面给出限制两位小数点后两位的 InputFilter 实现版本。

```
public class MoneyValueFilter extends DigitsKeyListener {

    private static final String TAG = "MoneyValueFilter";
    public MoneyValueFilter() {
        super(false, true);
    }

    private int digits = 2;

    public MoneyValueFilter setDigits(int d) {
        digits = d;
        return this;
    }

    @Override
    public CharSequence filter(CharSequence source, int start, int end,
                               Spanned dest, int dstart, int dend) {
        CharSequence out = super.filter(source, start, end, dest, dstart, dend);


        // if changed, replace the source
        if (out != null) {
            source = out;
            start = 0;
            end = out.length();
        }

        int len = end - start;

        // if deleting, source is empty
        // and deleting can't break anything
        if (len == 0) {
            return source;
        }

        //以点开始的时候，自动在前面添加0
        if(source.toString().equals(".") && dstart == 0){
            return "0.";
        }
        //如果起始位置为0,且第二位跟的不是".",则无法后续输入
        if(!source.toString().equals(".") && dest.toString().equals("0")){
            return "";
        }

        int dlen = dest.length();

        // Find the position of the decimal .
        for (int i = 0; i < dstart; i++) {
            if (dest.charAt(i) == '.') {
                // being here means, that a number has
                // been inserted after the dot
                // check if the amount of digits is right
                return (dlen-(i+1) + len > digits) ?
                    "" :
                    new SpannableStringBuilder(source, start, end);
            }
        }

        for (int i = start; i < end; ++i) {
            if (source.charAt(i) == '.') {
                // being here means, dot has been inserted
                // check if the amount of digits is right
                if ((dlen-dend) + (end-(i + 1)) > digits)
                    return "";
                else
                    break;  // return new SpannableStringBuilder(source, start, end);
            }
        }



        // if the dot is after the inserted part,
        // nothing can break
        return new SpannableStringBuilder(source, start, end);
    }
}

```

```

mEditText.setFilters(new InputFilter[]{new MoneyValueFilter().setDigits(3)});

```



# 最后

虽然都是一些小细节，也要重视起来，因为细节决定成败呐，希望自己以后要多注意，努力降低 bug 率，Fighting!


另外，关于限制两位小树输入的需求的代码已上传我的 github，项目地址为 [DR_MoneyEditTextDemo](https://github.com/DRPrincess/DR_MoneyEditTextDemo),欢迎 Star,热烈欢迎 Follow 。



---

刚刚开通了个人微信公众号，最新的博客，好玩的事情，都会在上面分享，欢迎关注，让我们一起学习和成长。

<div  align="center">    

![微信公众号](http://oriwplcze.bkt.clouddn.com/qrcode_for_gh_e8f891ce77fb_258.jpg)

</div>
