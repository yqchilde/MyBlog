# Zinx学习之全局配置模块

<!--more-->

![img](https://pic.yqqy.top/blog/20210102233220.png?imageMogr2/format/webp/interlace/1 "全局配置脑图")

### 配置文件

写一个全局配置是为了更方便的维护项目已经修改代码参数，创建 `conf/zinx.json` 文件，配置信息如下：

```json
{
  "Name": "demo server",
  "Host": "127.0.0.1",
  "IPVersion": "tcp4",
  "TcpPort": 8999,
  "MaxConn": 3
}
```

### 全局参数映射

创建 `utils/globalobj.go` 文件，代码编写如下：

```go
// GlobalObj 用来映射 zinx.json
type GlobalObj struct {
	TcpServer     ziface.IServer //全局Server对象
	IPVersion     string         // IP版本
	Host          string         //当前服务器主机IP
	TcpPort       int            //当前服务器主机监听端口号
	Name          string         //当前服务器名称
	Version       string         //当前服务版本号
	MaxPacketSize uint32         //都需数据包的最大值
	MaxConn       int            //当前服务器主机允许的最大链接个数
}

// 定义一个全局的对象
var GlobalObject *GlobalObj

//读取用户的配置文件
func (g *GlobalObj) Reload() {
	data, err := ioutil.ReadFile("conf/zinx.json")
	if err != nil {
		panic(err)
	}

	err = json.Unmarshal(data, &GlobalObject)
	if err != nil {
		panic(err)
	}
}

/*
   提供init方法，默认加载
*/
func init() {
	//初始化GlobalObject变量，设置一些默认值
	GlobalObject = &GlobalObj{
		Name:          "ZinxServerApp",
		Version:       "V4",
		TcpPort:       8999,
		Host:          "0.0.0.0",
		MaxConn:       1000,
		MaxPacketSize: 4096,
	}
	//从配置文件中加载一些用户配置的参数
	GlobalObject.Reload()
}
```

### 替换参数

server.go中NewServer替换

```go
// NewServer 实例Server
func NewServer(name string) ziface.IServer {
	return &Server{
		Name:      utils.GlobalObject.Name,
		IPVersion: utils.GlobalObject.IPVersion,
		IP:        utils.GlobalObject.Host,
		Port:      utils.GlobalObject.TcpPort,
		Router:    nil,
	}
}
```

在server.go中Start()方法中开始加上日志信息：

```go
fmt.Printf("[START] Server name: %s,listenner at IP: %s, Port %d is starting\n", s.Name, s.IP, s.Port)
	fmt.Printf("[Zinx] Version: %s, MaxConn: %d,  MaxPacketSize: %d\n",
		utils.GlobalObject.Version,
		utils.GlobalObject.MaxConn,
		utils.GlobalObject.MaxPacketSize)
```

### 测试

拷贝v3中的代码到v4，在v4目录下创建 `conf/zinx.json` ，将上面的zinx.json内容复制进去，分别运行server.go和client.go即可看到服务日志开始打印。
![img](https://pic.yqqy.top/blog/20210102232931.png?imageMogr2/format/webp/interlace/1 "测试")
