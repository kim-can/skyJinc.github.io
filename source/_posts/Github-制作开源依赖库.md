---
title: Github-制作开源依赖库
date: 2016-09-01 20:13:50
tags: 打包方式
category: Android
---

Github给大家依赖使用,如果是正常的流程，可能很繁琐。但是使用JitPack，只需要几个傻瓜式的操作即可

使用Gradle依赖别人项目有很多类似

![tu](https://skyJinc.github.io/images/github制作依赖/1.png)

<!-- more -->
## 新建一个开源库

以[sky架构](https://github.com/skyJinc/sky)为例子

![tu](https://skyJinc.github.io/images/github制作依赖/2.png)

提交到github

## Github 操作
### 第一步
![tu](https://skyJinc.github.io/images/github制作依赖/3.png)
### 第二步
![tu](https://skyJinc.github.io/images/github制作依赖/4.png)
### 第三步
![tu](https://skyJinc.github.io/images/github制作依赖/5.png)
### 第四步
![tu](https://skyJinc.github.io/images/github制作依赖/6.png)

## JitPack操作
[查看地址](https://jitpack.io/)
### 第一步
![tu](https://skyJinc.github.io/images/github制作依赖/7.png)
### 第二步
![tu](https://skyJinc.github.io/images/github制作依赖/8.png)
### 第三步
![tu](https://skyJinc.github.io/images/github制作依赖/9.png)
### 第四步
![tu](https://skyJinc.github.io/images/github制作依赖/10.png)


直接在项目中依赖就可以了～～～

## Gradle问题

因为sky架构使用了，我自己本地的gradle2.14版本，没有把gradle配置到项目里～JitPack会打包错误。

如果有人像我一样习惯使用本地gradle版本，需要配置gradle告诉JitPack我的Gradle版本。。。。

创建 ***jitpack.yml*** 文件

内容

	language: android
	jdk:
	  - oraclejdk8
	
	before_install:
	  - wget http://services.gradle.org/distributions/gradle-2.14-bin.zip
	  - unzip gradle-2.14-bin.zip
	  - export GRADLE_HOME=$PWD/gradle-2.14
	  - export PATH=$GRADLE_HOME/bin:$PATH

