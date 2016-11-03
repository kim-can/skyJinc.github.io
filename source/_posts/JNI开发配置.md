---
title: JNI配置和开发
date: 2016-09-06 19:54:49
tags: JNI
category: Android
---
## 介绍

 - 什么是NDK？

	NDK全称是Native Development Kit，NDK提供了一系列的工具，帮助开发者快速开发C（或C++）的动态库，并能自动将so和java应用一起打包成apk。NDK集成了交叉编译器（交叉编译器需要UNIX或LINUX系统环境），并提供了相应的mk文件隔离CPU、平台、ABI等差异，开发人员只需要简单修改mk文件（指出“哪些文件需要编译”、“编译特性要求”等），就可以创建出so。
	
<!-- more -->

 - 为什么使用NDK？

	1.代码的保护。由于apk的java层代码很容易被反编译，而C/C++库反汇难度较大。
	
	2.可以方便地使用现存的开源库。大部分现存的开源库都是用C/C++代码编写的。
	
	3.提高程序的执行效率。将要求高性能的应用逻辑使用C开发，从而提高应用程序的执行效率。
	
	4.便于移植。用C/C++写得库可以方便在其他的嵌入式平台上再次使用。

 - 什么是JNI？
 
 	JNI全称为：Java Native Interface。JNI 是本地编程接口，它使得在 Java 虚拟机内部运行的 	Java 代码能够与用其它语言(如 C、C++)编写的代码进行交互。
 	
 - 为什么使用JNI？
  
  	JNI的目的是使java方法能够调用c实现的一些函数。
  	
 - 安卓中的so文件是什么？

	Android中用到的so文件是一个c++的函数库。在android的JNI中，要先将相应的C语言打包成so库，	然后导入到lib文件夹中供java调用。
	
## AS 安装 NDK

Android Studio安装(需翻墙)

### AndroidDevTools 安装

[下载地址](http://www.androiddevtools.cn/)

![tu](https://skyJinc.github.io/images/NDK开发/1.png)

![tu](https://skyJinc.github.io/images/NDK开发/2.png)

 - 下载完成后，将NDK解压到某个文件夹下，打开Android Studio，选择File->Project Structure

![tu](https://skyJinc.github.io/images/NDK开发/3.png)

## 开发

新建一个项目，这里我使用[SkyNDK](https://github.com/skyJinc/SkyNDK)项目来说明

### gradle.properties 配置

	android.useDeprecatedNdk=true
	
### local.properties中加入ndk和sdk

	ndk.dir=/Users/sky/dev/sdk/ndk-bundle
	sdk.dir=/Users/sky/dev/sdk
	
	当然这里使用的是我的本地的路径， 这个配置AS都会自动生成，如果没有生成，就手动添加吧
	
### app文件夹下的build.gradle中的defaultConfig里加入如下代码

	ndk{    
		moduleName "sky"       //生成的so文件名字，调用C程序的代码中会用到该名字
		abiFilters "armeabi", "armeabi-v7a", "x86" //输出指定三种平台下的so库
	}
SkyNDK
![tu](https://skyJinc.github.io/images/NDK开发/4.png)


### 创建布局 编写代码

![tu](https://skyJinc.github.io/images/NDK开发/5.png)

![tu](https://skyJinc.github.io/images/NDK开发/6.png)

 - 直接引用将来生成的 ***skyNDK.so*** 
 - 声明将来要生成的本地方法 ***public native String getSkyNDKText();***


### 生成.h头文件

进入Android Studio底部的Terminal 执行代码

	cd app/src/main/java
	javah -jni 包名+类名
	
	说明： 
	第一行 是要进入到你的项目java目录下
	第二行 SkyNDK项目 完整命令
	javah -jni sky.skyndk.MainActivity

执行完以后会生成 如下：

![tu](https://skyJinc.github.io/images/NDK开发/7.png)

### 新建JNI文件夹

![tu](https://skyJinc.github.io/images/NDK开发/8.png)

![tu](https://skyJinc.github.io/images/NDK开发/9.png)

 - 把生成的 ***.h*** 头文件移动到 该目录下

![tu](https://skyJinc.github.io/images/NDK开发/10.png)

### 新建 ***.c*** 文件 

![tu](https://skyJinc.github.io/images/NDK开发/11.png)

![tu](https://skyJinc.github.io/images/NDK开发/12.png)

### 写C代码

	//引入上面生成的头文件，并实现头文件中声明的方法
	#include "sky_skyndk_MainActivity.h"
	JNIEXPORT jstring JNICALL Java_sky_skyndk_MainActivity_getSkyNDKText
	        (JNIEnv *env, jobject obj){
	    char *str="我是skyJC, 这里是C语言返回的内容";
	    return (*env)->NewStringUTF(env, str);
	}

![tu](https://skyJinc.github.io/images/NDK开发/13.png)


### 执行
	AndroidStudio
	
	Build->Clean Project
	Build->ReBuild Project

![tu](https://skyJinc.github.io/images/NDK开发/14.png)

 - 生成ndk

![tu](https://skyJinc.github.io/images/NDK开发/15.png)


## 说明

这里只是简单的教你如何快速的编写，因为我们是预先定义.so和 本地方法名，通过命令生成头文件。。

C++ 教程 网上很多.. 很多的时候核心业务都需要使用C++ 来提高效率和安全性

有时间我会写一篇关于C++开发的文章

[SkyNDK Demo](https://github.com/skyJinc/SkyNDK)
