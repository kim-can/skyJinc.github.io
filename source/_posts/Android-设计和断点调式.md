---
title: Android 设计和断点调式
date: 2016-09-08 09:45:57
tags: 调试
category: Android
---

一个App其实设计很重要，设计好了 看起来不是很low...

我不懂设计，可是我懂怎么去找资源， 我不会搭配颜色，那我就找专业的给我配色，没有设计给我图标，我自己找图标。


<!-- more -->

为什么主要写这篇文章..　我也犹豫了 很久... 

而且把设计和开发时用到的知识，合并到了一起...

进入主题吧。。


# 设计

## 配色

### [adobe color](https://color.adobe.com/zh/explore/newest/)

 - Adobe 公司为广大设计者，提供的配色大全，哈哈。如果你有设计的基础，或者想自己去调一些颜色，那这个绝对是你的最爱，涵盖了众多颜色，同时还可以自己自定义。譬如说，去调整色域，透明度等等，应有尽有。

![tu](https://skyJinc.github.io/images/dd设计和调式/1.png)

### [colordrop](https://colordrop.io/)

 - 颜色也特别符合 Material Design,可以选择 HEX 和 RGB 两个颜色格式，不用花心思去找，因为每一种颜色都特别好看。

![tu](https://skyJinc.github.io/images/dd设计和调式/2.png)

### [materialpalette](https://www.materialpalette.com/indigo/light-green)

 - 颜色较少，但是基本常用的都涵盖了，而且可以直观的看到搭配
 
![tu](https://skyJinc.github.io/images/dd设计和调式/3.png)

## 图标
### [iconfont](http://www.iconfont.cn/collections?personal=1)

 - 阿里的公开图标库

![tu](https://skyJinc.github.io/images/dd设计和调式/4.png)

### [icon8](https://icons8.com/web-app/new-icons/android)

 - 涵盖了 Android ，iOS , Windows 众多平台的图标，提供多种类型格式下载。

![tu](https://skyJinc.github.io/images/dd设计和调式/5.png)

# 断点调试
Bug是不可避免的，在这种情况下，除了日志外最常用的就是Debug了。除了写程序，当我们接手一个旧的程序，熟悉代码最常用的手段也是调试

## AS功能键

![tu](https://skyJinc.github.io/images/dd设计和调式/6.png)

1. step over：点击该图标程序执行下一行，如果是调用方法，这个方法会被直接执行不会进入该方法内部。
2. step into：点击该图标，如果当前代码是自定义的方法，会进入方法内部逐步执行，如果是官方库的方法，不会进入方法内部，如果不是将执行下一行
3. force step into：点击该图标会进入方法内部，不论是自定义方法还是官方库方法。会使你脱离当前断点，从你选择的方法开始调试。
4. step out：点击该图标会快速运行完该方法，跳出当前执行的方法内部，执行到该方法调用的下一句代码。
5. drop frame：点击该图标会回到调用该方法的开始处，恢复原始值，可以重新运行该方法。
6. run to cursor：点击该图标会使程序跳转到下一个断点处，如果设置多个断点逐句运行会比较麻烦，可以通过这个功能快速跳转到下一个断点。
7. 调试功能键介绍完到求值表达式功能键区，求值表达式功能的作用是：当程序运行到某一个断点的时候，恰好你对该处的某对象的某些属性很感兴趣，那么可以通过该按钮跳出。点击该图标会跳出一个Evaluate expression窗口，我们可以在这个窗口中输入感兴趣的表达式，然后点击evaluate按钮，你就可以得到想要知道的结果了
![tu](https://skyJinc.github.io/images/dd设计和调式/7.png)


## AS还提供其他的断点调试方法

### 条件断点调试

当我们设置的断点在一个循环里面，我们只对循环中某个值得时候有兴趣，逐步调试会很麻烦，这个时候条件断点调试会极大的提高我们的效率。我们可以设置一个条件，只有当循环满足我们的条件的时候，循环才会停下来，例如：

![tu](https://skyJinc.github.io/images/dd设计和调式/8.png)

![tu](https://skyJinc.github.io/images/dd设计和调式/9.png)

### 日志断点

日志断点严格来说并不是断点调试，它不会在你打断点的地方停下来，它只是让你在需要的地方输出日志而已。例如：

![tu](https://skyJinc.github.io/images/dd设计和调式/10.png)

### 方法断点

当我们只对方法执行的结果或者返回值感兴趣的时候，方法断点是一个不错的选择。只需要在方法的第一行打上断点即可，断点的图标也会不一样。

![tu](https://skyJinc.github.io/images/dd设计和调式/11.png)

### 异常断点

如果你对程序发生的异常感兴趣，Exception Breakpoint是非常不错的选择，当程序运行发生异常时，直接让程序停下来，保留事故发生的现场，你可以即使有效的发现问题的原因以及异常发生的位置。

![tu](https://skyJinc.github.io/images/dd设计和调式/12.png)

![tu](https://skyJinc.github.io/images/dd设计和调式/13.png)

![tu](https://skyJinc.github.io/images/dd设计和调式/14.png)

![tu](https://skyJinc.github.io/images/dd设计和调式/15.png)


### 设置变量的值

在调试过程中，你发现某个变量的值跟你预期的结果不一样，或者你怀疑某变量这出现某些特殊值时程序会发生崩溃，而当前测试环境又极难模拟这种情况，那么你可以在调试模式时，如图所示，在变量区右键你想监控的变量，设置你想要出现的值，然后继续运行。

![tu](https://skyJinc.github.io/images/dd设计和调式/16.png)


## 说明
开发的时候，其实我们经常用的也就是调式功能键了。。不过能了解后，在不同的场景能用到，方便的排查问题。。

很基础的东西... 也为自己记录下来。。。。
