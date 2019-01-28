
---
title: Android-解决Webview对 `<input type="file"> ` 点击无响应
date: 2017-08-31 18:03:44
tags:
---

上个周，项目中有需要接入一个 H5 页面，H5 中有上传图片的功能，接入测试的时候，发现 iOS 端正常，但是所有的 Android 手机在点击上传图片的按钮时，都毫无反应 。当时我的表情是这样的 (˶‾᷄ ⁻̫ ‾᷅˵) 。  

<!-- more -->

![封面](http://upload-images.jianshu.io/upload_images/4373698-06589b73d2df6c80.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



# <font color="#008000">发现一个 WebView 的坑：</font>

上个周，项目中有需要接入一个 H5 页面，H5 中有上传图片的功能，接入测试的时候，发现 iOS 端正常，但是所有的 Android 手机在点击上传图片的按钮时，都毫无反应 。当时我的表情是这样的 (˶‾᷄ ⁻̫ ‾᷅˵) 。




问题原因：

原因是 H5 访问本地文件的时候，使用的`<input type="file">` ,WebView 出于安全性的考虑，限制了以上操作。

解决方法：

重写 WebviewChromeClient 中的 openFileChooser() 和 onShowFileChooser()方法响应`<input type="file">`，然后使用原生代码来实现调用本地相册和拍照的功能，最后在 onActiivtyResult 把选择的图片 URI 回传给 WebviewChromeClient。

# <font color="#008000">逻辑分析</font>

首先明确下，我需要做的工作有：

1.弹出对话框选择相机或相册
2.调用系统相册的实现代码  
3.调用系统相机拍照的实现代码
4.需要兼容 6.0 的动态权限问题和 7.0 的文件管理问题。
5.相机拍照后的图片上传后要进行删除，以免占用手机存储空间

列下来觉得还是蛮多的。

# <font color="#008000">博主实现完 Demo，并扔给你一段核心代码</font>

 WebviewChromeClient 中重写方法响应 `<input type="file">` 。

 4.1以上系统，使用 openFileChooser() ，该方法5.0已经被废弃

 5.0以上系统，使用 onShowFileChooser()

 ```java

 mWebView.setWebChromeClient(new WebChromeClient() {

            //For Android  >= 5.0
            @Override
            public boolean onShowFileChooser(WebView webView, ValueCallback<Uri[]> filePathCallback, FileChooserParams fileChooserParams) {

                uploadMessageAboveL = filePathCallback;
                //调用系统相机或者相册
                uploadPicture();
                return true;
            }


            //For Android  >= 4.1
            public void openFileChooser(ValueCallback<Uri> valueCallback, String acceptType, String capture)

                uploadMessage = valueCallback;
                //调用系统相机或者相册
                uploadPicture();
            }

        });

 ```

 调用相机和相册选择图片后，在 onActivityResult() 中将图片的 Uri 通过 uploadMessage，5.0以上用 uploadMessageAboveL，使用 onReceiveValue 回传过去。

 这里需要注意的是，当取消拍照或者未选择照片的时候，uploadMessage 或者 uploadMessageAboveL 要返回 null.因为 valueCallback 持有的是 WebView，在 onReceiveValue 没有回传值的情况下，WebView 无法进行下一步操作，会导致取消选择文件后，点击 `<input type = 'file'>`,不会再响应。


 ```JavaScript

 @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);

        if (requestCode == REQUEST_CODE_ALBUM || requestCode == REQUEST_CODE_CAMERA) {

            if (uploadMessage == null && uploadMessageAboveL == null) {
                return;
            }

            //取消拍照或者图片选择时,返回null,否则<input file> 就是没有反应
            if (resultCode != RESULT_OK) {
                if (uploadMessage != null) {
                    uploadMessage.onReceiveValue(null);
                    uploadMessage = null;
                }
                if (uploadMessageAboveL != null) {
                    uploadMessageAboveL.onReceiveValue(null);
                    uploadMessageAboveL = null;

                }
            }

            //拍照成功和选取照片时
            if (resultCode == RESULT_OK) {
                Uri imageUri = null;

                switch (requestCode) {
                    case REQUEST_CODE_ALBUM:

                        if (data != null) {
                            imageUri = data.getData();
                        }

                        break;
                    case REQUEST_CODE_CAMERA:

                        if (!TextUtils.isEmpty(mCurrentPhotoPath)) {
                            File file = new File(mCurrentPhotoPath);
                            Uri localUri = Uri.fromFile(file);
                            Intent localIntent = new Intent(Intent.ACTION_MEDIA_SCANNER_SCAN_FILE, localUri);
                            sendBroadcast(localIntent);
                            imageUri = Uri.fromFile(file);
                            mLastPhothPath = mCurrentPhotoPath;
                        }
                        break;
                }


                //上传文件
                if (uploadMessage != null) {
                    uploadMessage.onReceiveValue(imageUri);
                    uploadMessage = null;
                }
                if (uploadMessageAboveL != null) {
                    uploadMessageAboveL.onReceiveValue(new Uri[]{imageUri});
                    uploadMessageAboveL = null;

                }

            }

        }


    }

 ```

最后不要忘记，在项目的混淆文件中加入以下规则，保护 openFileChooser() 不被混淆，否则混淆后的包在 Android 4.X 不能正常使用。

```java

#确保openFileChooser方法不被混淆
-keepclassmembers class * extends android.webkit.WebChromeClient{
 public void openFileChooser(...);
 }

 ```

调用系统相机和相册的代码，因为动态权限申请和文件删除的功能，搞的太长了，就不贴了。下面是完整代码的 github 项目地址：

[https://github.com/DRPrincess/DR_WebviewDemo](https://github.com/DRPrincess/DR_WebviewDemo)



# <font color="#008000"> 参考资料 </font>

[Android爬坑之旅之WebView](http://www.jianshu.com/p/3ad7c39858ec)

[Android WebView那些坑之上传文件](http://www.jianshu.com/p/48e688ce801f)

# <font color= "#008000"> 最后 </font>

刚刚开通了个人微信公众号，最新的博客，好玩的事情，都会在上面分享，欢迎关注 (*^o^*)。

<div  align="center">    

![微信公众号](http://raw.githubusercontent.com/DRPrincess/BlogImages/master/qiniu/836a36d6a91d859428783f8ea2ce85d7.png)

</div>
