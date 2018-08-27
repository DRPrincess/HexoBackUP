---
title: 真容BaseLib
tags:
---


# CommWebActivity

##输入

```

private static final long serialVersionUID = -88490470358977045L;
public String url;
public String title;
public String titleRight;

```

## 输出：

- 优惠券页面，就拦截跳转
```
//如果是优惠券页面，就拦截跳转
     if(url.contains("coupon/new/1")){
         Navigation.toUrl(context, Url.getGainCoupon());
         finish();
     }
```

-  返回键
如果已经是最后一个页面，点击的时候要跳转到首页面

- 解析网络给的URL，做相应跳转

- url_.contains("app=waplogin")

  登录页面

- url_.contains("ocert/login")

  在线检测的登录页面
- url_.contains("showProgress")
