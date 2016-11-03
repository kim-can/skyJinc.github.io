---
title: Android-CodeReview
date: 2016-10-31 15:08:34
tags: review代码
category: Android
---

首先需要知道什么是Code Review和我们使用它的目的是什么。

Code Review是一种用来确认方案设计和代码实现的质量保证机制，通过这个机制我们可以对代码，测试过程和注释进行检查。

Code Review主要用来在软件工程过程中改进代码质量，通过Code Review可以达到如下目的：

 - 在项目早期就能够发现代码中的BUG

 - 帮助初级开发人员学习高级开发人员的经验，达到知识共享

<!-- more -->

## 前言

找了一段时间， 一直没发现好的软件，来进行code review。

经人介绍，Upsource 使用以后 果然不错..操作简单..

主要是这个安装和使用，这个软件的文章太少,写一个比较常用的使用教程 和 安装步骤

## Upsource 安装

[下载地址](https://www.jetbrains.com/upsource/download/)

 - 下载后解压， 里面有一个文件 `README.txt`


```file
Running Upsource
================

# Usage

## Windows 系统
Run `<home>\bin\upsource.bat start` from the command line to start Upsource.

On first start the welcome page will open in the default browser.

## Linux 和 mac 系统

Run `<home>/bin/upsource.sh start` from the command line to start Upsource.
Run `<home>/bin/upsource.sh stop` to stop it.

# Default credentials

Login: admin
Password: admin

# Installation and administration guide

https://www.jetbrains.com/help/upsource/3.5
```


 - 执行 `<home>\bin\upsource.bat start` 后会自动弹出初始化页面


![tu](https://skyJinc.github.io/images/codereview/1.png)
![tu](https://skyJinc.github.io/images/codereview/2.png)
![tu](https://skyJinc.github.io/images/codereview/3.png)

## Upsource 使用

 - Hub 用户和权限管理 等等..

![tu](https://skyJinc.github.io/images/codereview/4.png)

 - Users 用户管理
 - Groups 权限分组
 - Roles 权限管理

![tu](https://skyJinc.github.io/images/codereview/5.png)


 - Upsource 项目管理

![tu](https://skyJinc.github.io/images/codereview/6.png) 

![tu](https://skyJinc.github.io/images/codereview/7.png)

注:点进去可以看到你的项目 所有git提交的代码, 这个时候就可以进行每一次提交是否审核了..

 - 一般我们都是切出来分支开发...所以切出来分支以后 对这个分支进行全部codereview

![tu](https://skyJinc.github.io/images/codereview/8.png)

 - 选择一个分支

![tu](https://skyJinc.github.io/images/codereview/9.png)

 - 创建分支review

![tu](https://skyJinc.github.io/images/codereview/10.png)

 - 你会发现所有的提交都是等待review状态

![tu](https://skyJinc.github.io/images/codereview/12.png)

 - 指定 review 的人, 当然也可以通过 分组权限来设置 默认review的人
 - Accept 通过
 - Raise concern  不通过

![tu](https://skyJinc.github.io/images/codereview/11.png)

 - 可以给代码里写留言，这个不会提交到真正的代码库里
 - 也可以给这次git提交进行留言, 来互动

![tu](https://skyJinc.github.io/images/codereview/13.png)

 - 当分支 都是 `review completed` 状态的时候 可以合并到master上了..如果不是，继续修改吧....

![tu](https://skyJinc.github.io/images/codereview/14.png)


## 修改地址

想局域网内的小伙伴们，都能够访问你搭建的服务器，需要修改地址才可以

 - 我的本地端口号 `8080`

 
 - 我的本地IP地址 `10.71.172.215`

 
 - 执行以下命令


```
1. <home>/bin/upsource.sh stop
2. <home>/bin/upsource.sh configure --listen-port 8080 --base-url http://10.71.172.215:8080
3. <home>/bin/upsource.sh start
```

## 结尾

只是简单的说明了使用方式,里面还有很多好玩的,可以自己去探索吧。。