---
title: "Git设置代理解决git clone太慢的问题"
subtitle: ""
date: 2020-02-01T12:33:03+00:00
lastmod: 2020-02-01T12:33:03+00:00
author: ""
authorLink: ""
description: "可以不要引号，实测也可以，反而加了引号有些网站不能访问，不需要设置 https.proxy ，仅设置 http.proxy就可以了， 端口需要看本地的socks端口，不是固定"

tags: ["Git"]
categories: ["Tips"]

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

### 设置代理

```shell
git config --global http.proxy socks5://domain:port

// 栗子:
git config --global http.proxy socks5://127.0.0.1:2081
```

<font color="#F56C6C">注意点：</font>

1. 可以不要引号，实测也可以，反而加了引号有些网站不能访问
2. 不需要设置 https.proxy ，仅设置 http.proxy就可以了
3. 端口需要看本地的socks端口，不是固定

![mark](https://pic.yqqy.top/blog/20200201/efIkpyjqj5Ju.png?imageMogr2/format/webp/interlace/1)

![mark](https://pic.yqqy.top/blog/20200201/GC449AV9djFA.png?imageMogr2/format/webp/interlace/1)

### 取消代理

```shell
git config --global --unset http.proxy
```

查看代理

```shell
git config --global --get http.proxy
```