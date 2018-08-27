
# 重构原因：

- 从车猫二手车迁移而来，历史悠久，代码繁杂混乱
- UI车猫元素和真容元素共存
- 为了顺应 Android 系统的更新和发展 targetSdkVersion = 22 需要升级 targetSdkVersion = 22 。

  实际上 GooglePlay 很早已经发出声明，国内市场现在也开始跟进。
  [OPPO应用市场对 Android 版本的要求声明](https://open.oppomobile.com/service/message/detail?id=91487)

# 重构层面
- UI层面-排查真容和车猫元素，做适当调整，字体，图片，按钮大小颜色需要出一个统一规范

- 代码层面-前台页面和逻辑分离

- 代码层面-兼容系统,需要跨4个系统改动过兼容，以下是相关资料

相关技术参考资料：

|系统|系统资料连接|
|----|----|
|Android M|https://developer.android.com/about/versions/marshmallow/|
|Android N|https://developer.android.com/about/versions/nougat/|
|Android O|https://developer.android.com/about/versions/oreo/|
|Android P|https://developer.android.com/preview/|
|Android P|Android P非SDK接口管控特性解读及适配:https://blog.csdn.net/HuaweiOpenlab/article/details/80417311
|Android P|Android P版本刘海屏适配指南:https://blog.csdn.net/zhangbijun1230/article/details/80097746|

# 重构计划

  1. 采用模块化分层思想，重构初期阶段，先整理独立出抽出基础库，建立真容工具二方库（3天）
      1. 网络库 （7.9）
      2. 常用工具类库（不包含UI）（7.9）
      3. 常用UI控件库 （7.11）
      4. 车型库（Android 目前没有车型库，此时是否独立，待讨论） （7.16）

  2. 代码架构从高耦合 MVC 转为 MVP （所有的类整理出来，最快需要 3-4 天）

     1. 各类Base类制定，BaseFragment，BaseActivity，BaseView,BasePresenter
     2. 现有页面整理调整为 MVP

  3. 开始做系统兼容，下面列出主要兼容项 （大概是 4天，时间未定，非常可能会出现意外问题）
     1. Android 6.0 权限兼容 （需排查代码找出所有需要权限兼容的功能）
     2. Android 7.0 FileProvider 兼容  （需排查代码找出所有访问本地文件的地方）
     3. Android 8.0 gradle 构建版本需升级 3.0，后台服务限制调整，应用 logo 图标调整。
     4. Android P 非 SDK 接口扫描更改

  4. 采用模块化分层思想，页面按照大功能模块，分别建立moduel,以后模块可以分开开发和测试，减少出错率和项目开发时间 （3天，模块之间的连接和相互调用，页面归分，可能会遇到问题，）
     1. 用户模块（登录）
     2. 设置模块 （关于公司信息，更新等）
     3. 整理连接模块 （首页和我的）
     4. 检测模块 （云端检测，检测订单列表，检测报告列表）
     5. 车辆模块 （卖车列表，卖车页，车辆列表，筛选条件页面，搜索页，收藏列表，订阅列表，浏览记录）

  5. UI调整（所需时间需要看具体UI图）

----

# 截止到2018.8.20 总结

因为公司停业原因，组件化项目不得不暂停，目前进度和原先计划有所不同，暂时没有做版本兼容和全部架构调整，业务模块已经全部分离开。

- app ： 主框架模块，有启动和欢迎页面，并负责连接其他模块
- baselib : 基本二方库，通用的工具类和自定义控件
- cmnetworklib : fapi 封装网络库
- basicRes : 真容二手车的通用资源管理，包括路由和Base类
- buycarcomponent : 买车模块
- sellcarcomponent : 卖车模块
- carbrandcomponent : 车型款选择模块
- citycomponent : 选择城市模块
- couponcomponent : 优惠券功能模块
- settingcomponent : APP设置模块
- updatecomponent : APP更新模块
- usercomponent : 用户登录模块
- onlinecertcomponent : 在线检测模块

---

待完成：

- 微信分享组件化接入
- 个推组件化接入方案
- 百度定位组件化接入方案
- 友盟统计组件化接入方案
