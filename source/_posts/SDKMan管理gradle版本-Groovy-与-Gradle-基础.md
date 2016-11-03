---
title: 'SDKMan管理gradle版本 与 Groovy 基础'
date: 2016-09-08 17:42:01
tags: 开发环境
category: Android
---
sdkman是什么？

sdkman(The Software Development Kit Manager), 中文名为:软件开发工具管理器．这个工具的主要用途是用来解决在类unix操作系统(如mac, linux等)中多种版本开发工具的切换, 安装和卸载的工作

<!-- more -->

## [SKDMAN官网](http://sdkman.io/)

说明：安装和使用 官网说明已经很简单了。跟着我走一遍吧。。

打开 terminal 执行

	curl -s "https://get.sdkman.io" | bash
	
安装完毕以后

	source "$HOME/.sdkman/bin/sdkman-init.sh"
	
	//如果没有权限 前面加 sudo
	sudo source "$HOME/.sdkman/bin/sdkman-init.sh"
	
检查是否安装成功

	 sdk version
![tu](https://skyJinc.github.io/images/sdkman/1.png)

自定义位置，默认是在/usr/.sdkman里。

	export SDKMAN_DIR="/usr/local/sdkman" && curl -s "https://get.sdkman.io" | bash

### Gradle 安装

我们安装两个版本 来 互相切换 做讲解使用

	sdk install gradle 3.0

	sdk install gradle 2.14
	
![tu](https://skyJinc.github.io/images/sdkman/3.png)
 
会提示
	
	Do you want gradle 2.14 to be set as default? (Y/n):
	//默认选择版本

查看已经安装的gradle版本

	sdk list gradle
	
![tu](https://skyJinc.github.io/images/sdkman/2.png)

 - 这里 * 表示已经安装的 > 表示 现在指向的版本

切换版本

	sdk default gradle 3.0
	
![tu](https://skyJinc.github.io/images/sdkman/4.png)

gradle 命令查看一下版本
	
	gradle -v

![tu](https://skyJinc.github.io/images/sdkman/5.png)

 * 因为环境变量是sdkman 自动生成，如果你手动设置了 删除了吧。。

查看环境变量

	echo $GRADLE_HOME

![tu](https://skyJinc.github.io/images/sdkman/9.png)

	echo $PATH

![tu](https://skyJinc.github.io/images/sdkman/10.png)




### AndroidStudio gradle 3.0配置

因为个人比较喜欢使用 gradle 本地版本

AS配置

![tu](https://skyJinc.github.io/images/sdkman/6.png)

因为使用Gradle3.0对应插件2.2.0以下版本有BUG...升级吧。

![tu](https://skyJinc.github.io/images/sdkman/7.png)

[gradle插件最新版本](https://bintray.com/android/android-tools/com.android.tools.build.gradle)

对应Gradle 3.0 版本

	buildscript {
	    repositories {
	        jcenter()
	    }
	    dependencies {
	        classpath 'com.android.tools.build:gradle:2.2.0-rc1'
	    }
	}
	
### AndroidStudio gradle 2.14 配置

切换gradle 版本

	sdk use gradle 2.14
	
![tu](https://skyJinc.github.io/images/sdkman/11.png)

gradle 命令查看一下版本
	
	gradle -v

![tu](https://skyJinc.github.io/images/sdkman/12.png)

* 是不是很快？切换版本

AS配置

![tu](https://skyJinc.github.io/images/sdkman/13.png)

[gradle插件最新版本](https://bintray.com/android/android-tools/com.android.tools.build.gradle)

对应Gradle 2.14 版本

	buildscript {
	    repositories {
	        jcenter()
	    }
	    dependencies {
	        classpath 'com.android.tools.build:gradle:2.1.2'
	    }
	}

 * sdkman 还是蛮好玩的... 也很简单.. 用用看吧。。。

## Groovy 基础玩法

Gradle采用了Groovy语言作为主要的脚本语言。一个build.gradle 文件，其实是一个Groovy类。

Groovy 是一个基于 JVM 的语言，代码最终编译成字节码（bytecode）在 JVM 上运行。它具有类似于 Java 的语法风格，但是语法又比 Java 要灵活和方便，同时具有动态语言（如 ruby 和 Python）的一些特性。



使用 sdkman 下载

	sdk list groovy
	
![tu](https://skyJinc.github.io/images/sdkman/14.png)

安装最新的

	sdk install groovy
	
检查

	groovy -v
	
![tu](https://skyJinc.github.io/images/sdkman/15.png)

### 开始编辑

安装好环境之后，先来一个 hello, world！

vim 文本编辑器 应该都知道的.. 不知道的补课去吧

新建文件

	vim sky.groovy
	
写上内容

	println "hello, skyJC"
	
保存退出，执行

![tu](https://skyJinc.github.io/images/sdkman/16.png)

是不是很简单？？？？


groovy 基础的例子

这样一个 Groovy 脚本，经过编译之后，会产生一个继承自 groovy.lang.Script 类的子类：

	public class ScriptClass extends Script {
	    public ScriptClass() {
	        CallSite[] var1 = $getCallSiteArray();
	    }
	
	    public ScriptClass(Binding context) {
	        CallSite[] var2 = $getCallSiteArray();
	        super(context);
	    }
	
	    public static void main(String... args) {
	        CallSite[] var1 = $getCallSiteArray();
	        var1[0].call(InvokerHelper.class, ScriptClass.class, args);
	    }
	
	    public Object run() {//关键方法
	        CallSite[] var1 = $getCallSiteArray();
	        return var1[1].callCurrent(this, "hello, skyJC");// got it?
	    }
	}

### Groovy 支持如下的方式来定义变量和函数：

	def varAndMethod() {
	    def a = 100 //不显式声明变量类型
	    a = "jincan" //运行时改变类型
	
	    println a//无需；结束一行代码
	    a = 5000 //最后一行作为返回值
	}
	def ret = varAndMethod()//文件内运行方法
	
	println ret//输出5000
	
![tu](https://skyJinc.github.io/images/sdkman/17.png)

### Groovy 支持单引号，双引号，三单引号声明一个字符串；

	def test() {
	        def singleQ = 'hello, jincan'// 声明为java.lang.String
	        def doubleQ = "hello, 连接-- ${singleQ}"// 如果有${},则为groovy.lang.GString，支持变量替换;否则为java.lang.String
	        def tripleQ = '''hello,triple quot'''// 允许多行，而不需要+号
	        println singleQ
	        println doubleQ
	        println tripleQ
	
	        singleQ = "返回值"
	}
	
	println test()
	
![tu](https://skyJinc.github.io/images/sdkman/18.png)

### Groovy 默认使用 java.util.ArrayList 来提供 List 服务，但提供了更加灵活易用的操作方式：

	def playList() {
	    def lst = ["skyJC",28,"有为青年",2000.22]//支持不同类型元素
	
	    println(lst)
	}
	
	playList()
	
![tu](https://skyJinc.github.io/images/sdkman/19.png)

### Map，注意KEY

	def playMap() {
	    def map = [a: "a", b: "b"]
	
	    println(map)
	
	    def key = "name"
	    def map2 = [key: 'a']//未使用
	    def map3 = [(key): 'a']//使用
	
	    def value = (key)
	    println(value)
	    println(map2)
	    println(map3)
	}
	
	playMap()	
	
![tu](https://skyJinc.github.io/images/sdkman/20.png)

### Groovy 提供了更强大的 import

默认 import，这些类都是被默认 import 到代码中的，可以直接使用

	import java.lang.*
	import java.util.*
	import java.io.*
	import java.net.*
	import groovy.lang.*
	import groovy.util.*
	import java.math.BigInteger
	import java.math.BigDecimal
	
引入一个类，通过 as 关键字赋予一个别名

	import java.lang.String as KString
	
	println(new KString("aaa"))
	
	
### 面向对象特性
哈哈，我是学Java出身。。看到这个我很开心。。。

	class People{
	    String name
	    int age
	}
	
	People p1 = new People();
	People p2 = new People(name:"Luis",age: 29)//通过类似 map 的方式赋值参数
	
	
	println(p1.name)
	println(p2.name)
![tu](https://skyJinc.github.io/images/sdkman/21.png)

### Groovy 提供了一个叫做 Trait 特性实现了多继承

	trait Fly {
	    void fly() {
	        println("jc")
	    }
	}
	
	trait Walk {
	    void walk() {
	        println("sky")
	    }
	}
	
	class Duck implements Fly, Walk {
	
	}
	
	Duck duck = new Duck()
	duck.fly()
	duck.walk()
![tu](https://skyJinc.github.io/images/sdkman/22.png)

Groovy 还有很多深入的内容

[参考](https://pragprog.com/book/vslg2/programming-groovy-2)
