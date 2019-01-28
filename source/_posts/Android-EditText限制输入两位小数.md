---
title: Android-EditText两种方法限制输入两位小数
date: 2017-10-09 18:02:44
tags:
---



为什么有这篇博客的产生，那是因为限制输入位数这个需求简直说是无处不在。

<!-- more -->

# <font color= "#008000"> 为什么有这个需求</font>


说实话，这个需求简直可以说无处不在了，因为，只要有输入金额的需求，客户端限制输入位数几乎是肯定的。

# <font color= "#008000"> 功能点分析 </font>


1.首位输入`.`的时候,补全为`0.`  

2.删除“.”后面超过2位后的数据   

3.如果起始位置为0,且第二位跟的不是".",则无法后续输入



# <font color= "#008000"> 代码实现之 TextWatcher 方法 </font>


```java

/**
 *
 *描述   ：金额输入字体监听类，限制小数点后输入位数
 *
 * 默认限制小数点2位
 * 默认第一位输入小数点时，转换为0.
 * 如果起始位置为0,且第二位跟的不是".",则无法后续输入
 *
 *作者   ：Created by DuanRui on 2017/9/28.
 */
public class MoneyTextWatcher implements TextWatcher {
    private EditText editText;
    private int digits = 2;

    public MoneyTextWatcher(EditText et) {
        editText = et;
    }
    public MoneyTextWatcher setDigits(int d) {
        digits = d;
        return this;
    }


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
}


```


使用方法：

``` java

//默认两位小数
mEditText.addTextChangedListener(new MoneyTextWatcher(mEditText1));

//手动设置其他位数，例如3
mEditText.addTextChangedListener(new MoneyTextWatcher(mEditText1).setDigits(3);

```


# <font color= "#008000"> 代码实现之 setFilter 方法 </font>


```java

/**
 *
 *描述   ：金额输入过滤器，限制小数点后输入位数
 *
 * 默认限制小数点2位
 * 默认第一位输入小数点时，转换为0.
 * 如果起始位置为0,且第二位跟的不是".",则无法后续输入
 *
 *作者   ：Created by DuanRui on 2017/9/28.
 */

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



使用方法：

``` java

//默认两位小数
mEditText.setFilters(new InputFilter[]{new MoneyValueFilter()});

//手动设置其他位数，例如3
mEditText.setFilters(new InputFilter[]{new MoneyValueFilter().setDigits(3)});
```   


---


上述代码已上传我的 github，项目地址为 [DR_MoneyEditTextDemo](https://github.com/DRPrincess/DR_MoneyEditTextDemo),欢迎 Star,热烈欢迎 Follow 。

---

# <font color= "#008000"> 最后 </font>

刚刚开通了个人微信公众号，最新的博客，好玩的事情，都会在上面分享，欢迎关注 (*^o^*)。

<div  align="center">    

![微信公众号](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/836a36d6a91d859428783f8ea2ce85d7.png)

</div>
