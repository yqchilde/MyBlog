---
title: "解决PHP5.6版本“No input file specified”"
subtitle: ""
date: 2019-04-23T14:13:16+00:00
lastmod: 2019-04-23T14:13:16+00:00
author: ""
authorLink: ""
description: "使用TP框架做项目时，在启用REWRITE的伪静态功能的时候，首页可以访问，但是访问其它页面的时候，就提示：“No input file specified.”"

tags: ["PHP","ThinkPHP"]
categories: ["PHP"]

hiddenFromHomePage: false
hiddenFromSearch: false

featuredImage: ""
featuredImagePreview: ""

toc:
  enable: true
math:
  enable: false
lightgallery: false
license: ""
---
<!--more-->

### 搬砖 

> 使用TP框架做项目时，在启用REWRITE的伪静态功能的时候，首页可以访问，但是访问其它页面的时候，就提示：“No input file specified.”

### 原因

原因在于使用的PHP5.6是fast_cgi模式，而在某些情况下，不能正确识别path_info所造成的错误，“No input file specified.”，是没有得到有效的文件路径造成的。

### 解决办法如下

`在 public/.htaccess中修改`加一个 “？”号

![mark](https://pic.yqqy.top/blog/20200111/5DPOlNW9fp45.png?imageMogr2/format/webp/interlace/1)