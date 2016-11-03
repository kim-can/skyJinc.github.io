---
title: AndroidStudio代码检查
date: 2016-09-24 16:12:24
tags: 代码检查
category: Android
---

团队成员之间既有新成员又有老成员，既有大神也有菜鸟，为了提高产品代码质量，所以团队成员在产品迭代周期中固定时间进行代码Review有利于提高全队成员对产品代码的理解与个人能力的提高。

通过代码Review，团队成员对产品的每个模块都有了认识，对App上出现的错误信息立即反馈与修复

<!-- more -->

## 代码review

前提条件是有充足的开发时间, 嘿嘿 开发时间是否充足也是根据你的能力而定...

代码review什么时候进行呢？ 每个迭代周期编码结束之后测试之前，团队成员之间相互查看对方的代码，不一定是逐行逐句的查看，主要是对核心方法，编程思路....

### 代码review好处

 - 通过代码Review可以提高产品代码的质量

 - 通过代码Review可以增强团队成员之间的沟通

 - 通过代码Review能够有效的提前发现代码中存在的缺陷与BUG，降低线上出现事故的概率

 - 通过代码Review可以提高团队成员的编程能力，不同成员之间对功能设计思路的重构可以很好的提高团队成员的个人专业技能

### 流程

团队内部每周必须要有固定的时间进行代码review,每次review时间大概半个小时至一个小时之间

 - 每个人介绍各自的功能需求，实现的主要逻辑，核心代码等；

 - 团队成员提出问题，其他实现思路等；

 - 讨论不同实现思路的方式以及优劣势；

## AS lint代码检查

android studio默认已经提供了强大的lint检查工具，通过其我们可以很方便的发现代码中存在的问题，修正可能出现的bug等。


### 操作

执行Android studio –> Analyze –> Inspect code操作，打开代码检查框 

![tu](https://skyJinc.github.io/images/androidstudio代码检查/1.png)


![tu](https://skyJinc.github.io/images/androidstudio代码检查/2.png)


![tu](https://skyJinc.github.io/images/androidstudio代码检查/3.png)

![tu](https://skyJinc.github.io/images/androidstudio代码检查/4.png)

这里 我在XML 直接使用了 “按钮” 这个值，让你把字符串定义在@string里 我们修改后

![tu](https://skyJinc.github.io/images/androidstudio代码检查/5.png)

执行 

![tu](https://skyJinc.github.io/images/androidstudio代码检查/6.png)

刚才的检查提示没有了。。在项目开发完毕后，记得检查一下...

### AS 强制规范化

![tu](https://skyJinc.github.io/images/androidstudio代码检查/7.png)

这样写的时候,AS并没有为我们提示什么错误信息,我们可以更改lint检查提示,让其一旦检测到有布局文件硬编码的情况就报错

![tu](https://skyJinc.github.io/images/androidstudio代码检查/8.png)

查看

![tu](https://skyJinc.github.io/images/androidstudio代码检查/9.png)

## 条件检查

 - 根据名称扫描检查

![tu](https://skyJinc.github.io/images/androidstudio代码检查/10.png)

 - 输入unused未使用的里面有很多项可查 

![tu](https://skyJinc.github.io/images/androidstudio代码检查/11.png)
 
未使用的 id,import, library,大家可以根据自己的需求去检查,这里我们选中 resources 弹出如下对话框

![tu](https://skyJinc.github.io/images/androidstudio代码检查/2.png)

## 说明

当你成为一个团队的leader 我说的是技术型，不是管理型 ...

帮助大家养成一种习惯，当这种习惯养成以后，你会发现你的团队的变化。

就算你不在这个团队里，这个习惯会持续下去...


