---
title: "MySQL复习笔记一"
subtitle: ""
date: 2019-05-20T08:00:21+00:00
lastmod: 2019-05-20T08:00:21+00:00
author: ""
authorLink: ""
description: "这是我的第一篇MySQL复习笔记"

tags: ["MySQL"]
categories: ["MySQL"]

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

MySQL复习笔记二： [http://www.yqqy.top/notes/426](http://www.yqqy.top/notes/426)

### 创建数据库（此处按照课本创建学生管理系统）
`create database student`

### 显示创建的数据库

`show databases`

### 使用 `use 数据库` 来选择数据库进行操作

### 给数据库添加 `表`，并为其添加数据

`create table xs(sno char(14) primary key, sname char(20))`

上面这行sql语句意思是创建 xs 这个表，并添加两个数据，sno(学生学号) sname(学生姓名) 并将sno 设置为主键

同理我们创建课程表，选课表

### 展示创建的表

`show tables`

我们就看到了刚创建好的三个表

### 现在我们为此插入数据

我们给kc（课程）这个表插入了三个数据

我们给xk（选课）这个表插入三条数据 **最后一个数据插入sno主键冲突，后面已改 20170201 改为 20170202**

### 我们来看下刚刚插入的数据

`select * from 表名` * 代表所有

先看kc（课程）这个表

然后是xk（选课）这个表

### 我们删除一条数据

`delete from 表名 where 条件`

### 带入条件查询

查询学生成绩为90的信息

### 增加数据库字段

`alter table 表名 add 字段名 `

用 `desc 表名` 查到字段已经加入进来了

### 修改数据

`update 表名 set key = value where 条件`

key 此处代表是字段名，value此处代表是值

### 判断是否查询值为空

在上面查询 xk(选课) 这个表发现前面两个的dept这个字段值是 null

那么我们先看 `select * from xk where dept = null` 是否可以

我们来写正确的sql语句 `is null`

### 连表查询

![mark](https://pic.yqqy.top/blog/20200111/nTNkInm0gFrq.png?imageMogr2/format/webp/interlace/1)

### 带查询条件的连表查询

![mark](https://pic.yqqy.top/blog/20200111/TabvY2r2bO3m.png?imageMogr2/format/webp/interlace/1)

### as 修改连表查询的名称

![mark](https://pic.yqqy.top/blog/20200111/A96dgJljR56U.png?imageMogr2/format/webp/interlace/1)

### 多表之间的join连接 ansi方式

![mark](https://pic.yqqy.top/blog/20200111/fVs48IobMr4F.png?imageMogr2/format/webp/interlace/1)

### 多表之间的连接方式

![mark](https://pic.yqqy.top/blog/20200111/eGKmc2yOLmfA.png?imageMogr2/format/webp/interlace/1)

### 三表联合查询的方法
1. 最笨的办法 依次查询

![mark](https://pic.yqqy.top/blog/20200111/sFXqc8hddS8k.png?imageMogr2/format/webp/interlace/1)

2. 外连接方法 （左连接方法）

![mark](https://pic.yqqy.top/blog/20200111/Qt5NzFGTJa4r.png?imageMogr2/format/webp/interlace/1)

**我们发现这个地方比普通方法多了一条数据，原因是普通方法如果出现null就查不到**

### 创建视图 view

`create or replace view 视图名 AS select语句`

后面的select是一般的查询语句就行，此处是三个表联合查询

![mark](https://pic.yqqy.top/blog/20200111/fo3IFUew2x3K.png?imageMogr2/format/webp/interlace/1)

### 查询视图 

`show tables`

![mark](https://pic.yqqy.top/blog/20200111/47gm7AWUzaev.png?imageMogr2/format/webp/interlace/1)

### 导出数据库 （在cmd窗口）

`mysqldump -u 账号 -p 数据库名字 > 路径`

![mark](https://pic.yqqy.top/blog/20200111/TSTvaEuifJS6.png?imageMogr2/format/webp/interlace/1)

### 导入数据库 (在cmd窗口)

`mysql -u 账号 -p 数据库名字 < 路径`

![mark](https://pic.yqqy.top/blog/20200111/YOCRu0NY9fgg.png?imageMogr2/format/webp/interlace/1)

