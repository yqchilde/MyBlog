# IDM怎么设置代理配合Shadowsocks下载国外文件

<!--more-->

### 起因

{{indent}}虽说用了好久的IDM,但是还是遇到了不会的问题，IDM怎么设置代理配合Shadowsocks下载国外文件？今天老师让我帮他下载国外的文档，原因是他自己用浏览器下载总是断，又不能断线续传，那我就想到了IDM。然鹅IDM下载国内的资源都是很给力的，下载国外的资源一直是连接不成功。就是这个逼样。

![mark](https://pic.yqqy.top/blog/20200111/drQjlCvq4fw4.png?imageMogr2/format/webp/interlace/1)

### 介绍

{{indent}}Shadowsocks是属于SOCKS5代理 ，除非其他软件支持 设置SOCKS5代理 ，比如：IDM你设置SOCKS5代理 127.0.0.1 1080 ，IDM就会走Shadowsocks了，还有浏览器也是默认读取系统代理设置的。不过首先我们设置连接方式为PAC，全局也是可以，不过此方法不合适，PAC也有好处，就是在国内下载资源，会自动切换下载源。

{{indent}}**注意：** 1080不是固定，只是你所代理的IP，所以记得灵活改变

![mark](https://pic.yqqy.top/blog/20200111/CpmWxzH011WW.png?imageMogr2/format/webp/interlace/1)

### 具体设置方法如下

1. ![mark](https://pic.yqqy.top/blog/20200111/TOyWUUlhgCF0.png?imageMogr2/format/webp/interlace/1)

2. ![mark](https://pic.yqqy.top/blog/20200111/DuOpHBJ1rdVH.png?imageMogr2/format/webp/interlace/1)
