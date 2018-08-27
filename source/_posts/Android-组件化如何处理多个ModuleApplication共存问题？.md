---
title: Android-组件化如何处理多个ModuleApplication共存问题？
date: 2018-08-27 01:46:18
tags:
---

多 Application 无法共存，使用反射可以达到最终目的。
<!--more-->


# 一个美好的设想

组件化的目的是为了业务解耦，每个业务模块需要不同的功能，例如车辆详情模块需要第三方分享，城市定位模块需要百度地位等。有些特殊功能的初始化需要在 Application 中去做，但是这些功能并非全部业务组件都用到的东西，放到 BaseApplication 不合适。

因此，我想这样操作：

- 模块共有的初始化，放入BaseApplication 中。
- 模块自身的特殊功能初始化，放在自己的 Application。

设想是美好的，但实现前需要先思考一个问题：

多 Module 项目开发的时候，app module 和 library module 的 都有不同的自定义 Application ,可以共存并且自动合并吗？

答案是 No。

# 为什么不可以？



首先，自定义 Application 需要声明在 AndroidManifest.xml 中。其次，每个 Module 都有该清单文件，但是最终的 APK 文件只能包含一个。因此，在构建应用时，Gradle 构建会将所有清单文件合并到一个封装到 APK 的清单文件中。

合并的优先级是:

App Module > Library Module

合并的规则:

![](http://oriwplcze.bkt.clouddn.com/component_2.png)


结合我们的情况，是值 A 合并值 B，会产生冲突错误，如下是我的亲身试法：

```
Execution failed for task ':app:processDebugManifest'.
> Manifest merger failed : Attribute application@name value=(com.baseres.BaseApplication) from AndroidManifest.xml:8:9-51
  	is also present at [:carcomponent] AndroidManifest.xml:14:9-55 value=(com.carcomponent.CarApplication).
  	Suggestion: add 'tools:replace="android:name"' to <application> element at AndroidManifest.xml:7:5-24:19 to override.

```

错误信息中给出了解决建议，在高优先级的 App Module 中使用 tools:replace="android:name"，但这样做是直接用值 A 替换了值 B，并非我们想要的结果。

除了上面报错的方法，另外再推荐给大家一个方法，打开 App Module 的 AndroidManifest.xml 文件，选择下方 Merged Manifest 选项卡，可以看到预合并结果。

![](http://oriwplcze.bkt.clouddn.com/component_3.png)


以上我们就明白为什么不同的 Application 不能共存的原因。那还有其他方法去实现美好设想吗？

这次的答案是 Yes !

# 强烈感谢反射的存在

回想需求来源，是需要在 Application 创建时期，实现模块的特殊功能初始化，初始化时间和初始化内容
是确定无误，问题核心是如何连接两者。直接连接方式实践失败，只能采用间接方式。一提到间接，于是想起来了反射。

- 初始化内容，假设现在写在  ModuleA 的 A 类，ModuleB 的 B 类中的 init() 方法中。
- 最终 Application 的 onCreate 中通过反射拿到 A 类 和 B 类,调用各自的 init() 方法。

以上就已经解决我们的问题，但是为了 A 类 和 B 类 中的初始化方法名称保持一致，最好使用接口强制规范。创建接口 IComponentApplication，其中定义好方法签名，让 A 类和 B 类都实现它。如此，接口应放在 基础库 BaseRes 中，反射调用内容放在 BaseApplication 最为合适。


所有的思维逻辑演变成代码是这样纸的：

```

Module A:

private class A implements IComponentApplication{

	public void init(Application application){
	   // ModuleA的初始化
	}

}

Module B:

private class B implements IComponentApplication{

	public void init(Application application){
	   // ModuleB的初始化
	}

}


BaseRes 中：

public interface IComponentApplication {

	void init(Application application);

}

public class MyApplication extends Application {
	private static final String[] MODULESLIST =
	              {"com.moduleA.A",
	              "com.moduleA.B"};

	@Override
	public void onCreate() {
		super.onCreate();

		//Module类的APP初始化
		modulesApplicationInit();
	}

	private void modulesApplicationInit(){
		 for (String moduleImpl : MODULESLIST){
		     try {
		         Class<?> clazz = Class.forName(moduleImpl);
		         Object obj = clazz.newInstance();
		         if (obj instanceof IComponentApplication){
		             ((IComponentApplication) obj).init(BaseApplication.getInstance());
		         }
		     } catch (ClassNotFoundException e) {
		         e.printStackTrace();
		     } catch (IllegalAccessException e) {
		         e.printStackTrace();
		     } catch (InstantiationException e) {
		         e.printStackTrace();
		     }
		  }
	}
}

```


# 最后有福利

福利是组件化系列博客所说的方案，写出来了一个Demo,传送门在这里 [你们可能需要的组件化 Demo](https://github.com/DRPrincess/DRComponentDemo)。说是福利，大概是我的脸太大，技术浅薄，欢迎指正和交流。

另外，关于 AndroidManifest.xml 的合并，详细了解可以看这里[合并多个清单文件 Google 官方文档](https://developer.android.com/studio/build/manifest-merge#merge_priorities),需要自备交通工具。

下篇文章见。


---

<center>
<font color=gray>欢迎关注博主的微信公众号，快快加入哦，期待与你一起成长！</font>
<img src="http://oriwplcze.bkt.clouddn.com/qrcode_130.png" width="130" height="130" />
</center>
