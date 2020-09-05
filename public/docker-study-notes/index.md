# Docker学习笔记

<!--more-->

## 安装

首先在安装Docker之前，需要添加Docker安装源

```bash
$ sudo yum-config-manager\
	-add-repo\
	https://download.docker.com/linux/centos/docker-ce-repo
```

**安装最新版本docker**

```bash
$ sudo yum install docker-ce docker-ce-cli containerd.io
```

## 启动

```bash
$ sudo systemctl start docker
```

## 容器原理技术

### Chroot

`chroot` 是在Unix和Linux系统的一个操作，针对正在运行的软件行程和它的子进程，改变它外显的根目录。一个运行在这个环境下，经由`chroot` 设置根目录的程序，他不能够对这个指定根目录之外的文件进行访问动作，不能读取，也不能更改它的内容。

### Namespace

Namespace对内核资源进行隔离，使得容器中的进程都可以在单独的命名空间中运行并且只可以访问当前容器命名空间的资源。

Namesapce可以隔离进程ID、主机名、用户ID、文件名、网络访问和进程间通信等相关资源。

Docker用到以下五种命名空间

#### Pid Namespace

用于隔离进程ID

#### Net Namespace

用于隔离网络接口，使用户拥有独立的IP、路由、端口等

#### Mnt Namespace

用于文件系统挂载点隔离

#### Ipc Namespace

用于信号量、消息队列和共享内存的隔离

#### Uts Namespace

用于主机名和域名的隔离

### Cgroup

`Cgroup` 是一种Linux内核功能，可以限制和隔离进程的资源使用情况（CPU、内存、磁盘I/O、网络等）

### 联合文件系统

`联合文件系统` 又叫 `UnionFS` ，是一种通过创建文件层进程操作的文件系统，常用的联合文件系统有AUFS、Overlay和Devicemapper等

## 镜像

镜像是容器不可或缺的使用条件，是以只读的文件和文件夹组合，是Docker 容器启动的先决条件

## 容器

容器是镜像的运行实体，容器运行着真正的应用进程，容器有初建、运行、停止、暂停和删除五种状态。

在容器内部，无法看到主机上的进程、环境变量、网络等信息

## Docker 重要组件

### RunC

`runC` 是用来运行容器的轻量级工具，是真正用来运行容器的

### Containerd

`containerd` 是通过 `containerd-shim` 启动并管理 `runC`，是从Docker中剥离出来的，是真正管理了Docker生命周期的重要组件
