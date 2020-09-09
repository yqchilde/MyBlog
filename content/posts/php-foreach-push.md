---
title: "php利用foreach给数组指定键值添加内容"
subtitle: ""
date: 2019-08-01T08:44:17+00:00
lastmod: 2019-08-01T08:44:17+00:00
author: ""
authorLink: ""
description: "PHP利用foreach给指定数组根据键去添加内容"

tags: ["PHP"]
categories: ["PHP"]

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

### 栗子如下

```php
$jsonArr = array();
foreach ($res as $value) {
	array_push($jsonArr, array('classId' => $value['classid'], 'className' => $value['classname']));
}
$jsonArr = json_encode($jsonArr, true);
exit(print_r($jsonArr));
```