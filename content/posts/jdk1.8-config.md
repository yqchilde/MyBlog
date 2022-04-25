---
title: "JDK1.8的环境变量配置"
subtitle: ""
date: 2019-07-24T14:34:15+00:00
lastmod: 2019-07-24T14:34:15+00:00
author: ""
authorLink: ""
description: "本来是配置好的环境，但是一直没发现在控制台，只有java可以用，javac却不能用"

tags: ["Java"]
categories: ["Android"]

hiddenFromHomePage: false
hiddenFromSearch: false

featuredImage: ""
featuredImagePreview: ""

toc:
  enable: false
math:
  enable: false
lightgallery: false
license: ""
---
<!--more-->

> 本来是配置好的环境，但是一直没发现在控制台，只有java可以用，javac却不能用

### 1.5以后不再需要JAVA_HOME和CLASSPATH

直接在系统环境变量新增jdk的绝对路径就行，如图

![mark](https://pic.yqqy.top/blog/20200111/xmPCAwSdtEKH.png)

成功如图：

![mark](https://pic.yqqy.top/blog/20200111/1IX3vtwq39BI.png)

### 1.5以前仍然要配置JAVA_HOME和CLASSPATH

**JAVA_HOME:** 你自己的jdk的路径

CLASSPATH=.;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar

**注意前面有一个.千万不要忘了。**
（注意 如果是1.5以后的JDK版本不需要配置CLASSPATH）