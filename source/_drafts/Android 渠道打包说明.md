# 渠道打包

- 加固方案：[360加固](http://jiagu.360.cn/qcms/protect.html)
- 渠道包打包方案： [packer-ng](http://jiagu.360.cn/qcms/protect.html)
- 签名文件：[应用签名.zip](/uploads/414700ddafc38a59b91404a8aeaf7901/应用签名.zip)
- 渠道分发的文件：[真容二手车的分发渠道.txt](/uploads/1c7b8a46e1284d03232c4099015dcc57/真容二手车的分发渠道.txt)[车猫二手车的分发渠道.txt](/uploads/5857c74074bac68b0d21f322d53b20de/车猫二手车的分发渠道.txt)
- 推荐博客：[Android-项目中采用的混淆加固多渠道打包方案](https://blog.csdn.net/qq_32452623/article/details/77274757)

### 完整打包流程

![image](/uploads/20d9bad045dc7ce83fbf710b644136a8/image.png)

- 混淆：Android Studio 集成的 proguard  工具打包即可，需要编写好混淆规则
- 打正式包 : gradle-task-assembleRelease
- 加固: 上传到[360加固](http://jiagu.360.cn/qcms/protect.html) 加固，下载加固完成的安装包文件
- 重新签名 ： 可以使用一些重新签名的工具，也可以使用以下命令行方式重新签名，支持V1和V1版签名。
如下：

```
zipalign -v 4 <apk_path> <after_apk_path>//对齐
apksigner sign --ks <keystore_path> <after_apk_path> //重新签名

//以下命令可用来验证对齐和再签名的结果
zipalign -c -v 4 <apk_path>//验证是否对齐
apksigner verify --verbose  <after_apk_path>  //查看签名信息，用来验证是否是 V2 签名

```
其中
apksigner 是Android SDK 自24.0.3开始提供的官方签名工具，位于：Android SDK/build-tools/对应版本/apksigner。

zipalign 是 Android SDK 提供代码对齐工具, 位于 Android SDK/build-tools/对应版本/zipalign。

如果已经配置好 sdk/build-tools/的 Path，直接在 Terminal 中，任何路径下就能使用，如果没有可以选择是配置一下路径或者切换到 sdk/build-tools/ 对应版本／ 中操作。


- 打渠道包 ： 市面上有多种方案，采取的是packer-ng 方案中的脚本打包方式，具体操作看[packer-ng使用说明 ](http://jiagu.360.cn/qcms/protect.html)
