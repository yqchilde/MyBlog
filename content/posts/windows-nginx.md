---
title: "win10配置nginx并设置反向代理"
subtitle: ""
date: 2020-01-11T12:41:23+00:00
lastmod: 2020-01-11T12:41:23+00:00
author: ""
authorLink: ""
description: "记录一下在Win10里配置Nginx并设置代理"

tags: ["Nginx"]
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

### 下载nginx

[点这里下载](http://nginx.org/en/download.html)

![mark](https://pic.yqqy.top/blog/20200111/4E4abJ16o0df.png?imageMogr2/format/webp/interlace/1)

### 解压到指定目录，文件夹内容如下

![mark](https://pic.yqqy.top/blog/20200111/lv1ToWIHSOBx.png?imageMogr2/format/webp/interlace/1)

### 启动Nginx

![mark](https://pic.yqqy.top/blog/20200111/VnoVLHwYdhns.png?imageMogr2/format/webp/interlace/1)

查看是否成功运行（在任务管理器->详细信息中找到nginx进程）

![mark](https://pic.yqqy.top/blog/20200111/JRE5KzvQcjin.png?imageMogr2/format/webp/interlace/1)

### 浏览器运行`127.0.0.1`查看

![mark](https://pic.yqqy.top/blog/20200111/KQPeW6geG0xQ.png?imageMogr2/format/webp/interlace/1)

### 日志目录

* 成功日志 `logs/access.log`
* 错误日志`logs/error.log`
* nginx.pid（如果有`nginx.pid`文件，则说明nginx在运行）

### 常用的命令

```shell
nginx.exe -s stop   //停止nginx
nginx.exe -s reload //重新加载nginx
nginx.exe -s quit   //退出nginx
```

### 注意

1. 以上的命令中，.exe可以去掉
2. 强制停止nginx服务器，如果有未处理的数据，则会丢弃 `nginx -s stop`
3. 优雅的停止nginx服务器，如果有未处理的数据，等待处理完成之后停止 `nginx -s quit`

### 简单配置一个nginx反向代理

1. 修改本地`hosts`文件，添加一个代理本地的域名

   ![mark](https://pic.yqqy.top/blog/20200111/C4x03rT0su6y.png?imageMogr2/format/webp/interlace/1)

2. 我们启用的是golang的服务，启动端口是8080端口，也就是`127.0.0.1:8080`，修改nginx配置，目录地址为`conf/nginx.conf`，删掉原来的`server`，进行重写`server`内容

   ```conf
   server {
       	listen 80; #要监听的端口（本地为80端口）
       	server_name www.yubo.top; #域名（对应上面的hosts文件里面的域名）
   
   		# location范围很广，/ 代表根目录
       	location / {
       		proxy_pass http://127.0.0.1:8080; #要代理的ip地址
       		proxy_set_header Host $host;
   		    proxy_set_header X-Real-IP $remote_addr; #设置真实ip头
   		    proxy_set_header REMOTE-HOST $remote_addr;
   		    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
       	}
   
   		#错误页面配置
       	error_page   500 502 503 504  /50x.html;
           location = /50x.html {
               root   html;
           }
       }
   ```

3. 重新加载nginx会发现生效了，访问`www.yubo.top`就可以代理到`127.0.0.1:8080`的内容了