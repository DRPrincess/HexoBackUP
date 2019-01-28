

AndroidStudio  安装启动App的 adb 过程：

```

$ adb push /Users/duanrui/AndroidStudioProjects/NewRetail/app/build/outputs/apk/padDevelop/debug/app-pad-develop-debug.apk /data/local/tmp/com.youzan.retailhd
$ adb shell pm install -t -r "/data/local/tmp/com.youzan.retailhd"



$ adb shell am start -n "com.youzan.retailhd/com.youzan.retail.launch.PrepareActivity" -a android.intent.action.MAIN -c android.intent.category.LAUNCHER


```


#  有赞零售缓存地址

/sdcard/Android/data/com.youzan.retailhd/cache/crash

Android优惠明细代码优化


Android  端的优惠明细实现是写死的，现在要做成列表方式，根据接口动态显示


改变营销活动的增加，就需要修改增加优惠明细项的问题


优惠明细通用，根据接口动态显示，代码中没有每个营销活动自成一体的代码


改变营销活动的增加，就需要修改增加优惠明细项的问题，只需要特别处理和微调就好，减少开发工作量
