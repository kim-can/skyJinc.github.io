---
title: Android发布项目到中央仓库
date: 2016-10-25 16:46:26
tags: 打包方式
category: Android
---
本文将通过[Sky架构](https://github.com/skyJinc/sky)来演示如何将 AAR 包 发布到中央仓库，引用方式官方化...

<!-- more -->

## 注册JIRA

[地址](https://issues.sonatype.org/)

 - 点击右上角的 Login ，如果没有在登陆页面可以点击 Sign Up 进入到注册页面

![tu](https://skyJinc.github.io/images/centermaven/1.png)

 - 按照参考自己写信息

![tu](https://skyJinc.github.io/images/centermaven/2.png)

### 创建项目

![tu](https://skyJinc.github.io/images/centermaven/3.png)

 - Project 项目就按照我选择的进行选择，该类型是专门用于发布开源库的类型；
 - Issue Type 这个点击一下选择 New Project
 - Summary 就在于你取个名字了，最好取和你的 GitHub 项目相同的名字
 - Desciription 教程
 - Attachment  如果你有个人域名，就要上传域名证书，没有的话可以空着
 - Group Id 有一定[规则](http://central.sonatype.org/pages/choosing-your-coordinates.html)

![tu](https://skyJinc.github.io/images/centermaven/4.png)

 - Project URL 是你的Github项目地址
 - SCM url  是你的github的http - git 地址

![tu](https://skyJinc.github.io/images/centermaven/6.png)

 - UserName 看着起吧

![tu](https://skyJinc.github.io/images/centermaven/5.png)

 - OK 创建就可以了。。。需要等待审核 ，因为我第一次Group Id 写错了，给我拒绝了，重新提交了一下，估计得等几天咯。。。

![tu](https://skyJinc.github.io/images/centermaven/7.png)

 -  修改以后提交 等待 审核结束吧。。。 下面这个状态就可以了

![tu](https://skyJinc.github.io/images/centermaven/8.png)

 - 给你以下这些信息 证明你已经成功了

![tu](https://skyJinc.github.io/images/centermaven/9.png)

## 生成秘钥

 - 我这里使用的是Mac笔记本 所以下了OS X系统的 MAC GPG

[下载地址](https://www.gnupg.org/download/index.html)

 -  创建秘钥并上传秘钥

![tu](https://skyJinc.github.io/images/centermaven/10.png)

说明: 创建后 记住keyID 和 密码 以后要用

## 编写Gradle

### 第一步 [gradle.properties](https://github.com/skyJinc/sky/blob/master/gradle.properties) 设置参数
```file
#最小版本
MIN_SDK_VERSION=11
#编译版本
COMPILE_SDK_VERSION=24
BUILD_TOOLS_VERSION=24.0.0
#suppor版本
SUPPORT_LIBRARY_VERSION=24.0.0
#guava版本
GUAVA_VERSION=19.0
#版本控制
VERSION_NAME=2.2.4
VERSION_CODE=26
# ------  中央仓库配置 -- 这要是这里 上面的是为了修改方便我写的
GROUP=com.jincanshen
POM_DESCRIPTION=Android Library
POM_URL=https://github.com/skyJinc/sky
POM_SCM_URL=https://github.com/skyJinc/sky
POM_SCM_CONNECTION=scm:https://github.com/skyJinc/sky.git
POM_SCM_DEV_CONNECTION=scm:https://github.com/skyJinc/sky.git
POM_LICENCE_NAME=The Apache Software License, Version 2.0
POM_LICENCE_URL=http://www.apache.org/licenses/LICENSE-2.0.txt
POM_LICENCE_DIST=repo
POM_DEVELOPER_ID=skyJinc
POM_DEVELOPER_NAME=Jin Can
SNAPSHOT_REPOSITORY_URL=https://oss.sonatype.org/content/repositories/snapshots
RELEASE_REPOSITORY_URL=https://oss.sonatype.org/service/local/staging/deploy/maven2
```

 - GROUP 这个很重要 唯一标记
 - POM_DESCRIPTION 介绍
 - POM_URL 你的项目地址
 - POM_SCM_URL scm 地址 跟项目地址一样
 - POM_SCM_CONNECTION git地址
 - POM_SCM_DEV_CONNECTION git地址
 - POM_LICENCE_NAME 许可介绍
 - POM_LICENCE_URL 许可地址
 - POM_LICENCE_DIST 许可
 - POM_DEVELOPER_ID 用户ID  一般都是github 账号名
 - POM_DEVELOPER_NAME 真实姓名
 - SNAPSHOT_REPOSITORY_URL maven仓库地址 基本上大家都一样
 - RELEASE_REPOSITORY_URL maven仓库地址 基本上大家都一样

### 第二步 [app/gradle.properties](https://github.com/skyJinc/sky/blob/master/app/gradle.properties) 设置参数

```file
POM_NAME=AndroidSkyLibrary
POM_ARTIFACT_ID=sky
POM_PACKAGING=aar
```

 - POM_NAME 描述一下
 - POM_ARTIFACT_ID 这个挺重要的 起一个好一点的
 - POM_PACKAGING 生成包类型

### 第三步 把生成的秘钥信息填充到 ~/.gradle/gradle.properties

 - 如果自己的.garadle里没有这个文件 就自己创建一个

![tu](https://skyJinc.github.io/images/centermaven/11.png)

 - SKY_USERNAME 是你在JIRA中注册的账号名
 - SKY_PASSWORD 是你在JIRA中注册的密码
 - 上面两个值要跟 [maven-center-aar.gradle](https://github.com/skyJinc/sky/blob/master/app/maven-center-aar.gradle) 里的 getRepositoryUsername（） 和 getRepositoryPassword() key对应，当然完全可以自定义
 - signing.keyId 上面生成秘钥的key
 - signing.password 上面生成秘钥的密码
 - signing.secretKeyRingFile 秘钥文件的路径 找到secring.gpg的完整路径写上就好

### 第四部 引用和发布到临时库

 - 引用

[app/build.gradle](https://github.com/skyJinc/sky/blob/master/app/build.gradle)

```gradle
apply from: 'maven-center-aar.gradle'
```

 -  执行


```gradle
gradle uploadArchives
```

 - 提示

![tu](https://skyJinc.github.io/images/centermaven/12.png)

当提示这个的时候，恭喜你，你的项目已经提交到临时库了。跟着我继续配置

### 第五步 跟JIRA管理员申请同步到maven中央库

 - 进入管理中心,登录

[管理地址](https://oss.sonatype.org/#stagingRepositories)

 - 左侧 Staging Repositories 进入后 在最后 有你的临时项目名称

![tu](https://skyJinc.github.io/images/centermaven/13.png)

 - 关闭项目

![tu](https://skyJinc.github.io/images/centermaven/14.png)

 - 关闭后，登录JIRA,并找到你的Issues 进行留言

[JIRA地址](https://issues.sonatype.org)

![tu](https://skyJinc.github.io/images/centermaven/15.png)

![tu](https://skyJinc.github.io/images/centermaven/16.png)

 - 最后JIRA管理会让你打Release 然后在次进行留言。。当然你可以直接打后留言一次就可以了。。

![tu](https://skyJinc.github.io/images/centermaven/20.png)

![tu](https://skyJinc.github.io/images/centermaven/19.png)

 - 再次进行留言 就可以了。。。

### 结尾

漫长的等待，如果通过了 会提示 

![tu](https://skyJinc.github.io/images/centermaven/17.png)

最后可以在你的Android studio 里的 maven仓库里找到你的项目了

![tu](https://skyJinc.github.io/images/centermaven/18.png)
