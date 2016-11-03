---
title: 'Android:Gradle根据变量打包Apk'
date: 2016-09-01 19:43:36
tags: 打包方式
category: Android
---
真正开发项目的时候，我们需要打不同环境的APK包。

例如,测试包，开发包，发布包，等等...

这些包对应的http环境有可能不同，这个时候 我们可能在想 如果有以下要求生成APK包就好了。。。

<!-- more -->

	1. 构建不同环境的APK
	2. 不需要修改代码
	3. 可以选择不同的签名

通过buildConfigField就能解决以上的问题

## Gradle - buildConfigField
***buildConfigField***是gradle的一个方法，他的作用是可以给BuildConfig添加变量，赋值等。他的使用方式:

	buildConfigField("int", "SKY", "0")
	
这样就给BuildConfig中新增加了一个属性EN_SETTING:
	
	public static final int SKY = 0;
	
在代码中可以直接使用:

	BuildConfig.SKY
	
***buildConfigField***通过***BuildConfig***类可以做到,把gradle里的配置传递给Java代码。

### BuildTypes
虽然通过gradle中可以配置ENV_SETTING变量，通过buildConfigField可以让java代码获取到设置的值。但是在buildTypes中我们只能设置两个变量:

	buildTypes {
        release {
            signingConfig signingConfigs.release
            buildConfigField("int", "SKY", "1")
        }
        debug {
            buildConfigField("int", "SKY", "0")
        }
    }
   

也就是buildTypes中只有两种配置，在build的时候只能有两个APK:debug和release，这就需要我们重新定义一种新的buildTypes，用来构建测试环境的APK.
我们新增加一种buildTypes：

	buildTypes {
        release {
            signingConfig signingConfigs.release
            buildConfigField("int", "SKY", "1")
        }
        debug {
            buildConfigField("int", "SKY", "0")
        }
        
        releaseTest.initWith(buildTypes.release)
        
        releaseTest {
            buildConfigField("int", "SKY", "2")
        }
    }
    
这样就有了三种不通的build配置

### Build
gradle 命令 进行打包 

	assembleDebug
	assembleRelease
	assembleTeleaseTest
	
这样我们就可以根据需要构建不同的APK,不需要改代码就可以构建不同环境的APK,而且生成的APK的名字也能区分出来不同的环境：

	app-debug.apk
	app-release.apk
	app-releaseTest.apk
	
也可以根据需要设置不同的APK签名。

## 例子 - SkyApp项目
[项目地址](https://github.com/skyJinc/SkyApp)

app.gradle 配置

![tu](https://skyJinc.github.io/images/gradle打包/1.png)

区分不同环境 对应不同的地址～ 和 区分不同环境 打印日志开关

![tu](https://skyJinc.github.io/images/gradle打包/2.png)

	gradle assembleRelease
	
	打包后点击登陆按钮....
	
![tu](https://skyJinc.github.io/images/gradle打包/3.png)

