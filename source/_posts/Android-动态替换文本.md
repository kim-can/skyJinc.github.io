---
title: Android 动态替换文本
date: 2016-10-24 22:45:18
tags: 资源替换
category: Android
---

需求是: 用5分钟的时间写一个简单的Android的string.xml替换文本教程...

问题：如果要把 5 和 Android 动态替换 如何实现？

<!-- more -->

## string.xml

```string.xml
<string name="demo">用%1$d分钟的时间写一个简单的%2$s的string.xml替换文本教程...</string>
```

## 使用
```
String input = getResources().getString(R.string.demo);
String ouput = String.format(input, 5,"Android");
```
## 说明
```说明
%1$d：1是指位置1，小写d是指定替换类型为整数。
%2$s：2是指位置2，小写s是指定替换类型为字符串。
```

## 结尾

简单的也要记录, 省的自己忘记