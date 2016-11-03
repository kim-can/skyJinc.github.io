---
title: Android开发规范
date: 2016-09-26 16:49:19
tags: 开发规范
category: Android
---

开发规范其实是让团队高效，很重要的一个环节～～

## 为什么要规范呢？

 - 一个软件的生命周期中，80%的花费在于维护
 - 几乎没有任何一个软件，在其整个生命周期中，均由最初的开发人员来维护
 - 编码规范可以改善软件的可读性，可以让程序员尽快而彻底地理解新的代码

<!-- more -->

## 前言

很多时候规范起了很大的作用，只是很多公司为了快速产出把程序员的时间压榨了导致程序员为了快速产出不能按照规范写代码，导致最后维护起来很麻烦，最后苦的还是程序员... 所以一定要规范起来... 

## 规范的作用

 - 减少维护花费
 - 提高可读性
 - 加快工作交接
 - 减少名字增生
 - 降低缺陷引入的机会


### 常量命名规范
***说明*** 

 - 常量用于保存需要常驻内存中并且经常使用变化不多的数据，定义常量的名称的时候需要遵循望文知意的原则；

***规则***

 - 全部为大写字母；
 - 中间以“_”连接；
 - 望文知意原则；

***举例***

正确

 - public static final int SKY_INT_NUMBER = 1；

错误

 - public static final int sky_int_number= 1;
 - public static final int skyIntNumber = 1; 
 - public static final int NUMBER = 1;
 - public static final int number = 1;
 - public static final int wd = 1;

### 变量命名规范
***说明*** 

 - 变量用于保存系统中的临时数据，变量命名时遵循望文知意，简单明了，驼峰标示等原则

***规则***

 - 首字母小写；
 - java驼峰命名；
 - 望文知意原则；
 - 推荐引用类型变量添加前缀“m”；
 - 如果是View组件变量，则组件名称为xml文件中定义的ID名称去掉下划线，下划线后一位大写；

***举例***

正确

 - public int mSkyIntNumber = 1；
 - public TextView mTvNumber;(xml = tv_number)

错误

 - public int skyIntNumber  = 1;
 - public int intNumber  = 1;
 - public int number  = 1;

### 方法命名规范
***说明*** 

 - 方法名的命名应该遵循简单明了的原则；

***规则***

 - 首字母小写；
 - java驼峰命名；
 - 简单明了原则；

***举例***

正确

 - public int getSkyNmuber();

错误

 - public int getskyNmuber();
 - public int getskynumber();
 - public int getSkynumber();
 - public int getnumber();
 - public int getNumber();

### 类命名规范
***说明*** 

 - 类名主要表示一个类的作用，需要简明，看名字就知道什么意思，并且首字母大写。

***规则***

 - 首字母大写；
 - java驼峰命名；
 - 望文知意原则；
 - 能够说明类的功能和主要作用（注释的作用）；
 - Acitivity类以Acitivity结尾；
 - Fragment类以Fragment结尾；
 - Service类以Service结尾；
 - BroadcastReceiver类以Receiver结尾；
 - ContentProvider类以Provider结尾；
 - Application类以Application结尾；
 - 自定义View类以Custom**View结尾；
 - 自定义Adapter类以Adapter结尾；
 - adapter中的ViewHolder以Holder结尾；
 - 实体Bean以Model结尾；

***举例***

正确

 - public class TimeUtils;//定义时间工具类

错误

 - public class Timeutils;
 - public class Time;
 - public class timeutils;

### 接口命名规范
***说明*** 

 - 接口命名需要简单明了，长度不宜过长；

***规则***

 - 名称前面追加“I”；
 - 字母大写（第二个字母也是大写）；
 - java驼峰命名；
 - 望文知意原则；
 
***举例***

正确

 - public interface I**Listener
 - public interface I**CallBack
 - public interface I**；

错误

 - public interface BaseActivity;
 - public interface Baseactivityinter;
 - public interface BaseInter；
 - public interface ActivityInter；

### 包名规范
***说明*** 

 - 用于分类管理类文件；

