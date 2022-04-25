---
title: "Scrapy第一次爬虫（并将数据传到mysql中）"
subtitle: ""
date: 2019-05-27T04:02:48+00:00
lastmod: 2019-05-27T04:02:48+00:00
author: ""
authorLink: ""
description: "记录一下用Python的Scrapy库爬虫"

tags: ["Python"]
categories: ["Python"]

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

实操python的Scrapy库，也就是爬虫很强大的一个库

今天要练习的是一个新闻网，如下

![mark](https://pic.yqqy.top/blog/20200111/z3PT5NqUlXla.png "网站")

## 创建项目

首先我们先在控制台创建一个 `Scrapy` 项目 

`scrapy startproject 项目名称`

![mark](https://pic.yqqy.top/blog/20200111/MO65fedlEQ2U.png "创建项目")

## 添加爬虫域

我们先用 `cd news` 进入爬虫文件中，然后再添加爬虫域 

`scrapy genspider new hxfzzx.com`

![mark](https://pic.yqqy.top/blog/20200111/g2KOWiSHUKCu.png "添加爬虫域")

## 目录结构

看下一下scrapy的代码目录结构

![mark](https://pic.yqqy.top/blog/20200111/4IUhxuhN2rsO.png "目录结构")

- spiders/: 放置spider代码的目录
- new.py: 该项目的python模块
- items.py: 项目中的item文件
- middlewares.py:爬虫中间件
- piplines.py:项目中处理数据行为.如:一般结构化的数据持久化
- settings.py:项目的设置文件

## 分析元素

先在 `items.py` 里面写下爬虫元素

```python
# -*- coding: utf-8 -*-

# Define here the models for your scraped items
#
# See documentation in:
# https://doc.scrapy.org/en/latest/topics/items.html

import scrapy

class NewsItem(scrapy.Item):
    # define the fields for your item here like:
    new_title = scrapy.Field()
    new_time = scrapy.Field()
    new_href = scrapy.Field()
    content_img = scrapy.Field()
    content = scrapy.Field()
```

## 编写脚本

然后在 `new.py` 里面写项目代码

```python
# -*- coding: utf-8 -*-
import scrapy
from news.items import NewsItem

class NewSpider(scrapy.Spider):
    name = 'new'
    allowed_domains = ['hxfzzx.com'] # 爬虫域
    start_urls = ['http://www.hxfzzx.com/news/fzfj/']  # 开始爬取的地址

    def parse(self, response):
        li_list = response.xpath("//div[@class='zw']//li")  # 分析页面元素
        for li in li_list:
            item = NewsItem()
            item["new_title"] = li.xpath("./a/text()").extract_first()  # 分别取出新闻标题，新闻时间，新闻详情页地址
            item["new_time"] = li.xpath("./span/text()").extract_first()
            item["new_href"] = li.xpath("./a/@href").extract_first()
            if item["new_href"] is None:  # 因为此处会爬虫有None的数据，所以过滤一下
                continue

            # 发起一个请求，去获取详情页
            yield scrapy.Request(
                item["new_href"],
                callback=self.parse_detail,
                meta={"item": item}
            )

        # 遍历10页的内容
        next_url = response.xpath("//div[@id='pages']/a[@class='a1'][3]/@href").get()
        if next_url != "/news/fzfj/11.html":
            next_url = "http://www.hxfzzx.com"+next_url
            yield scrapy.Request(
                next_url,
                callback=self.parse
            )

    # 获取详情页信息
    def parse_detail(self, response):
        item = response.meta["item"]
        item["content"] = response.xpath("//div[@class='zw show']").extract()  # 获取详情页的内容，此处将他的div整个都爬了，方便格式
        yield item
```

## 处理数据

最后在 `pipelines.py` 这个文件里写数据处理代码，并将其提交到mysql数据库中，mysql中创建的字段如下

![mark](https://pic.yqqy.top/blog/20200111/ai77k9J0wLxq.png "处理数据")

`pipelines.py` 代码如下：

```python
# -*- coding: utf-8 -*-

# Define your item pipelines here
#
# Don't forget to add your pipeline to the ITEM_PIPELINES setting
# See: https://doc.scrapy.org/en/latest/topics/item-pipeline.html

import pymysql

class NewsPipeline(object):
    def __init__(self):
        # 连接MySQL数据库
        self.connect = pymysql.connect(host='连接ip', user='用户名', password='密码', db='数据库名', port=3306)
        self.cursor = self.connect.cursor()

    def process_item(self, item, spider):
        # 往数据库里面写入数据
        sql = "insert into news(`new_title`, `new_time`, `new_href`, `content`) values (%s, %s, %s, %s)"
        self.cursor.execute(sql, (item['new_title'], item['new_time'], item['new_href'], item['content']))
        self.connect.commit()
        return item

    # 关闭数据库
    def close_spider(self, spider):
        self.cursor.close()
        self.connect.close()
```

## 注意

注意事项，要在 `setting.py` 代码中将pipelines的注释打开

![mark](https://pic.yqqy.top/blog/20200111/R3SBimNNsB0n.png "注意事项")

## 运行

最后我们运行一下代码文件

`scrapy crawl new`

## 效果

效果如下：

![res.gif](https://pic.yqqy.top/blog/20171011444.gif "运行效果")