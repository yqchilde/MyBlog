---
title: "Golang实现软件设计模式"
subtitle: ""
date: 2020-04-21T15:00:16+00:00
lastmod: 2020-04-21T15:00:16+00:00
author: ""
authorLink: ""
description: "Golang复习软件设计模式，通过设计模式学习其设计思想，无他，唯手熟尔。"

tags: ["Golang"]
categories: ["Golang"]

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

![img](https://pic.yqqy.top/blog/20200406232406.png?imageMogr2/format/webp/interlace/1)

虽然设计模式存在于面向对象的语言中，Golang并不是一门OOP的语言，但是这并代表不能实现设计模式。

## 六大原则

### 开闭原则

一个软件实体，如类、模块和函数应该对扩展开放，对修改关闭。在软件的生命周期内，因变化、升级、和维护对原有代码进行修改，可能会引入错误，所以当软件需要变化时，通过扩展软件实体的行为来实现变化，而不是修改原有代码。

### 里氏代换原则

任何基类可以出现的地方，子类一定可以出现。里氏代换原则是继承复用的基石，只有当衍生类可以替换掉基类，软件单位的功能不受到影响时，基类才能真正被复用，而衍生类也能够在基类的基础上增加新的行为。里氏代换原则是对“开闭”原则的补充。实现“开闭”原则的关键步骤就是抽象化。而基类与子类的继承关系就是抽象化的具体实现，所以里氏代换原则是对实现抽象化的具体步骤的规范。通俗的说就是：子类可以扩展父类的功能，但不能改变父类原有的功能。

### 单一职责原则

一个类只负责一项职责。

### 依赖倒置原则

高层模块不应该依赖低层模块，二者都应该依赖其抽象；抽象不应该依赖细节；细节应该依赖抽象。通俗的说：多用抽象的接口来描述相同的动作，降低实现这个动作的人和物之前的耦合度。

### 接口隔离原则

客户端不应该依赖它不需要的接口；一个类对另一个类的依赖应该建立在最小的接口上。

### 迪米特法则

一个对象应该对其他对象保持最少的了解。类与类之间的关系越密切，耦合度越大，当一个类发生改变时，对另一个类的影响也越大。

|          创建型           |          结构型           |          行为型           |
| :-----------------------: | :-----------------------: | :-----------------------: |
| [创建者模式](#创建者模式) | [装饰者模式](#装饰者模式) | [迭代器模式](#迭代器模式) |
|   [工厂模式](#工厂模式)   |        适配器模式         |   [命令模式](#命令模式)   |
| [抽象工厂模式](#抽象工厂) |         代理模式          | [责任链模式](#责任链模式) |
|         单例模式          |         外观模式          | [观察者模式](#观察者模式) |

## 创建型

### 创建者模式

> wiki解释

创建者模式（也可称为生成器模式 / 建造模式），是一种对象构建模式。它可以将复杂对象的建造过程抽象出来（抽象类别），使这个抽象过程的不同实现方法可以构造出不同表现（属性）的对象。

> 建造者模式的主要角色

- Product：产品角色。它是包含多个组成部件的复杂对象和属性，是一个模型。

- Builder：抽象建造者。它是一个包含创建产品各个子部件的抽象方法的接口或者抽象类，通常还包含一个返回复杂产品的方法 ，该方法一般是抽象并且返回值类型是产品角色（Product）。理解为它在调用生成方法。

- ConcreteBuilder：具体建造者。实现 Builder 接口或者继承Builder 抽象类，完成复杂产品的各个部件的具体创建方法。

- Director：指挥者。它调用建造者（Builder）中的部件构造与装配方法完成复杂对象的创建，不设计产品具体构建细节。

> 主要作用

- 用户不用关心对象的建造过程就可以创建复杂对象

- 方便用户创建很多类似或者有相同属性的对象

- 代码复用性高，且用封装性

> Golang实现

```go
package builder

// 为创建一个Product对象的各个部件指定抽象接口
type Builder interface {
	Build()
}

// 实现Builder的接口以构造和装配该产品的各个部件
type ConcreteBuilder struct {
	built bool
}

// 构造一个使用Builder接口的对象
type Director struct {
	builder Builder
}

// 表示被构造的复杂对象
// ConcreteBuilder创建该产品的内部表示并定义它的装配过程
type Product struct {
	Build bool
}

// 实例化Director对象
func NewDirector(b Builder) Director {
	return Director{builder: b}
}

// 实例化ConcreteBuilder对象
func NewConcreteBuilder() ConcreteBuilder {
	return ConcreteBuilder{built: false}
}

// ConcreteBuilder实现了 Builder 接口的 Build 方法
func (c *ConcreteBuilder) Build() {
	c.built = true
}

// 构造了一个使用Builder接口的对象
func (d *Director) Construct() {
	d.builder.Build()
}

// 通过ConcreteBuilder 获取 Product 的结果
func (c *ConcreteBuilder) GetResult() Product {
	return Product{Build: c.built}
}
```

> 单元测试

```go
package builder

import (
	"fmt"
	"testing"
)

func TestConcreteBuilder_GetResult(t *testing.T) {
	// 具体建造者，实现一个完成Builder接口的对象
	builder := NewConcreteBuilder()
	// 指挥具体建造者操作
	director := NewDirector(&builder)
	// 指挥具体建造者去做建造工作
	director.Construct()
	// 建造完成的结果
	result := builder.GetResult()

	fmt.Println(result.Build)
}
// result
=== RUN   TestConcreteBuilder_GetResult
true
--- PASS: TestConcreteBuilder_GetResult (0.00s)
PASS
```



### 工厂模式

**简单工程模式**

> wiki解释

普通的工厂方法模式通常伴随着对象的具体类型与工厂具体类型的一一对应，客户端代码根据需要选择合适的具体类型工厂使用。然而，这种选择可能包含复杂的逻辑，这时，可以创建一个单一的工厂类，用以包含这种选择逻辑，根据参数的不同选择实现不同的具体对象。这个工厂类不需要由每个具体产品实现一个自己的具体的工厂类。

> 白话理解

即单独创建一个单一的工厂类，只做相关的实现，避免复杂的创建过程

> Golang实现

```go
package simple_factory

// 实现接口
type API interface {
	say(name string) string
}

type Chinese struct{}
type English struct{}

// 实现方法
func NewAPI(lang string) API {
	switch lang {
	case "ch":
		return &Chinese{}
	case "en":
		return &English{}
	default:
		return nil
	}
}

func (*Chinese) say(name string) string {
	return "你好" + name
}

func (*English) say(name string) string {
	return "Hello" + name
}
```

> 单元测试

```go
package simple_factory

import (
	"fmt"
	"testing"
)

func TestNewAPI(t *testing.T) {
	apiCh := NewAPI("ch")
	say1 := apiCh.say("张")
	fmt.Println(say1)

	apiEn := NewAPI("en")
	say2 := apiEn.say("Tom")
	fmt.Println(say2)
}
// result
=== RUN   TestNewAPI
你好张
HelloTom
--- PASS: TestNewAPI (0.00s)
PASS
```

**复杂工厂模式**

> 白话理解

当我们需要实现数据固定，实现不同操作的时候，就可以扩展操作来实现新加入的方法不影响之前的代码，就比如下面的代码，实现了一个接口，让接口实现设置、计算方法，设计一个减法工厂，使其实现减法，如果我们需要再实现一个加法工厂，只需要单独在创建一个加法的，在项目中，往往这些需要抽离在单独的文件中。

```go
package factory

type Operator interface {
	SetLeft(int)
	SetRight(int)
	Result() int
}

// factory pattern
type OperatorFactory interface {
	Create() Operator
}

// operator base
type OperatorBase struct {
	left, right int
}

// 赋值
func (o *OperatorBase) SetLeft(left int) {
	o.left = left
}

// 赋值
func (o *OperatorBase) SetRight(right int) {
	o.right = right
}

// 操作数
type SubOperator struct {
	*OperatorBase
}

// 减法工厂
type SubOperatorFactory struct{}

// 实际操作
func (o SubOperator) Result() int {
	return o.left - o.right
}

func (o SubOperatorFactory) Create() Operator {
	return &SubOperator{
		&OperatorBase{},
	}
}
```

> 单元测试

```go
package factory

import (
	"fmt"
	"testing"
)

func TestSubOperator_Result(t *testing.T) {
	var fac OperatorFactory
	fac = SubOperatorFactory{}

	op := fac.Create()
	op.SetLeft(10)
	op.SetRight(20)
	fmt.Println(op.Result())
}
// result
=== RUN   TestSubOperator_Result
-10
--- PASS: TestSubOperator_Result (0.00s)
PASS
```

### 抽象工厂

## 结构型

### 装饰者模式

> wiki解释

修饰模式，是面向对象编程领域中，一种动态地往一个类中添加新的行为的设计模式。就功能而言，修饰模式相比生成子类更为灵活，这样可以给某个对象而不是整个类添加一些功能。

通过使用修饰模式，可以在运行时扩充一个类的功能。原理是：增加一个修饰类包裹原来的类，包裹的方式一般是通过在将原来的对象作为修饰类的构造函数的参数。装饰类实现新的功能，但是，在不需要用到新功能的地方，它可以直接调用原来的类中的方法。修饰类必须和原来的类有相同的接口。

> 白话

做一个修饰方法，他的参数是要修饰的函数的参数，他的返回值是我们想要得到的结果，动态地给一个对象添加一些额外的职责。比如我们要实现一个记录每个函数执行时间的方法，如果我们手动在每个函数添加记录执行时间的函数，在函数很多的情况下，要花费大量时间且不易维护，所以就需要修饰模式来帮我们修饰一个方法，得到特定的结果。

> Golang实现

```go
package decorator

import "fmt"

// 定义一个组件接口带有一个计算属性
type Component interface {
	Calc(int) interface{}
}

// 定义一个计算正方形面积组件模型
type SquareAreaComponent struct{}

// 计算面积
func (s *SquareAreaComponent) Calc(sideLen int) interface{} {
	return sideLen * sideLen
}

// 定义修饰器
type SignDecorator struct {
	component Component
}

// 实例修饰器
func WarpMulDecorator(c Component) Component {
	return &SignDecorator{component: c}
}

// 用修饰器返回特定内容
func (d *SignDecorator) Calc(sideLen int) interface{} {
	return fmt.Sprintf("边长为 %v 的正方形面积为 %v", sideLen, sideLen*sideLen)
}
```

> 单元测试

```go
package decorator

import (
	"fmt"
	"testing"
)

func TestCall(t *testing.T) {
    // 普通方法
	s := &SquareAreaComponent{}
	ret := s.Calc(10)
	fmt.Println(ret)

    // 用修饰器调用
	decorator := WarpMulDecorator(s)
	fmt.Println(decorator.Calc(10))
}
// result
=== RUN   TestCall
100
边长为 10 的正方形面积为 100
--- PASS: TestCall (0.00s)
PASS
```



## 行为型

### 迭代器模式

> wiki解释

在面向对象的编程里，**迭代器模式**是一种设计模式，是一种最简单也最常见的设计模式。他可以让用户透过特定的接口寻访容器中的每一个元素而不用了解底层的实现。

> 白话解释   推荐这篇文章，真的是白话解释

* [白话解释 迭代器(ITERATOR)和生成器(GENERATOR)](https://segmentfault.com/a/1190000007208388)

> Golang实现

```go
package iterator

type Iterator interface {
	Idx() int         // 索引
	Val() interface{} // 值
	HasNext() bool    // 是否还有下一个值
	Next()            // 下一个
}

type ArrayIterator struct {
	array []interface{}
	index *int
}

// return idx
func (a *ArrayIterator) Idx() *int {
	return a.index
}

// return val
func (a *ArrayIterator) Val() interface{} {
	return a.array[*a.index]
}

// return is next
func (a *ArrayIterator) HasNext() bool {
	return len(a.array) >= *a.index+1
}

// return next
func (a *ArrayIterator) Next() {
	if a.HasNext() {
		*a.index++
	}
}
```

> 单元测试

```go
package iterator

import (
	"fmt"
	"testing"
)

func TestArrayIterator(t *testing.T) {
	arr := []interface{}{1, 3, 5, 7, 9}
	idx := 0
	interator := ArrayIterator{array: arr, index: &idx}
	for it := interator; it.HasNext(); it.Next() {
		idx, val := it.Idx(), it.Val().(int)
		for val != arr[*idx] {
			fmt.Println("err")
		}
		fmt.Println(*idx, val)
	}
}
// result
=== RUN   TestArrayIterator
0 1
1 3
2 5
3 7
4 9
--- PASS: TestArrayIterator (0.00s)
PASS
```



### 命令模式

> wiki解释

在面向对象编程的范畴中，命令模式是一种设计模式，它尝试以对象来代表实际行动。命令对象可以把行动及其参数封装起来，于是这些行动可以被：

- 重复多次

- 取消（如果该对象有实现的话）

- 取消后又再重做

> Golang实现

```go
package command

import "fmt"

// 定义一个Person，他有名字，他有动作且可以传递
type Person struct {
	name string
	cmd  Command
}

// 定义一个动作，他有执行者的名字，有具体操作的方法
type Command struct {
	person *Person
	method func()
}

// 实例化动作
func NewCommand(p *Person, method func()) Command {
	return Command{
		person: p,
		method: method,
	}
}

// execute
func (c *Command) Execute() {
	c.method()
}

// 实例化Person
func NewPerson(name string, cmd Command) Person {
	return Person{
		name: name,
		cmd:  cmd,
	}
}

func (p *Person) Buy() {
	fmt.Println(fmt.Sprintf("%s is buying", p.name))
}

func (p *Person) Cook() {
	fmt.Println(fmt.Sprintf("%s is cooking", p.name))
	p.cmd.Execute()
}

func (p *Person) Wash() {
	fmt.Println(fmt.Sprintf("%s is washing", p.name))
	p.cmd.Execute()
}

func (p *Person) Listen() {
	fmt.Println(fmt.Sprintf("%s is Listening", p.name))
	p.cmd.Execute()
}

func (p *Person) Talk() {
	fmt.Println(fmt.Sprintf("%s is Talking", p.name))
	p.cmd.Execute()
}
```

> 单元测试

```go
package command

import "testing"

func TestCommand_Execute(t *testing.T) {
    // 依次调用
	zhangsan := NewPerson("zhangsan", NewCommand(nil, nil))
	lisi := NewPerson("lisi", NewCommand(&zhangsan, zhangsan.Buy))
	wangwu := NewPerson("wangwu", NewCommand(&lisi, lisi.Cook))
	zhaoliu := NewPerson("zhaoliu", NewCommand(&wangwu, wangwu.Listen))
	sunqi := NewPerson("sunqi", NewCommand(&zhaoliu, zhaoliu.Wash))

	sunqi.Talk()
}
// result
=== RUN   TestCommand_Execute
sunqi is Talking
zhaoliu is washing
wangwu is Listening
lisi is cooking
zhangsan is buying
--- PASS: TestCommand_Execute (0.00s)
PASS
```



### 责任链模式

> wiki解释

责任链模式在面向对象程式设计里是一种软件设计模式，它包含了一些命令对象和一系列的处理对象。每一个处理对象决定它能处理哪些命令对象，它也知道如何将它不能处理的命令对象传递给该链中的下一个处理对象。

> 白话讲：
>
> 我个人理解就是如果一件事情，多个人要去做，就比如请假来说，小于3天的班主任可以请，大于3天小于5天的级部主任可以请，大于5天小于10天的只能教务校长请，其他的不给请，在传统模式中写代码需要写一堆的`if`  `else if`  `else`，这会使得代码臃肿、耦合度高。那么就需要使用**责任链模式**，通过接口暴露出一个方法，每个人只需要做到方法里面的事即可，如果自己处理不了，那就交给上一级处理。

> Golang实现

```go
package Responsibillity_Chain

import "strconv"

// 定义一个接口 里面是处理方法
type Handler interface {
	Handler(handlerID int) string
}

// 这个处理方法有一些参数，例如执行者的名字，ID以及他可以管理的下级
type handler struct {
	name      string
	next      Handler
	handlerId int
}

// 实例化这个处理方法
func NewHandler(name string, next Handler, handlerID int) *handler {
	return &handler{
		name:      name,
		next:      next,
		handlerId: handlerID,
	}
}

// 定义处理方法
func (h *handler) Handler(handlerID int) string {
	// 如果是自己执行，是可以的
	if h.handlerId == handlerID {
		return h.name + " handled " + strconv.Itoa(handlerID)
	}
	// 如果自己已经是一串责任链中的最后一个人，即代表后面没有，那就返回
	if h.next == nil {
		return ""
	}
	// 否则就继续给下个人执行
	return h.next.Handler(handlerID)
}
```

> 单元测试

```go
package Responsibillity_Chain

import (
	"fmt"
	"testing"
)

func TestHandler_Handle(t *testing.T) {
	// 李四是大领导 张三是小领导 李四管着张三
	zhangsan := NewHandler("zhangsan", nil, 1)
	lisi := NewHandler("lisi", zhangsan, 2)

	// 李四可以自己处理，当然也可以叫下级处理
	task := lisi.Handler(2)
	//task := lisi.Handler(1)
	fmt.Println(task)
	// 张三只能处理自己的，处理不了的还需要给上级
	task = zhangsan.Handler(1)
	fmt.Println(task)
}
// result
=== RUN   TestHandler_Handle
lisi handled 2
zhangsan handled 1
--- PASS: TestHandler_Handle (0.00s)
PASS
```



### 观察者模式

> wiki解释

观察者模式是软件设计模式的一种，在此种模式中，一个目标对象管理所有相依于它的观察者对象，并且在它本身的状态改变时主动发出通知。这通常透过呼叫各观察者所提供的方法来实现，此种模式通常被用来实时事件处理系统。

> 白话

添加一个事件监听，可以用来监听改变转态等等

> Golang实现

```go
package Observer

import (
	"fmt"
	"sync"
	"time"
)

// 定义事件，用于通知
type Event struct {
	Data int
}

// 定义观察者接口，通知回馈
type Observer interface {
	NotifyCallback(event Event)
}

// 定义主题接口，实现添加监听，移除监听，通知三个方法
type Subject interface {
	AddListener(observer Observer)
	RemoveListener(observer Observer)
	Notify(event Event)
}

// 确保观察者唯一
type eventObserver struct {
	ID   int
	Time time.Time
}

// 方法体
type eventSubject struct {
	Observers sync.Map
}

// NotifyCallback
func (e *eventObserver) NotifyCallback(event Event) {
	fmt.Printf("Recieved: %d after %v\n", event.Data, time.Since(e.Time))
}

// AddListener
func (e *eventSubject) AddListener(obs Observer) {
	e.Observers.Store(obs, struct{}{})
}

// RemoveListener
func (e *eventSubject) RemoveListener(obs Observer) {
	e.Observers.Delete(obs)
}

// Notify
func (e *eventSubject) Notify(event Event) {
	// 范围依次调用映射中的每个键和值，如果f返回false，则range停止迭代
	e.Observers.Range(func(key, value interface{}) bool {
		if key == nil {
			return false
		}
		key.(Observer).NotifyCallback(event)
		return true
	})
}

// 输出fib序列数
func Fib(n int) chan int {
	out := make(chan int)
	go func() {
		defer close(out)
		for i, j := 0, 1; i < n; i, j = i+j, i {
			out <- i
		}
	}()
	return out
}

```

> 单元测试

```go
package Observer

import (
	"sync"
	"testing"
	"time"
)

func TestFib(t *testing.T) {
	// 实例化主题对象
	subject := eventSubject{Observers: sync.Map{}}
	// 绑定观察者ID
	obs1 := eventObserver{ID: 1, Time: time.Now()}
	obs2 := eventObserver{ID: 1, Time: time.Now()}
	// 添加监听
	subject.AddListener(&obs1)
	subject.AddListener(&obs2)

	for k := range Fib(10) {
		subject.Notify(Event{Data: k})
	}
}
// result
=== RUN   TestFib
Recieved: 0 after 0s
Recieved: 0 after 0s
Recieved: 1 after 0s
Recieved: 1 after 0s
Recieved: 1 after 0s
Recieved: 1 after 0s
Recieved: 2 after 0s
Recieved: 2 after 0s
Recieved: 3 after 0s
Recieved: 3 after 0s
Recieved: 5 after 962.3µs
Recieved: 5 after 962.3µs
Recieved: 8 after 962.3µs
Recieved: 8 after 962.3µs
--- PASS: TestFib (0.00s)
PASS
```



## 参考

- WIKI百科

- [Bilibili](https://space.bilibili.com/375038855/dynamic)

- [github](https://github.com/senghoo/golang-design-pattern)