***规则***

 - 所有字母小写；
 - 简单明了，层级很深，没有拼接的包名；
 - 望文知意；
 - 按功能划分包名，如“我的”
 - 工具类可以划分为一个工具类的包名，utils，里面可以添加包名层级；
 - 系统类的可以划分为一个系统类的包，system，里面可以添加包名层级；
 - 组件类的可以划分为一个组件类的包，*，里面添加adapter的包名，自定义view包名；
 - Service类的可以划分为一个服务类的包，service，里面可以添加包名层级；
 - 数据库相关类可以划分为一个数据库类，db，里面可以添加数据库相关类，Bean类，数据库服务类等；
 - 广播类的可以划分为广播类的包，receiver，可以放一些广播相关的类；
 - 网络类相关的可以划分为，http，放一些网络相关的类

***举例***

 - 没有

### 目录名称规范
***说明*** 

 - 主要是一些jar包，so文件的配置目录名称

***规则***

 - 全部为小写字母；
 - 简单明了；
 - 望文知意；
 - 驼峰表示；

***举例***

 - lib：第三方jar的保存路径；
 - jniLibs：jni引用的so文件的目录；

### 布局文件名称规范
***说明*** 

 - 主要包含资源文件的命名问题；

***规则***

 - 全部为小写字母；
 - 中间以”_”连接；
 - 望文知意原则；
 - 布局文件的开头问类名；
 - 列表项的xml布局文件名称：item_类名.xml；
 - activity类的xml文件名称：activity_类名.xml；
 - fragment类的xml文件名称：fragment_类名.xml；
 - 自定义View的xml文件的名称：父类名_类名.xml；

***举例***

 - 尽量不使用大写字母等 没有例子

### drawable,mipmap,string,资源目录
***说明*** 

 - 主要包含资源文件的命名问题

***规则***

 - 全部为小写字母；
 - 中间以”_”连接；
 - 望文知意原则；

***举例***

 - selector_业务_状态_后缀(src、bg、color)
 - shapre_业务_状态_后缀(src、bg、color)
 - textview组件 tv_pay_money
 - 页面标题，命名格式为：title_{页面}
 - 按钮文字，命名格式为：btn_{按钮事件}
 - 标签文字，命名格式为：label_{标签文字}
 - 选项卡文字，命名格式为：tab_{选项卡文字}
 - 消息框文字，命名格式为：toast_{消息}
 - 编辑框的提示文字，命名格式为：hint_{提示信息}
 - 图片的描述文字，命名格式为：desc_{图片文字}
 - 对话框的文字，命名格式为：dialog_{文字}
 
### 注释规范
***说明*** 

 - 在类、接口定义之前当对其进行注释，包括类、接口的目的、作用、功能、继承于何种父类，实现的接口、实现的算法、使用方法、示例程序等。

***举例***
	
	类注释
	/**
	 * author:作者
	 * time:时间
	 * desc:描述
	 */
	 
	方法注释
	/**
	 * desc:描述
	 * @param 参数名 参数描述
	 * @param 参数名2 参数描述
	 * @return 返回值类型说明
	 * @throws Exception 异常说明
	 */

	内部逻辑注释模板：
	//登陆成功
	if (response.isLogin() == 0) {
	   Toast.makeText(H5Activity.this, "登陆成功", Toast.LENGTH_LONG).show();
	   //跳转首页
	   goto(home)
	}
	//登陆失败
	else if (response.isLogin() == -1) {
	  Toast.makeText(H5Activity.this, "支付失败", Toast.LENGTH_LONG).show();
	  //刷新当前页面
	  reflush(currentUrl);
	}
### 异常规范
***说明*** 

 - 定义异常的时候，异常的后缀名称以Exception结尾，及**Exception；


***规则***

 - 尽量英文描述，简单明了

***举例***

 - 没有

###  小规范

 - class文件行数最好少于2K
 - 对那些临时性的、短期的、够棒但不完美的代码，请使用 TODO 注释。

## 结尾

这个只是平常的规范, 不要觉得麻烦,  因为连规范都觉得麻烦, 你就不是一个好的程序员... 

当然规则是死的，如果你是架构师，可以自己定义一套更合理的规范，让团队变的更高效...
