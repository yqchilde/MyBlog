---
title: "Zinx学习之Router模块"
subtitle: ""
date: 2020-12-27T21:00:29+08:00
lastmod: 2020-12-27T21:00:29+08:00
author: ""
authorLink: ""
description: "Zinx是一个轻量级TCP服务框架，会跟着课程开始做一些笔记，Router模块主要封装了Request接口和Router接口，用于实现通过路由请求数据，并且通过路由调用链接功能，v3是单路由模式。"

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

![img](https://pic.yqqy.top/blog/20201227213134.png?imageMogr2/format/webp/interlace/1 "Router模块脑图")

现在我们需要给用户一个自定的Conn处理业务的接口，需要用户传入所需要处理的业务，而不是写死的回显方法。我们需要定义一些 `interface{}` 来让用户填写任意格式的连接处理业务方法。
那么，很显然func不能满足我们的需求，接下来就是定义抽象的接口类。
我们需要把客户端请求的连接信息和请求的数据，放在一个 `Request` 的结构体里，这样的好处是我们可以从Request里面得到全部客户端的请求信息，也为我们之后扩展框架有一定的作用，一旦客户端有额外的含义的数据信息，都可以放在这个Request中。可以理解为每次客户端的全部请求数据，Zinx都会把它们放在一个Request结构体中。

### 创建抽象IRequest层

在 `ziface` 下创建 `irequest.go` 

```go
// IRequest 接口
// 实际上是把客户端请求的链接信息和请求的数据包装到一个request中
type IRequest interface {
	// 得到当前链接
	GetConnection() IConnection
	// 得到请求的消息数据
	GetData() []byte
}
```

### 创建实体Request层

在 `znet` 下创建 `request.go` 

```go
type Request struct {
	// 已经和客户端建立好的链接
	conn ziface.IConnection

	// 客户端请求的数据
	data []byte
}

func (r *Request) GetConnection() ziface.IConnection {
	return r.conn
}

func (r *Request) GetData() []byte {
	return r.data
}
```

`request` 相关的封装好了，接下来是配置 `Router` 层，目的是为了在router里面携带服务要处理的业务

### 创建抽象的IRouter层

在 `ziface` 下创建 `irouter.go` 
我们知道 **router** 实际上的作用就是，服务端应用可以给 **Zinx** 框架配置当前链接的处理业务方法，之前是写死的回显方法，有了router就可以通过router携带，并且会定义三个方法，且支持方法重写。

- PreHandle:  在处理conn业务之前的钩子方法Hook
- Handle:       在处理conn业务的主方法Hook
- PostHandle: 在处理conn业务之后的钩子方法Hook

每个方法的形参就是上面封装的 `IRequest` 对象，用来告知我们所要连的链接以及请求数据，作为我们业务方法的输入数据。

```go
// IRouter 路由抽象接口
// 路由里的数据都是IRequest
type IRouter interface {
	// 在处理conn业务之前的钩子方法Hook
	PreHandle(request IRequest)

	// 在处理conn业务的主方法Hook
	Handle(request IRequest)

	// 在处理conn业务之后的钩子方法Hook
	PostHandle(request IRequest)
}
```

### 创建实体Router层

```go
type BaseRouter struct{}

// PreHandle 在处理conn业务之前的钩子方法Hook
func (br *BaseRouter) PreHandle(request ziface.IRequest) {

}

// Handle 在处理conn业务的主方法Hook
func (br *BaseRouter) Handle(request ziface.IRequest) {

}

// PostHandle 在处理conn业务之后的钩子方法Hook
func (br *BaseRouter) PostHandle(request ziface.IRequest) {

}
```

我们注意到了，在实体的Router层里我们写了空的结构体 `BaseRouter` ，这里讲一下目的。在实现router时，我们要基于 `BaseRouter` ，也就是面向对象中的继承基类，继承之后就可以对这个基类的方法进行重写。
举个例子，有的router没有前置或后置处理业务，在继承基类后，有需要写的就直接重写方法即可，不需要的直接忽略即可。

### IServer 添加路由添加功能

我们需要给 `IServer` 结构体中添加一个方法 `AddRouter` ，目的是让框架使用者自定义一个Router处理业务方法。
在 `zinx/ziface/iserver.go` 修改如下：

```go
type IServer interface {
	// 启动
	Start()
	// 结束
	Stop()
	// 运行
	Serve()
	// 路由功能，给当前的服务注册一个路由方法
	AddRouter(router IRouter)
}
```

### Server中添加Router成员

在 `zinx/znet/server.go` 修改如下：

```go
type Server struct {
	Name      string
	IPVersion string
	IP        string
	Port      int

	// 给当前的Server添加一个router，Server注册的链接对应的处理业务
	Router ziface.IRouter
}
```

在初始化 `NewServer()` 方法中也要添加一个初始化成员

```go
func NewServer(name string) ziface.IServer {
	return &Server{
		Name:      name,
		IPVersion: "tcp4",
		IP:        "0.0.0.0",
		Port:      8999,
		Router:    nil, // 初始化为nil
	}
}
```

还记得在之前临时在 `Connection` 中添加了一个 `handleAPI ziface.HandleFunc` ，目的是为了直接在业务处理方法，如今我们封装了Router，就是为了在Router中携带业务方法，所以可以删掉了，然后添加处理的 `Router` 对象，如下：

```go
// 链接模块
type Connection struct {
	// 当前链接的socket TCP套接字
	Conn *net.TCPConn
	// 链接的ID
	ConnID uint32
	// 当前的链接状态
	isClosed bool
	// 告知当前链接已经退出/停止的 channel
	ExitChan chan bool
	// 该链接处理的方法Router
	Router ziface.IRouter
}
```

然后去实现接口中的 `AddRouter` 方法

```go
func (s *Server) AddRouter(r ziface.IRouter) {
	s.Router = r
	log.Println("Add router success!")
}
```

同样在初始化 `NewConnection` 中添加router

```go
// 初始化链接模块的方法
func NewConnection(conn *net.TCPConn, connID uint32, router ziface.IRouter) *Connection {
	return &Connection{
		Conn:     conn,
		ConnID:   connID,
		isClosed: false,
		Router:   router,
		ExitChan: make(chan bool, 1),
	}
}
```

接下来当然是去处理之前在 `StartReader()` 中写的调用 `HandleFunc` 方法
删掉：

```go
if err := c.handleAPI(c.Conn, buf, cnt); err != nil {
    log.Println("ConnID = ", c.ConnID, "Handle is error: ", err)
    break
}
```

添加：

```go
// 从路由中，找到注册绑定的Conn对应的router调用
req := Request{
    conn: c,
    data: buf,
}
// 执行注册的路由方法
go func(request ziface.IRequest) {
    c.Router.PreHandle(request)
    c.Router.Handle(request)
    c.Router.PostHandle(request)
}(&req)
```

---

以上完成了Zinx的路由模块功能，接下来是测试了。
拷贝之前的 v2 测试包重命名为 v3。修改 `Server.go` ，我们实现一个 `IRouter` ，并重写里面的三个方法，如下：

```go
type PingRouter struct {
	znet.BaseRouter
}

// PreHandle 在处理conn业务之前的钩子方法Hook
func (p *PingRouter) PreHandle(request ziface.IRequest) {
	log.Println("Call Router PreHandle")
	_, err := request.GetConnection().GetTCPConnection().Write([]byte("before.ping...\n"))
	if err != nil {
		log.Println("before ping error is ", err)
	}
}

// Handle 在处理conn业务的主方法Hook
func (p *PingRouter) Handle(request ziface.IRequest) {
	log.Println("Call Router Handle")
	_, err := request.GetConnection().GetTCPConnection().Write([]byte("ping...\n"))
	if err != nil {
		log.Println("ping error is ", err)
	}
}

// PostHandle 在处理conn业务之后的钩子方法Hook
func (p *PingRouter) PostHandle(request ziface.IRequest) {
	log.Println("Call Router PostHandle")
	_, err := request.GetConnection().GetTCPConnection().Write([]byte("after.ping...\n"))
	if err != nil {
		log.Println("after ping error is ", err)
	}
}
```

在 `main` 方法中添加 `Router` 

```go
func main() {
	server := znet.NewServer("Zinx v3")

	// 添加自定义router
	server.AddRouter(&PingRouter{})

	server.Serve()
}
```

测试结果如下图：（可以看出三个方法按照链式进行）

![image](https://pic.yqqy.top/blog/20201227213249.png?imageMogr2/format/webp/interlace/1 "测试结果")

现在Zinx框架的路由只是单路由结构，后续会增加多路由功能