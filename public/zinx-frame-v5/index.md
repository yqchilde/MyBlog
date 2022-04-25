# Zinx学习之消息封装模块

<!--more-->

![img](https://pic.yqqy.top/blog/20210102233301.png "消息封装脑图")

### 封装消息接口

在之前我们的消息是在Request中作为data传递的，现在我们需要对消息进行细分，所以要抽象出一个消息接口
创建 `ziface/imessage.go` ，代码如下：

```go
// IMessage 将去请求的消息封装起来
type IMessage interface {
	// 获取消息数据段长度
	GetDataLen() uint32
	// 获取消息ID
	GetMsgId() uint32
	// 获取消息内容
	GetData() []byte
	// 设置消息ID
	SetMsgId(uint32)
	// 设置消息内容
	SetData([]byte)
	// 设置消息数据段长度
	SetDataLen(uint32)
}
```

### 消息实体及初始化

这一块没有太多难度，直接贴代码了。

```go
// Message 包含消息的属性
type Message struct {
	// 消息ID
	ID uint32
	// 消息长度
	DataLen uint32
	// 消息内容
	Data []byte
}

// NewMsgPackage 初始化消息包
func NewMsgPackage(id uint32, data []byte) *Message {
	return &Message{
		ID:      id,
		DataLen: uint32(len(data)),
		Data:    data,
	}
}

//获取消息数据段长度
func (msg *Message) GetDataLen() uint32 {
	return msg.DataLen
}

//获取消息ID
func (msg *Message) GetMsgId() uint32 {
	return msg.ID
}

//获取消息内容
func (msg *Message) GetData() []byte {
	return msg.Data
}

//设置消息数据段长度
func (msg *Message) SetDataLen(len uint32) {
	msg.DataLen = len
}

//设计消息ID
func (msg *Message) SetMsgId(msgId uint32) {
	msg.ID = msgId
}

//设计消息内容
func (msg *Message) SetData(data []byte) {
	msg.Data = data
}
```

### 消息的封包与拆包

Zinx框架使用经典的 **TLV(Type-Len-Value)** 封包格式来解决TCP黏包问题。

**TLV** 即在head部分先存放数据长度，再存放数据ID，body部分存数据。这样在读消息时，先去head部分把数据长度读到，然后再把该数据向后偏移该数据长度就是整个数据，每个数据又用数据ID来区分。

**关于TCP黏包** 这里简单查了下资料，便于理解：当客户端调用两次send，服务端一次recv就全部读取出来，我们是希望怎么传就怎么读，保证数据的完整性。现象是这个样子，但是TCP本来就是基于字节流而不是消息包的协议，它自己说的清清楚楚：我会把你的数据变成字节流发到对面去，而且保证顺序不会乱，但是你要自己搞定字节流解析。**所以这个问题其实就是“如何设计应用层协议的问题”。**

创建 `zinx/ziface/idatapack.go` 文件，构造如下方法：

   - 封包数据
   - 拆包数据
   - 得到头部长度

```go
type IDataPack interface {
	// 头部长度
	GetHeadLen() uint32
	// 封包方法
	Pack(msg IMessage) ([]byte, error)
	// 拆包方法
	UnPack([]byte) (IMessage, error)
}
```

创建 `zinx/znet/datapack.go` 文件，实现**IDataPack**中的全部方法：

```go
// DataPack 封包拆包类对象
type DataPack struct{}

// NewDataPack 封包拆包实例初始化方法
func NewDataPack() *DataPack {
	return &DataPack{}
}

// GetHeadLen 获取包头长度方法
func (d *DataPack) GetHeadLen() uint32 {
	// DataLen uint32(4) + MsgID uint32(4)
	return 8
}

// Pack 封包方法
func (d *DataPack) Pack(msg ziface.IMessage) ([]byte, error) {
	dataBuff := bytes.NewBuffer([]byte{})

	// 写DataLen
	if err := binary.Write(dataBuff, binary.LittleEndian, msg.GetDataLen()); err != nil {
		return nil, err
	}

	// 写MsgID
	if err := binary.Write(dataBuff, binary.LittleEndian, msg.GetMsgId()); err != nil {
		return nil, err
	}

	// 写Data数据
	if err := binary.Write(dataBuff, binary.LittleEndian, msg.GetData()); err != nil {
		return nil, err
	}

	return dataBuff.Bytes(), nil
}

// UnPack 拆包方法
func (d *DataPack) UnPack(binaryData []byte) (ziface.IMessage, error) {
	dataBuff := bytes.NewReader(binaryData)
	msg := new(Message)

	// 读DataLen
	if err := binary.Read(dataBuff, binary.LittleEndian, &msg.DataLen); err != nil {
		return nil, err
	}

	// 读MsgID
	if err := binary.Read(dataBuff, binary.LittleEndian, &msg.ID); err != nil {
		return nil, err
	}

	// 判断DataLen是否合法
	if utils.GlobalObject.MaxPacketSize > 0 && msg.DataLen > utils.GlobalObject.MaxPacketSize {
		return nil, errors.New("Too large msg data received ")
	}

	return msg, nil
}
```

### 测试封包拆包

构建单元测试 `zinx/znet/datapack_test.go` 文件，代码如下：

```go
func TestDataPack(t *testing.T) {
	// 模拟Server端
	// 1. 创建 socketTCP
	listener, err := net.Listen("tcp", "127.0.0.1:7777")
	if err != nil {
		t.Log("Server listener err: ", err)
		return
	}

	// 负责处理客户端业务
	go func() {
		for {
			// 2. 从客户端读取数据，拆包处理
			conn, err := listener.Accept()
			if err != nil {
				t.Log("Server accept err: ", err)
			}

			go func(conn net.Conn) {
				// 处理客户端的请求
				dp := NewDataPack()
				for {
					// 2.1 第一次从conn读，把包的head读出来
					headData := make([]byte, dp.GetHeadLen())
					_, err := io.ReadFull(conn, headData)
					if err != nil {
						t.Log("Read head err: ", err)
						break
					}
					// 2.2 拆包
					msgHead, err := dp.UnPack(headData)
					if err != nil {
						t.Log("Server unpack err: ", err)
						return
					}
					// 2.3 第二次从conn读，根据head中dataLen去拿data
					if msgHead.GetDataLen() > 0 {
						msg := msgHead.(*Message)
						msg.Data = make([]byte, msg.GetDataLen())

						// 偏移DataLen后拿数据
						_, err := io.ReadFull(conn, msg.Data)
						if err != nil {
							t.Log("Server unpack data err: ", err)
							return
						}

						t.Logf("MsgID: %d, ReceiveLen: %d, data: %s", msg.ID, msg.DataLen, string(msg.Data))
					}
				}
			}(conn)
		}
	}()

	// 模拟Client端
	conn, err := net.Dial("tcp", "127.0.0.1:7777")
	if err != nil {
		t.Log("Client dial err: ", err)
		return
	}

	// 创建一个封包对象
	dp := NewDataPack()

	// 模拟黏包过程，封装两个msg一同发送
	msg1 := &Message{
		ID:      1,
		DataLen: 4,
		Data:    []byte{'Z', 'i', 'n', 'x'},
	}
	sendData1, _ := dp.Pack(msg1)

	msg2 := &Message{
		ID:      2,
		DataLen: 6,
		Data:    []byte{'n', 'i', 'h', 'a', 'o', '!'},
	}
	sendData2, _ := dp.Pack(msg2)

	sendData1 = append(sendData1, sendData2...)
	_, _ = conn.Write(sendData1)

	// 客户端阻塞
	select {}
}
```

**测试结果如下：**

![img](https://pic.yqqy.top/blog/20210102233439.png "测试")
