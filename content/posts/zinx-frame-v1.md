---
title: "Zinx学习之基础Server模块"
subtitle: ""
date: 2020-12-27T21:00:09+08:00
lastmod: 2020-12-27T21:00:09+08:00
author: ""
authorLink: ""
description: "Zinx是一个轻量级TCP服务框架，会跟着课程开始做一些笔记，Server模块是快速实现Zinx最小版本的一个基础模块。"

tags: ["Golang","Zinx"]
categories: ["Golang"]

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

## 第一章 基础Server

![img](https://pic.yqqy.top/blog/20201227215056.png "Server脑图")

### 创建抽象与实体模块

首先创建两个模块使用，一个是 `ziface` ，一个是 `znet` ，分别创建 `ziface/iserver.go` 和 `znet/server.go` 
作为接口，我们对外只提供方法，故抽象出三个方法，分别是：

- 启动服务器方法
- 停止服务器方法
- 开启业务服务方法

`iserver.go` : 

```go
// IServer 定义Server抽象层
type IServer interface {
	// 启动
	Start()
	// 结束
	Stop()
	// 运行
	Serve()
}
```

在server.go中定义Server结构体实现三个属性

- 服务器名
- 服务器IP
- 服务器监听端口
- IP版本（IPv4、IPv6）

```go
// Server 包含一个服务必要的属性
type Server struct {
	Name      string
	IPVersion string
	IP        string
	Port      int
}

// NewServer 实例Server
func NewServer(name string) ziface.IServer {
	return &Server{
		Name:      name,
		IPVersion: "tcp4",
		IP:        "0.0.0.0",
		Port:      8999,
	}
}
```

**接下来去实现IServer中所有方法**
启动一个服务器需要分为三步：

1. 获取TCP的地址
1. 监听服务器地址
1. 启动Server网络连接业务

### 实现Start() 方法

```go
// Start ...
func (s *Server) Start() {
	fmt.Printf("[Start] Server Listener at IP: %s, Port: %d, is starting\n", s.IP, s.Port)

	go func() {
		// 1. 获取一个TCP的Addr
		addr, err := net.ResolveTCPAddr(s.IPVersion, fmt.Sprintf("%s:%d", s.IP, s.Port))
		if err != nil {
			log.Println("resolve tcp addr error: ", err)
			return
		}

		// 2. 监听服务器的地址
		listener, err := net.ListenTCP(s.IPVersion, addr)
		if err != nil {
			log.Println("listen ", s.IPVersion, "error ", err)
			return
		}

		log.Println("Start Zinx server success, ", s.Name, "Listening...")

		// 3. 阻塞等待服务器链接，处理客户端链接业务（读写）
		for {
			// 如果有客户端连接，阻塞会返回
			conn, err := listener.AcceptTCP()
			if err != nil {
				log.Println("Accept error ", err)
				continue
			}

            // 暂时做一个最大512字节的回显功能
			go func () {
                //不断的循环从客户端获取数据
                for  {
                    buf := make([]byte, 512)
                    cnt, err := conn.Read(buf)
                    if err != nil {
                        fmt.Println("recv buf err ", err)
                        continue
                    }
                    //回显
                    if _, err := conn.Write(buf[:cnt]); err !=nil {
                        fmt.Println("write back buf err ", err)
                        continue
                    }
                }
            }()
		}
	}()
}
```

### 实现Stop() 方法

```go
// Stop ...
func (s *Server) Stop() {
	log.Println("[STOP] Zinx server , name ", s.Name)
}
```

### 实现Serve() 方法

```go
// Serve ...
func (s *Server) Serve() {
	// 启动服务
	s.Start()

	// todo 用来再此处处理其他业务

    // 阻塞在此，防止主协程结束
	select {}
}
```

### 测试

在项目中创建 `v1/client.go` 和 `v1/server.go` 代码分别如下：

```go
// client.go

package main

import (
	"log"
	"net"
	"time"
)

func main() {
	log.Println("client start...")
	time.Sleep(1 * time.Second)
	// 1. 直接链接远程服务器，得到一个conn链接
	conn, err := net.Dial("tcp", "127.0.0.1:8999")
	if err != nil {
		log.Println("client start error ", err)
		return
	}

	// 2. 链接调用write写数据
	for {
		_, err := conn.Write([]byte("Hello Zinx v1"))
		if err != nil {
			log.Println("write buf error ", err)
			return
		}

		buf := make([]byte, 512)
		cnt, err := conn.Read(buf)
		if err != nil {
			log.Println("read buf error ", err)
			return
		}

		log.Printf("Server call back %s, cnt = %d\n", buf, cnt)

		// CPU阻塞
		time.Sleep(1 * time.Second)
	}
}
```

```go
// server.go

package main

import "Zinx/znet"

func main() {
	server := znet.NewServer("Zinx v1")

	server.Serve()
}
```

在控制台分别运行server.go 和 client.go 可以看到client一直在发送，server也可以接收。