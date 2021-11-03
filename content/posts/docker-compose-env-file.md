---
title: "docker-compose使用env处理环境变量妙用"
subtitle: ""
date: 2021-03-13T12:49:58+08:00
lastmod: 2021-03-13T12:49:58+08:00
author: ""
authorLink: ""
description: "记录在docker-compose中对环境变量抽离，使用env文件嵌套变量使用的方法。"

tags: ["Docker"]
categories: ["Docker"]

hiddenFromHomePage: false
hiddenFromSearch: false

featuredImage: ""
featuredImagePreview: ""

toc:
  enable: true
math:
  enable: false
lightgallery: true
license: ""
---

<!--more-->







> {{indent}}最近在用的一个服务他的docker-compose需要配置很多很多的环境变量，而每个环境变量可能又很长，所以查阅资料了解到env file这种配置

## env_file

{{indent}}官方文档地址 [https://docs.docker.com/compose/environment-variables](https://docs.docker.com/compose/environment-variables)，这里记录一下我遇到的问题，如果要使用env_file配置，这时候，是会把所有的变量都带入进来的，我不需要那么多变量，举个🌰

```txt
# 京东农场
FRUIT1=0f6f68f0d58d43a98d2f578a7e1a0215
FRUIT2=8e2785b15cef4f03a714f4288a496dec
FRUIT3=c8f3876f3ea24dedbb174868ad53e4d2
FRUIT4=667ebac5844e418d98393004362eb28s
FRUIT5=dad356ebad9a4de6ab8fdd774b2ea94g


FRUIT_SHARECODE1=${FRUIT2}@${FRUIT3}@${FRUIT4}@${FRUIT5}
FRUIT_SHARECODE2=${FRUIT1}@${FRUIT3}@${FRUIT4}@${FRUIT5}
FRUIT_SHARECODE3=${FRUIT1}@${FRUIT2}@${FRUIT4}@${FRUIT5}
FRUIT_SHARECODE4=${FRUIT1}@${FRUIT2}@${FRUIT3}@${FRUIT5}
FRUIT_SHARECODE5=${FRUIT1}@${FRUIT2}@${FRUIT3}@${FRUIT4}
```

{{indent}}`.env`文件中有这么一段内容，其中 `FRUIT1-5` 和 `FRUIT_SHARECODE1-5`全是变量，但是很显然我只要想 `FRUIT_SHARECODE1-5`的变量，不想要上面的，可以说是我需要按需引入变量，如果直接用如下这种配置，就会将所有的变量全部引入

```bash
$ cat docker-compose.yml
version: '3'
services:
  api:
    image: 'node:6-alpine'
    env_file:
     - ./api.env
```

{{indent}}所以最终想法还是只在同目录用 `.env` 文件，这样不需要特别声明引入，只需要保证同目录即可，这时候我在 `docker-compose.yml` 文件中引入了 `FRUIT_SHARECODE1` 变量，按照我的理解是变量嵌套会间接引入上面的变量，结果变成了下面这个样子。

![](https://pic.yqqy.top/blog/20210313150914.png?imageMogr2/format/webp/interlace/1 "图1")

{{indent}}难道docker-compose不支持这种写法？还被多加了一个 `$`变成了 `$${}` ，随后在自己的本地电脑试了下，是按照我预想的结果输出的，那么问题就好说了，这说明是dockerc-compose版本不对，并不是不支持，当前服务器下的版本是 1.25.4，翻阅版本记录发现在 1.26.0 以后使用了 `python-dotenv` 管理了env_file，所以至少要使用大于1.26.0以后的版本。

![](https://pic.yqqy.top/blog/20210313151336.png?imageMogr2/format/webp/interlace/1 "图2")

顺便提一下更新 `docker-compose`版本步骤

```bash
$ whereis docker-compose
docker-compose: /usr/local/bin/docker-compose

# 记得后面保存的位置是原来的位置
$ curl -L https://github.com/docker/compose/releases/download/1.27.4/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
```

更新之后再看一下效果吧

![](https://pic.yqqy.top/blog/20210313151938.png?imageMogr2/format/webp/interlace/1 "图三")

## 总结

{{indent}}docker-compose很方便，使用 `.env` 文件会更加方便，这样我们可以传递更多的内容进来，每次只需要维护 `.env` 不用频繁修改 `docker-compose.yml` 。