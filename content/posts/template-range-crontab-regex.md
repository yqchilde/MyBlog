---
title: "Go的template模板关于range换行和crontab的简单正则表达式"
subtitle: ""
date: 2021-04-04T17:39:04+08:00
lastmod: 2021-04-04T17:39:04+08:00
author: ""
authorLink: ""
description: "1. 在使用Go的Template时，在end后下方出现了一行空白的坑，很影响强迫症心情。<br>2. 记录一个简单的crontab正则表达式如何编写。"

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
lightgallery: true
license: ""
---

<!--more-->

## template range

```go
{
  {{ range $_, $cron := .CronList }}
  {{- $cron }}
  {{ end }}
} >> /scripts/docker/merged_list_file.sh
```

效果如下图：

![](https://pic.yqqy.top/blog/20210404174920.png?imageMogr2/format/webp/interlace/1 "图一")

出现了一行空白，接着修改如下：

```go
{
  {{ range $_, $cron := .CronList }}
  {{- $cron }}
  {{ end -}}
} >> /scripts/docker/merged_list_file.sh
```

![image-20210404175049733](https://pic.yqqy.top/blog/20210404175051.png?imageMogr2/format/webp/interlace/1 "图2")

正确修改方式如下，这个坑还是不少人会踩到：

```go
{
  {{- range $_, $cron := .CronList}}
  {{ $cron -}}
  {{- end }}
} >> /scripts/docker/merged_list_file.sh
```

![image-20210404175203952](https://pic.yqqy.top/blog/20210404175205.png?imageMogr2/format/webp/interlace/1 "图三")

## crontab regex

周末写工具自动拉取几个作者的脚本遇到需要提取脚本中的crontab表达式，研究了一下正则，因为我发现不同作者写脚本方式不一样，所以最稳的还是正则来匹配，之所以称为**简单匹配**，是因为没有做成真正的crontab是否有效的匹配，只是**匹配**格式，代码如下：

```go
\*                                       // 匹配*
\?                                       // 匹配?
[0-9]{1,2}                               // 匹配单个时间
[0-9]{1,2}\-[0-9]{1,2}                   // 匹配时间段
[0-9]{1,2}\-[0-9]{1,2}\/[0-9]{1,2}       // 匹配每隔多久的时间段
([0-9]{1,2}\,?)*                         // 匹配,号隔开的多个时间
([0-9]{1,2}\,?)*\-[0-9]{1,2}             // 匹配,号和-号组成的时间段
([0-9]{1,2}\,?)*\-[0-9]{1,2}\/[0-9]{1,2} // 匹配,号、-号和/号组成的间隔多个时间段
```

组合在一起装成出现5次，代码如下

```go
(((\*|\?|[0-9]{1,2}|[0-9]{1,2}\-[0-9]{1,2}|[0-9]{1,2}\-[0-9]{1,2}\/[0-9]{1,2}|([0-9]{1,2}\,?)*|([0-9]{1,2}\,?)*\-[0-9]{1,2}|([0-9]{1,2}\,?)*\-[0-9]{1,2}\/[0-9]{1,2})+[\s]){5})
```

效果如下，如果有人看到了这篇文章，恰好这个正则不能匹配你的crontab，麻烦留一下正则作为我的补充。

![image-20210404175645634](https://pic.yqqy.top/blog/20210404175647.png?imageMogr2/format/webp/interlace/1 "图4")

> 2021-06-05更新最新的crontab表达式

```go
[^\s\:]((\*|[0-9]{1,2}|[0-9]{1,2}\-[0-9]{1,2}|[0-9]{1,2}\-[0-9]{1,2}\/[0-9]{1,2}|([0-9]{1,2}\,?)*|([0-9]{1,2}\,?)*\-[0-9]{1,2}|([0-9]{1,2}\,?)*\-[0-9]{1,2}\/[0-9]{1,2})+[ ]){4}(\*|[0-9]{1,2}|[0-9]{1,2}\-[0-9]{1,2}|[0-9]{1,2}\-[0-9]{1,2}\/[0-9]{1,2}|([0-9]{1,2}\,?)*|([0-9]{1,2}\,?)*\-[0-9]{1,2}|([0-9]{1,2}\,?)*\-[0-9]{1,2}\/[0-9]{1,2})
```