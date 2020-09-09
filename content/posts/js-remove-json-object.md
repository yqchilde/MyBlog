---
title: "Js删除json中的对象"
subtitle: ""
date: 2019-07-31T12:55:43+00:00
lastmod: 2019-07-31T12:55:43+00:00
author: ""
authorLink: ""
description: "思路就是先将json转换成数组，然后在循环遍历数组，然后再用splice函数进行删除数组"

tags: ["Javascript"]
categories: ["Tips"]

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

### 思路

思路就是先将json转换成数组，然后在循环遍历数组，然后再用splice函数进行删除数组

### 举个栗子

```javascript
let delId = 3;
let jsonArr = ['{"userList": [{"name": "yqchilde", "userId": "2", "joinTime": "07月31日"}, {"name": "test404", "userId": "3", "joinTime": "07月30日"}], "className": "计算机科学一班"}']
jsonArr = JSON.parse(jsonArr);
for (let i = 0; i < jsonArr.userList.length; i++) {
	if (delId == jsonArr.userList[i].userId) {
		jsonArr.userList.splice(i, 1);
	}
};
jsonArr = JSON.stringify(jsonArr);
console.log(jsonArr)

//结果如下
// {"userList":[{"name":"yqchilde","userId":"2","joinTime":"07月31日"}],"className":"计算机科学一班"}
```