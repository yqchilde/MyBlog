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

Docker用到以下五种命名空间：

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

## 常用命令

```bash
// 拉取镜像
docker pull

// 重命名镜像
docker tag

// 查看镜像
docker image ls 或 docker images

// 删除镜像
docker rmi

// 构建镜像
1. docker build 基于Dockerfile构建镜像
2. docker commit 基于已经运行的容器提交为镜像
```

## 拉取镜像

Docker镜像的拉取使用 `docker pull` 命令，命令格式一般为

```bash
$ docker pull [Registry]/[Repository]/[Image]:[Tag]
```

* `Registry` 为注册服务器，Docker默认会从docker.io拉取镜像，如果你有自己的镜像仓库，可以把Registry替换为自己的注册服务器
* `Repository` 为镜像仓库，通常把一组相关联的镜像归为一个镜像仓库，`library` 为Docker默认的镜像仓库
* `Image` 为镜像名称
* `Tag` 为镜像的标签，如果不指定拉取镜像的标签，默认为 `latest`

## 过滤镜像

![image-20200909204607924](https://pic.yqqy.top/blog/image-20200909204607924.png?imageMogr2/format/webp/interlace/1 "过滤镜像")

## 重命名镜像

如果想自定义镜像名称或者推送镜像到其他镜像仓库，你可以使用 `docker tag` 命令将镜像重命名。`docker tag` 的命令格式为

```bash
$ docker tag [SOURCE_IMAGE][:TAG] [TARGET_IMAGE][:TAG]

Ps.
$ docker tag telemed:latest newtelemed:telemed
```

在执行完如上命令后，再次查看镜像就会发现多了一个仓库名为 `newtelemed` 的镜像，但是它和 `telemed` 的 `IMAGE ID` 是完全一样的，实际上它们指向了同一个镜像文件，只是别名不一样而已

## 构建镜像

构建镜像主要有两种方式：

1. 使用 `docker commit` 命令从运行中的容器提交为镜像
2. 使用 `docker build` 命令从 Dockerfile 构建镜像

### docker commit

1. 先进入镜像中

```bash
// 没有 `[]`
$ docker run -rm --name=[容器名] -it [容器名] sh
/ #
```

执行完上面的命令后，当前窗口会进入指定容器名的容器中，在容器中，执行以下命令创建一个文件并写入内容

```bash
/ # touch hello.txt && echo "I Love Docker." > hello.txt
/ #
```

此时在容器的根目录下，已经创建了一个hello.txt文件，并写入了"I Love Docker."。下面，重新打开一个命令行窗口，运行以下命令提交镜像

```bash
$ docker commit [TARGET_IMAGE] [TARGET_IMAGE][:TAG]
```

再次使用 `docker images` 就可以看到生成了 `[TARGET_IMAGE][:TAG]`这个镜像

### docker build

这种方式是最重要的也是最常用的镜像构建方式：Dockerfile。Dockerfile是一个包含了用户所有构建命令的文本。通过 `docker build` 命令可以从Dockerfile生成镜像

使用Dockerfile构建镜像具有以下特性：

* Dockerfile的每一行命令都会生成一个独立的镜像层，并且拥有唯一的ID
* Dockerfile的命令是完全透明的，通过查看Dockerfile的内容，就可以知道镜像是如何一步步构建的
* Dockerfile是纯文本的，方便跟随代码一起存放在代码仓库并做版本管理

## Dockerfile常用指令

| 指令       | 简介                                                         |
| :--------- | :----------------------------------------------------------- |
| FROM       | Dockerfile除了注释第一行必须是 FROM ，FROM 后面跟镜像名称，代表我们要基于哪个基础镜像构建我们的容器 |
| RUN        | RUN 后面跟一个具体的命令，类似于 Linux 命令行执行命令。      |
| ADD        | 拷贝本机文件或者远程文件到镜像内                             |
| COPY       | 拷贝本机文件到镜像内                                         |
| USER       | 指定容器启动的用户                                           |
| ENTRYPOINT | 容器的启动命令                                               |
| CMD        | CMD 为 ENTRYPOINT 指令提供默认参数，也可以单独使用 CMD 指定容器启动参数 |
| ENV        | 指定容器运行时的环境变量，格式为 key=value                   |
| ARG        | 定义外部变量，构建镜像时可以使用 build-arg = 的格式传递参数用于构建 |
| EXPOSE     | 指定容器监听的端口，格式为 [port]/tcp 或者 [port]/udp        |
| WORKDIR    | 为 Dockerfile 中跟在其后的所有 RUN、CMD、ENTRYPOINT、COPY 和 ADD 命令设置工作目录。 |

通过如下实例来熟悉一下Dockerfile

```docker
FROM centos:7
COPY nginx.repo /etc/yum.repos.d/nginx.repo
RUN yum install -y nginx
EXPOSE 80
ENV HOST=mynginx
CMD ["nginx", "-g", "daemon off;"]
```

分析一下上面的Dockerfile

* 第一行表示基于 centos:7 这个镜像来构建自定义镜像。这里需要注意，每个Dockerfile的第一行除了注释都必须以FROM开头
* 第二行表示拷贝本地文件 nginx.repo 文件到容器内的 `/etc/yum.repos.d` 目录下。这里拷贝 nginx.repo 文件是为了添加nginx的安装源
* 第三行表示在容器内运行 `yum install -y nginx` 命令，安装nginx服务到容器内，执行完第三行命令，容器内的nginx已经安装完成
* 第四行声明容器内业务（nginx）使用80端口对外提供服务
* 第五行定义容器启动时的环境变量 `HOST=mynginx`，容器启动后可以获取到环境变量HOST的值为mynginx
* 第六行定义容器的启动命令，命令格式为json数组。这里设置了容器的启动命令为nginx，并且添加了nginx的启动参数 `-g 'daemon 0ff;'`，使nginx以前台的方式启动。

## 镜像的实现原理

其实Docker镜像是由一系列镜像层（layer）组成的，每一层代表了镜像构建过程中的一次提交。下面以一个镜像构建的Dockerfile来说明镜像是如何分层的。

```docker
FROM busybos
COPY test /tmp/test
RUN mkdir /tmp/testdir
```

上面的Dockerfile由三部组成

第一行基于 `busybox` 创建一个镜像层

第二行拷贝本地 `test` 文件到镜像内

第三行在 `/test` 文件夹下创建一个目录 `testdir`

为了验证镜像的存储结构，我们使用 `docker build` 命令在上面Dockerfile所在目录构建一个镜像：

```bash
$ docker build -t mybusybox .
```

这里的Docker使用的是overlay2文件驱动，进入到 `/var/lib/docker/overlay2` 目录下使用 `tree .` 命令查看产生的镜像文件

// todo 

分层的结构使得Docker镜像非常轻量，每一层根据镜像的内容都有一个唯一的ID值，当不同的镜像之间有相同的镜像层时，便可以实现不同的镜像之间共享镜像层的效果

总结一下，Docker镜像是静态的分层管理的文件组合，镜像底层的实现依赖于联合文件系统（UnionFS）。每一层代表了镜像构建过程中的一次提交，当我们需要修改镜像内的某个文件时，只需要在当前镜像层的基础上新建一个镜像层，并且只存放修改过的文件内容。
