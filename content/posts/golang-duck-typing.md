---
title: "Golang-Duck&Typing"
subtitle: ""
date: 2020-03-24T14:52:28+00:00
lastmod: 2020-03-24T14:52:28+00:00
author: ""
authorLink: ""
description: "在鸭子类型中，关注点在于对象的行为，能作什么；而不是关注对象所属的类型。"

tags: ["Golang"]
categories: ["Golang"]

hiddenFromHomePage: false
hiddenFromSearch: false

featuredImage: ""
featuredImagePreview: ""

toc:
  enable: false
math:
  enable: false
lightgallery: false
license: ""
---
<!--more-->

> [维基百科](https://zh.wikipedia.org/wiki/%E9%B8%AD%E5%AD%90%E7%B1%BB%E5%9E%8B#.E5.9C.A8Python.E4.B8.AD)解释：
>
> **鸭子类型**在[程序设计](https://zh.wikipedia.org/wiki/程序设计)中是[动态类型](https://zh.wikipedia.org/wiki/類型系統)的一种风格。在这种风格中，一个对象有效的语义，不是由继承自特定的类或实现特定的接口，而是由"当前[方法](https://zh.wikipedia.org/wiki/方法_(電腦科學))和属性的集合"决定。这个概念的名字来源于由[詹姆斯·惠特科姆·莱利](https://zh.wikipedia.org/w/index.php?title=詹姆斯·惠特科姆·莱利&action=edit&redlink=1)提出的[鸭子测试](https://zh.wikipedia.org/wiki/鸭子测试)，“鸭子测试”可以这样表述：
>
> ***当看到一只鸟走起来像鸭子、游泳起来像鸭子、叫起来也像鸭子，那么这只鸟就可以被称为鸭子。***
>
> 在鸭子类型中，关注点在于对象的行为，能作什么；而不是关注对象所属的类型。例如，在不使用鸭子类型的语言中，我们可以编写一个函数，它接受一个类型为"鸭子"的对象，并调用它的"走"和"叫"方法。在使用鸭子类型的语言中，这样的一个函数可以接受一个任意类型的对象，并调用它的"走"和"叫"方法。如果这些需要被调用的方法不存在，那么将引发一个运行时错误。任何拥有这样的正确的"走"和"叫"方法的对象都可被函数接受的这种行为引出了以上表述，这种决定类型的方式因此得名。
>
> 鸭子类型通常得益于"不"测试方法和函数中参数的类型，而是依赖文档、清晰的代码和测试来确保正确使用。
>
> 在常规类型中，我们能否在一个特定场景中使用某个对象取决于这个对象的类型，而在鸭子类型中，则取决于这个对象是否具有某种属性或者方法——即只要具备特定的属性或方法，能通过鸭子测试，就可以使用。


**按照我的理解** 就是实现了 `测试` 是否实现了某个接口中的所有方法，代码如下

```go
package main

import "fmt"

type Duck interface {
	Gaga()
	Swim()
}

func DuckSpeak(d Duck) {
	d.Gaga()
}

func DuckSwim(d Duck) {
	d.Swim()
}

type Bird struct {
}

func (b Bird) Gaga() {
	fmt.Println("Bird gaga~")
}

func (b Bird) Swim() {
	fmt.Println("Bird Swim!")
}

func main() {
	var bird Bird
	DuckSpeak(bird)  // Bird gaga~
	DuckSwim(bird)   // Bird Swim!
}
```

