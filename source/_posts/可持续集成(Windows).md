---
title: Windows + Jenkins + Gradle + Git = 可持续集成架构搭建
date: 2016-10-13 16:21:47
tags: 打包方式
category: Android
---
简化打包流程...

<!-- more -->



### 前言

因为我的电脑是mac，正好公司里有windows服务器，远程连接一下，进行配置

系统: windows 7 , 64位

### JDK 安装

可执行文件 直接安装就好了。。

[下载地址](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)

![tu](https://skyJinc.github.io/images/jenkins-win/3.png)

#### 环境变量

	1，新建 JAVA_HOME，变量值：
	D:\Java\jdk1.8.0_101
	
	2，打开PATH，添加变量值：
	%JAVA_HOME%\bin;%JAVA_HOME%\jre\bin
	
	3，新建变量名：CLASSPATH，变量值：
	.;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar

#### 测试环境变量是否成功

开始->搜索程序和文件->输入 cmd

![tu](https://skyJinc.github.io/images/jenkins-win/5.png)

	执行 java -version

![tu](https://skyJinc.github.io/images/jenkins-win/4.png)

### Tomact

[下载地址](http://tomcat.apache.org/)

Tomcat9

![tu](https://skyJinc.github.io/images/jenkins-win/1.png)

我放到了D盘，解压后，记住路径要配置环境变量

	路径 D:\tomcat9\apache-tomcat-9.0.0.M11

![tu](https://skyJinc.github.io/images/jenkins-win/2.png)

#### 环境变量

	1，新建变量名：TOMCAT_HOME，变量值：
	D:\tomcat9\apache-tomcat-9.0.0.M11
	
	3，打开PATH，添加变量值：
	%TOMCAT_HOME%\lib;%TOMCAT_HOME%\bin

#### 测试是否启动正确
开始->搜索程序和文件->输入 cmd

![tu](https://skyJinc.github.io/images/jenkins-win/5.png)

	cd D:\tomcat9\apache-tomcat-9.0.0.M11
	启动 startup
	
	
	//关闭 shutdown
	
[查看地址](http://localhost:8080)

### Jenkins安装

[下载地址](http://mirrors.jenkins-ci.org/war/latest/jenkins.war)

将下载的jenkins.war包直接放到tomcat下的webapps目录，启动tomcat即可安装完成

[查看Jenins](http://localhost:8080/jenkins/)

如果显示一下内容
![图1](https://skyJinc.github.io/images/jenkins/1.png)

这里红色部分地址不打一样，直接进入到红色地址里，用记事本打开，复制内容到输入框里就可以了～～

### Jenkins设置
#### SDK
[SDK下载地址](https://developer.android.com/studio/index.html)

直接下载SDKManage就好

![tu](https://skyJinc.github.io/images/jenkins-win/6.png)

下载安装以后，对应的android所需的下载一下
![tu](https://skyJinc.github.io/images/jenkins-win/7.png)

设置SDK

![tu](https://skyJinc.github.io/images/jenkins-win/8.png)

#### JDK	

[下载地址](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)

JDK地址是本地安装好的地址

系统管理->Global Tool Configuration

![tu](https://skyJinc.github.io/images/jenkins-win/9.png)

#### GIT

[下载地址](https://git-scm.com/downloads)

##### 配置环境变量

	1，打开PATH，添加变量值：
	D:\Program Files\Git\cmd
	
##### 测试环境变量是否成功

	git --version

![tu](https://skyJinc.github.io/images/jenkins-win/10.png)

##### 设置
可以让插件帮你下载，也可以手动配置

![tu](https://skyJinc.github.io/images/jenkins-win/11.png)

#### Gradle
我这里是让插件帮我去下载了，手动配置 自行解决吧，很好配置或者看mac版是怎么配置的

![tu](https://skyJinc.github.io/images/jenkins-win/12.png)

#### Python打多渠道

[下载地址](https://www.python.org/)

##### 配置环境变量

地址是我安装python的位置，自己的位置自行替换

	1，打开PATH，添加变量值：
	D:\Python27

##### 测试环境变量是否成功

	python
	
![tu](https://skyJinc.github.io/images/jenkins-win/14.png)
	
### 开始构建
#### 1.新建
![图](https://skyJinc.github.io/images/jenkins/8.png)
#### 2.设置参数
![图](https://skyJinc.github.io/images/jenkins/9.png)
#### 3.设置源码
说明：这里我主要是用了github和SSH密钥，如果使用svn 或者 git 账号和密码方式，自行增加，不会的留言提问哈。。
![图](https://skyJinc.github.io/images/jenkins/10.png)
#### 4.设置源码
![图](https://skyJinc.github.io/images/jenkins/11.png)
#### 5.输出内容过滤
![图](https://skyJinc.github.io/images/jenkins/12.png)
#### 6.开始构建
![图1](https://skyJinc.github.io/images/jenkins/13.png)
#### 7.查看构建内容
![图1](https://skyJinc.github.io/images/jenkins/14.png)

### 结尾

windows教程 和 mac教程有了， 有时间写个linux的～


[MAC教程](http://www.jincanshen.com/2016/08/22/%E5%8F%AF%E6%8C%81%E7%BB%AD%E9%9B%86%E6%88%90/)

[多渠道打包](http://www.jincanshen.com/2016/09/10/%E5%A4%9A%E6%B8%A0%E9%81%93%E6%89%93%E5%8C%85%E6%96%B9%E5%BC%8F/)


