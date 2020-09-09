---
title: php与js的时间戳相关问题
subtitle: ""
date: 2019-08-09T00:50:43+00:00
lastmod: 2019-08-09T00:50:43+00:00
author: ""
authorLink: ""
description: "众所周知php的 `time` 函数生成的是10位的时间戳，为了方便与js进行处理，js是13位时间戳，记录下转换方法"

tags: ["PHP","Javascript"]
categories: ["PHP"]

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

### php生成13位时间戳

众所周知php的 `time` 函数生成的是10位的时间戳，为了方便与js进行处理，js是13位时间戳，转化函数如下：

```php
/**
* 生成13位时间戳
* @return float
*/
public function getMillisecond() {
	list($t1, $t2) = explode(' ', microtime());
	return (float)sprintf('%.0f',(floatval($t1)+floatval($t2))*1000);
}
```

### js处理10位/13位时间戳转换成时间

```javascript
timestampToTime: function(timestamp, type=1) {
	var date = new Date(timestamp * 1000);//时间戳为10位需*1000，时间戳为13位的话不需乘1000
	let Y = date.getFullYear() + '.';
	let M = (date.getMonth()+1 < 10 ? '0'+(date.getMonth()+1) : date.getMonth()+1) + '.';
	let D = date.getDate() + ' ';
	let h = date.getHours() + ':';
	let m = date.getMinutes()
	let s = date.getSeconds();
	if (type == 1) { //全格式
		return Y+M+D+h+m+s;
	} else if (type == 2) { //年月日
		return Y+M+D;
	} else if (type == 3) { //时分秒
		return h+m+s;
	}
}
```