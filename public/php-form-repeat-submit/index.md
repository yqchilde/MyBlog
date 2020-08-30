# PHP防止表单重复提交策略

<!--more-->

## 描述

在开发过程中，经常会出现表单出错而返回页面的时候填写的信息全部丢失的情况。正好碰到帮别人写表单多次提交之后，返回时候提示确认重新提交表单，如图

![mark](https://pic.yqqy.top/blog/20200111/EVfKpdQI2Fcg.png?imageMogr2/format/webp/interlace/1 "问题")

## 方案

解决办法一句代码，为了支持页面回跳。

`header('Cache-control: private, must-revalidate');`

## 原理

提交之后立马清除缓存控制

## 相关缓存指令

![mark](https://pic.yqqy.top/blog/20200111/kfpwwGrVl50x.png?imageMogr2/format/webp/interlace/1 "指令")

## 搬砖地址

原文有8种防止重复提交表单机制，我遇到一种，故介绍一种，了解其他请查看原文

[https://www.cnblogs.com/myon/p/6636934.html](https://www.cnblogs.com/myon/p/6636934.html)
