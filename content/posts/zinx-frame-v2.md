---
title: "Zinx学习之基础链接封装和业务绑定模块"
subtitle: ""
date: 2020-12-27T21:00:23+08:00
lastmod: 2020-12-27T21:00:23+08:00
author: ""
authorLink: ""
description: "Zinx是一个轻量级TCP服务框架，会跟着课程开始做一些笔记，基础链接封装和业务绑定模块模块是将v1中直接在start方法中的功能抽象出一个Connection，该Connection又有业务绑定功能。"

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

![img](https://pic.yqqy.top/blog/20201227215136.png?imageMogr2/format/webp/interlace/1 "基础链接封装和业务绑定模块脑图")

{{indent}}v1版本已经实现了一个基础的Server框架，现在我们需要对客户端链接和不同的客户端链接所处理的不同业务在做一层接口封装。在ziface下创建一个链接的接口文件 `iconnection.go` ，以及在znet目录下的实现文件 `connection.go` 。

### 定义IConnection接口

```go
// IConnection 定义链接模块的抽象层
type IConnection interface {
	// 启动链接 让当前的链接准备开始工作
	Start()
	// 停止链接 结束当前链接的工作
	Stop()
	// 获取当前链接的绑定 socket conn
	GetTCPConnection() *net.TCPConn
	// 获取当前链接模块的链接ID
	GetConnID() uint32
	// 获取远程客户端的 TCP 状态 IP Port
	RemoteAddr() net.Addr
	// 发送数据 将数据发送给远程的客户端
	Send(data []byte) error
}

// 定义一个处理链接业务的方法
// 传入TCP句柄，数据以及长度
type HandleFunc func(*net.TCPConn, []byte, int) error
```

### 定义Connection结构体

```go
// 链接模块
type Connection struct {
	// 当前链接的socket TCP套接字
	Conn *net.TCPConn
	// 链接的ID
	ConnID uint32
	// 当前的链接状态
	isClosed bool
    // 该链接的处理方法API
    handleAPI ziface.HandleFunc
	// 告知当前链接已经退出/停止的 channel
	ExitChan chan bool
}

// 初始化链接模块的方法
func NewConnection(conn *net.TCPConn, connID uint32, callback_api ziface.HandFunc) *Connection {
	return &Connection{
		Conn:     conn,
		ConnID:   connID,
		isClosed: false,
		handleAPI: callback_api,
		ExitChan: make(chan bool, 1),
	}
}
```

### 实现IConnection接口所有方法

**Start()方法**

```go
// Start ...
func (c *Connection) Start() {
	log.Printf("Connection Start... ConnID = %d", c.ConnID)

	// 启动从当前链接的读数据的业务
	go c.StartReader()
}

// 此时单独构造了一个StartReader方法，用来处理开始读的业务
// StartReader 开始读的业务
func (c *Connection) StartReader() {
	log.Println("Reader Goroutine is running...")
	// 读完业务结束
	defer c.Stop()

	// 具体业务
	for {
		// 读取客户端的数据到buf中，最大512字节
		buf := make([]byte, 512)
		cnt, err := c.Conn.Read(buf)
		if err != nil {
			log.Println("read buf error ", err)
			continue
		}

		// 调用当前链接绑定HandleFunc
		if err := c.handleAPI(c.Conn, buf, cnt); err != nil {
			log.Println("ConnID = ", c.ConnID, "Handle is error: ", err)
			break
		}
	}
}

```

**Stop()方法**

```go
// Stop 停止链接 结束当前链接的工作
func (c *Connection) Stop() {
	log.Printf("Connection Stop... ConnID = %d", c.ConnID)

	// 如果已经关闭
	if c.isClosed {
		return
	}

	// 关闭链接
	c.isClosed = true
	_ = c.Conn.Close()

	// 回收资源
	close(c.ExitChan)
}
```

**GetTCPConnection()方法**

```go
// GetTCPConnection 获取当前链接的绑定 socket conn
func (c *Connection) GetTCPConnection() *net.TCPConn {
	return c.Conn
}
```

**GetConnID()方法**

```go
// GetConnID 获取当前链接模块的链接ID
func (c *Connection) GetConnID() uint32 {
	return c.ConnID
}
```

**RemoteAddr()方法**

```go
// RemoteAddr 获取远程客户端的 TCP 状态 IP Port
func (c *Connection) RemoteAddr() net.Addr {
	return c.Conn.RemoteAddr()
}
```

**Send()方法**

```go
// Send 发送数据 将数据发送给远程的客户端
func (c *Connection) Send(data []byte) error {
    // todo
	return nil
}
```

### 链接绑定方法处理

{{indent}}在v1中回显功能直接写在Start方法中，但是在v2中我们在Connection结构体中定义了该链接的处理方法API： `handleFunc` ，所以我们去 `server.go` 外部实现一个回显功能的方法，如下：

```go
// CallBackToClient 定义当前客户端链接所绑定的handle api
func CallBackToClient(conn *net.TCPConn, data []byte, cnt int) error {
	// 回显的业务
	if _, err := conn.Write(data[:cnt]); err != nil {
		log.Println("write back buf err is ", err)
		return errors.New("CallBackToClient error")
	}

	return nil
}
```

{{indent}}在 **Start()** 方法中将原本的回显功能删掉，使用 `NewConnection` 去直接绑定一个链接和该链接绑定的业务方法。

```go
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
		var cid uint32 = 0
		// 3. 阻塞等待服务器链接，处理客户端链接业务（读写）
		for {
			// 如果有客户端连接，阻塞会返回
			conn, err := listener.AcceptTCP()
			if err != nil {
				log.Println("Accept error ", err)
				continue
			}

			// 将处理新链接的业务方法和conn进行绑定，得到链接模块
			dealConn := NewConnection(conn, cid, CallBackToClient)
			cid++

			// 启动业务模块
			go dealConn.Start()
		}
	}()
}
```

### 测试

{{indent}}将v1的测试复制一份到v2里面，将其中的log信息修改为v2的数据，在两个控制台分别运行server.go和client.go 可以看到客户端一直发送数据，服务端也可以一直接收到。