---
title: Sky架构简单使用
date: 2016-08-26 12:15:32
tags: 架构
category: Android
---
无论MVC或是MVP模式都不可避免地存在一个弊端：
`额外的代码复杂度及学习成本`

<!-- more -->
## MVP和MVC

无论MVC或是MVP模式都不可避免地存在一个弊端：
`额外的代码复杂度及学习成本`

### MVC
	1、View接受用户的交互请求
	2、View将请求转交给Controller
	3、Controller操作Model进行数据更新
	4、数据更新之后，Model通知View数据变化
	5、View显示更新之后的数据
![图1](https://skyJinc.github.io/images/sky架构/2.png)
	
### MVP
	1、View接受用户的交互请求
	2、View将请求转交给Presenter
	3、Presenter操作Model进行数据库更新
	4、数据更新之后，Model通知Presenter数据发生变化
	5、Presenter更新View的数据
![图1](https://skyJinc.github.io/images/sky架构/3.png)


说明：presenter  我这里说要用biz(business)代替

## Sky简介
开头已经说了，无论MVC或是MVP模式都不可避免地存在一个弊端：`额外的代码复杂度及学习成本`  

Sky开发架构都优化了那些问题

### 第一个
因为通过接口交互，所以每一个View(Activity,Fragment,DialogFragment...) 都会多一个IView接口，一个IBiz和实现类Biz.

以Activity为例, 文件数量从1个变成了4个.
![图1](https://skyJinc.github.io/images/sky架构/4.png)

Sky架构做了调整，采用一个Java文件  包含 接口 和 类

![图1](https://skyJinc.github.io/images/sky架构/5.png)

这里要说到Java文件的限制，一个类文件只能有一个***public class*** 而Activity在AndroidManifest里必须是public，所以业务接口(IBiz)可以声明成public，而Activity不可以

Biz
![图1](https://skyJinc.github.io/images/sky架构/6.png)

Activity
![图1](https://skyJinc.github.io/images/sky架构/7.png)

### 第二个
activity 和 biz 依赖的问题

正常的依赖关系
Activity
![图1](https://skyJinc.github.io/images/sky架构/8.png)
Biz
![图1](https://skyJinc.github.io/images/sky架构/11.png)


Sky架构做了调整，采用泛型+注解方式依赖

Activity
![图1](https://skyJinc.github.io/images/sky架构/9.png)

Biz
![图1](https://skyJinc.github.io/images/sky架构/10.png)

#### @Impl  注解
可以看到Activity接口和Biz接口中都注解了@Impl(实现类.class)

通过@Impl 获取实现类class 获取实例

![图1](https://skyJinc.github.io/images/sky架构/12.png)


### 第三个
SKYHelper 提供一下接口方便全局使用

	display(Class<D> eClass)；跳转
	
	biz(Class<B> service)； 业务
	 
	isExist(Class<B> service)； 业务类存不存在(因为业务类是根据View声明周期走)
	 
	bizList(Class<B> service)； 业务类 批量获取
	
	common(Class<B> service)； 公共业务
	 
	http(Class<H> httpClazz)； 网络
	
	interfaces(Class<I> implClazz)； 接口
	
	screenHelper();Activity 管理器
	 
	threadPoolHelper()； 线程池管理器 
	
	mainLooper()；主线程
	
	downloader()； 下载管理器
	
	toast()；提示信息
	
	contact()； 系统通讯录管理器
	
	fileCacheManage()； 文件缓存管理器
	
-帮助类可以自定义，保留以上的接口，自定义helper

-继承 SKYModulesManage  和 SKYHelper 来自定义

例子 自定义数据库管理

![图1](https://skyJinc.github.io/images/sky架构/13.png)

![图1](https://skyJinc.github.io/images/sky架构/14.png)

![图1](https://skyJinc.github.io/images/sky架构/15.png)

注意 ： SKYModulesManage 不用关心单例和重复创建的问题，因为统一由Application管理，当然也可以不继承SKYModulesManage 自己来处理 

### 第四个

1.View（Activity,Fragment,DialogFragment）,Biz, Display(跳转调度), Commom(公共业务)，http, 还是以@Impl注解的接口都被代理， 进行解析方法注解

2.因为解析每一个方法的注解，比较耗时，采用了缓存策略,缓存接口的每一个方法，不会进行第二次解析...(思路来自[retrofit](https://github.com/square/retrofit))

3.除了View和Biz的实例（创建和销毁），缓存是跟着View生命周期走，其他的都实例缓存都统一由缓存管理器进行统一管理


## SKY简单使用

### 引用
架构里使用[butterknife](https://github.com/JakeWharton/butterknife) 所以 需要指定的插件

 ***classpath 'com.neenbedankt.gradle.plugins:android-apt:1.8'***
#### 第一种引用方式
第一步 - Project-build.gradle

	buildscript {
	    repositories {
	        jcenter()
	    }
	    dependencies {
	        classpath 'com.android.tools.build:gradle:2.1.2'
	        classpath 'com.neenbedankt.gradle.plugins:android-apt:1.8'
	    }
	}		

	allprojects {
    	repositories {
       	 jcenter()
        	//远程仓库
        	maven { url "https://github.com/J2W/mvn-repo-j2w/raw/master/repository" }
       ｝
    ｝
    
第二步 - App-build.gradle:


	apply plugin: 'android-apt'
	
	dependencies {
   		compile fileTree(dir: 'libs', include: ['*.jar'])
   		compile 'jc:sky:1.4'//请看github最新版本
    	apt 'com.jakewharton:butterknife-compiler:8.2.1'
	}

#### 第二种引用方式
第一步 - Project-build.gradle 文件

	allprojects {
        repositories {
            ...
            maven { url "https://jitpack.io" }
        }
    }

第二步 - App-build.gradle:

	 dependencies {
     	ompile 'com.github.skyJinc:sky:1.5'
    }  
    
### 初始化

实现 ISKYBind 

	//架构日志开关
	boolean isLogOpen();
	
	 //网络适配器
    Retrofit getRestAdapter(Builder var1);

	//方法拦截器
    SKYMethods getMethodInterceptor(jc.sky.modules.methodProxy.SKYMethods.Builder var1);

	//核心 -  配置管理器
    SKYModulesManage getModulesManage();


	//在Application的onCreate里绑定
	Application - onCreate -> SKYHelper.bind(ISKYBind)

实现类可以是Application 也可以自定义

以Demo项目为例子

![图1](https://skyJinc.github.io/images/sky架构/16.png)

### 使用

创建Activity和Biz 手动创建很麻烦，所以 我写了一个模板 

AndroidStudio ->设置-> Editor->File And Code Templates

Activity模板例子  ***[参考](https://github.com/skyJinc/sky/wiki/%E7%BB%93%E6%9E%84%E6%A8%A1%E6%9D%BF)***

	import android.os.Bundle;
	import jc.sky.core.Impl;
	import jc.sky.view.SKYBuilder;
	import jc.sky.view.SKYActivity;
	
	/**
	 * @创建人 ${USER}
	 * @创建时间 ${DATE} ${TIME}
	 * @类描述 一句话描述你的UI
	 */
	public class ${NAME}Activity extends SKYActivity<I${NAME}Biz> implements I${NAME}Activity {
		
		@Override protected SKYBuilder build(SKYBuilder skyBuilder) {
			return skyBuilder;
		}
	
		@Override protected void initData(Bundle bundle) {
	
		}
		
	}
	@Impl(${NAME}Activity.class)
	interface I${NAME}Activity {
	
	}
	
![图1](https://skyJinc.github.io/images/sky架构/17.png)
	
####开始写业务

#####创建
继承 SKYActivity - 采用了建造者模式 进行配置

页面
![图1](https://skyJinc.github.io/images/sky架构/18.png)

Activity
![图1](https://skyJinc.github.io/images/sky架构/19.png)

SKYBuilder 里有很多配置，主要的是 layoutId 设置布局文件

Biz
![图1](https://skyJinc.github.io/images/sky架构/20.png)

##### 业务逻辑 - 注解使用
登陆后打印账号和密码，模拟网络请求 延迟3秒钟 
![图1](https://skyJinc.github.io/images/sky架构/21.png)
![图1](https://skyJinc.github.io/images/sky架构/22.png)

这里你们会看到 这个不是在主线程执行 线程等待嘛？ 
是的, 所以我们需要给业务接口增加注释，告诉它你是需要在子线程执行的

![图1](https://skyJinc.github.io/images/sky架构/23.png)

提供了三种线程池

	HTTP 专门提供给网络请求使用
	WORK 专门给IO操作，数据库查询等使用
	SINGLEWORK 专门给需要队列形式，一个一个执行使用（比如数据库插入操作，必须是队列形式）

##### 业务逻辑 - 如何回调

Activity 和 Biz　是如何互相调用　和　访问的呢？

在注解使用中　我们看到Activity 点击事件 调用了***biz()***.login() 来执行业务

在biz里 可以使用 ***ui()*** 来进行回调

在View层接口里 声明两个接口，一个是用于开关进度条，一个是用于修改登陆按钮名称

![图1](https://skyJinc.github.io/images/sky架构/24.png)

biz 里 进行调用

![图1](https://skyJinc.github.io/images/sky架构/25.png)

可能有人会问，在子线程里 修改Button的名字 会报错的，因为只有在主线程修改。。。

是的, 可是运行Demo你会发现居然是可以的，因为仔细看过上面介绍的朋友会知道，我们View层和Biz层 都被动态代理了，所以 调用UI() 会自动判定是在主线程还是子线程

##### 业务逻辑 - 防止重复执行

很多开发的时候大家为了防止一个业务多次调用，会通过各种方式 来判定这个方法有没有在执行，如果在执行，就忽略掉～～

sky架构的所有业务接口都是防止重复执行的

![图1](https://skyJinc.github.io/images/sky架构/26.png)

循环调用了三次，只有一次执行成功，因为防止重复执行，所以只有方法真正执行完毕后才能继续调用，也可以注解允许重复执行，***@Repeat(true)*** 默认是fasle

![图1](https://skyJinc.github.io/images/sky架构/27.png)

##### 业务逻辑 - 列表页

创建列表页面  我使用的是mac所以快捷键是 command + n  选择SKYActivity 和 SKYIBiz-Activity , 也可以直接选择对应的包，调出菜单

![图1](https://skyJinc.github.io/images/sky架构/28.png)

输入名字

![图1](https://skyJinc.github.io/images/sky架构/29.png)


创建适配器

	继承 SKYRVAdapter<Model,Holder>


	model
	
	/**
	 * @创建人 sky
	 * @创建时间 16/8/27 上午11:59
	 * @类描述
	 */
	public class ListModel {
	
		public String title;
	}

	/**
	 * holder
	 */
	public class ItemHolder extends SKYHolder<ListModel> {

		public ItemHolder(View view) {
			super(view);
		}

		@Override public void bindData(ListModel listModel, int i) {

		}
	}
	
![图1](https://skyJinc.github.io/images/sky架构/31.png)

这里使用了[butterknife](https://github.com/JakeWharton/butterknife) 插件

![图1](https://skyJinc.github.io/images/sky架构/32.png)

![图1](https://skyJinc.github.io/images/sky架构/33.png)

![图1](https://skyJinc.github.io/images/sky架构/34.png)

builder里配置

![图1](https://skyJinc.github.io/images/sky架构/35.png)

开始写业务

View层接口定义 

	//设置数据
	void setData(List<ListModel> listModelList);
	
Biz层接口定义

	/**
	 * 加载数据
	 */
	@Background(BackgroundType.WORK) void loadData();

![图1](https://skyJinc.github.io/images/sky架构/36.png)

![图1](https://skyJinc.github.io/images/sky架构/37.png)

##### 业务逻辑 - display 跳转调度  拦截器

个人比较喜欢 把跳转写到当前类，模仿fragment 写法

![图1](https://skyJinc.github.io/images/sky架构/38.png)

display里包含了所有intent 和 fragment 方法

![图1](https://skyJinc.github.io/images/sky架构/39.png)

为什么要使用display来跳转呢？ 

如果需求是 想拦截跳转前 验证是否登陆 该如何处理？

Sky架构-- ISKYBind 接口里 设置 display拦截器 

这里有Display Start和End 拦截器 根据需求我们设置Start拦截器

![图1](https://skyJinc.github.io/images/sky架构/40.png)

定义拦截器

![图1](https://skyJinc.github.io/images/sky架构/41.png)

设置拦截器

![图1](https://skyJinc.github.io/images/sky架构/42.png)

我们在loginActivity 页面 点击登陆以后 跳转到 ListActivity里

![图1](https://skyJinc.github.io/images/sky架构/43.png)


##### 业务逻辑 - 拦截器

![图1](https://skyJinc.github.io/images/sky架构/44.png)

跟display类似，通过demo 项目自己可以研究一下，根据不同业务需求来添加拦截器

##### 业务逻辑 - 视图切换

![图1](https://skyJinc.github.io/images/sky架构/45.png)

	 //进度布局
    void layoutLoadingId(@LayoutRes int layoutId);
    //错误布局
    void layoutEmptyId(@LayoutRes int layoutId);
    //业务错误布局
    void layoutBizErrorId(@LayoutRes int layoutId);
    //网络错误布局
    void layoutHttpErrorId(@LayoutRes int layoutId);

	Activity里调用 调用来切换
	showHttpError();
	showContent();
	showLoading();
	showEmpty();
	showBizError();
	
注:sky架构采用 ViewStub 延迟加载


单个Activity布局设置，如果想所有的View层布局都有默认布局？

实现 SKYIViewCommon 接口

	@Override
	public int layoutLoading() {
		return 0;
	}

	@Override
	public int layoutEmpty() {
		return 0;
	}

	@Override
	public int layoutBizError() {
		return 0;
	}

	@Override
	public int layoutHttpError() {
		return 0;
	}

绑定
![图1](https://skyJinc.github.io/images/sky架构/46.png)

优先级: 设置单个activity优先，如果activity没有设置，获取全局默认布局


##### 业务逻辑 - 全局Biz 执行业务
例子，LoginActivity页面登陆后 跳转到 ListActivity页面 获取数据后，通知LoginActivity 修改按钮名称

改进一下Demo 

我们在ListBiz里 调用 LoginBiz的业务 告诉它进行刷新

写一个业务接口 更新按钮的
![图1](https://skyJinc.github.io/images/sky架构/47.png)

![图1](https://skyJinc.github.io/images/sky架构/48.png)


说明: 在任何都可以调用业务接口， 

	SKYHelper.biz(ILoginBiz.class)；
	
	参数可以是任何业务接口的class, 不必担心返回空值，因为已经做了处理，因为biz的生命周期是跟着View层走，所以如果你调用了已经销毁的Biz业务，它会执行空方法，不会报空指针异常。。



##### 业务逻辑 - 公共业务接口 ICommon

没有View层，只有业务逻辑 可以定义公共的业务接口进行业务的执行

后续版本会根据 [agera](https://github.com/google/agera) 进行修改，采用 观察者模式 进行获取数据，更新界面




## 结尾

还有一些使用我没有进行说明，比如 下拉刷新，分页加载，toolbar 绑定等等...
可以看源代码的 ***[SKYBuilder](https://github.com/skyJinc/sky/blob/master/app/src/main/java/jc/sky/view/SKYBuilder.java)*** 

下载Demo 看看 调试调试。。。
[DEMO](https://github.com/skyJinc/SkyApp)


原理和实现 都会在后续的文章写出来。。。。

