# 使用GitHub Actions部署Hugo到阿里云

<!--more-->

## 了解

在使用 `GitHub Actions` 前先要了解什么是GitHub Actions

推荐看阮一峰老师的文章：[GitHub Actions入门教程](http://www.ruanyifeng.com/blog/2019/09/getting-started-with-github-actions.html)

## 熟悉流程

1. 首先我们在github创建自己的私有仓库，用来推Hugo生成的 `public` 文件夹
2. 利用 `GitHub Actions` 远程连接 `SSH`,执行删除服务器的绑定的web目录（即运行目录）
3. 利用 `GitHub Actions` SCP服务推送Github仓库下的 `public` 到远端服务器的web目录

## 创建Actions

前面我们已经提到先将Hugo生成的public文件夹整体推到了Github，接下来就是创建 `GitHub Actions`

![img](https://pic.yqqy.top/blog/1.png?imageMogr2/format/webp/interlace/1 "创建Actions")

## 配置文件

连接SSH的选择一个Star比较多的，如下图

![img](https://pic.yqqy.top/blog/2.png?imageMogr2/format/webp/interlace/1 "选择市场插件")

这边我贴一下我配置好的

```yml
# This is a basic workflow to help you get started with Actions

name: CI

on:
  push:
    branches: [ master ]

jobs:
  build:
    runs-on: ubuntu-latest
    
    steps:
      - name: Check Out
        uses: actions/checkout@v2
          
      # 使用SSH远程连接
      - name: SSH Remote Commands
        uses: appleboy/ssh-action@master
        with:
          host: ${{ secrets.ALIYUN_HOST }}
          username: ${{ secrets.ALIYUN_USER }}
          password: ${{ secrets.ALIYUN_PASSWORD }}
          script: bash /root/blog_del.sh
          
      # 用SCP将文件传到远端服务器
      - name: SCP Files to aliyun
        uses: appleboy/scp-action@master
        with:
          host: ${{ secrets.ALIYUN_HOST }}
          username: ${{ secrets.ALIYUN_USER }}
          password: ${{ secrets.ALIYUN_PASSWORD }}
          source: "./public"
          target: ${{ secrets.ALIYUN_PATH }}
```

{{< admonition note "注意" >}}

`${{ secrets.xxx }}` 是官方设置秘钥的方法

${{ secrets.ALIYUN_HOST }} 服务器地址

${{ secrets.ALIYUN_USER }} 服务器用户名

${{ secrets.ALIYUN_PASSWORD }} 服务器密码

${{ secrets.ALIYUN_PATH }} 博客运行目录

第24行的运行脚本是指在连接到远端服务器时，先去删除服务器上原本的文件

{{< /admonition >}}

### 删除远端目录

![img](https://pic.yqqy.top/blog/20200830210144.png?imageMogr2/format/webp/interlace/1 "删除远端目录")

```bash
if [ ! -d "远端目录" ];then
echo "文件夹不存在"
else
rm -rf 远端目录
fi
```



### secrets添加

![img](https://pic.yqqy.top/blog/20200830204924.png?imageMogr2/format/webp/interlace/1 "secrets添加位置")

## 推送

点击右上角保存即可，这样每次只需要在本地将文章推到GitHub就可以自动部署到服务器了。

![img](https://pic.yqqy.top/blog/20200830205650.png?imageMogr2/format/webp/interlace/1 "运行日志")


