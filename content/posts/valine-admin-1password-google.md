---
title: "Valine-Admin、1Password、荣耀平板5谷歌三件套"
subtitle: ""
date: 2020-03-22T13:45:11+00:00
lastmod: 2020-03-22T13:45:11+00:00
author: ""
authorLink: ""
description: "周末灌水，上午修补Valine-Admin，中午白嫖1Password，下午处理荣耀平板5的谷歌三件套，Valine-Admin: 不能每日邮件遗漏补发，原因：函数写半个？荣耀平板5最新系统9.1.0.175无法获取谷歌服务？"

tags: ["mood"]
categories: ["Notes"]

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

> 周末灌水，上午修补Valine-Admin，中午白嫖1Password，下午处理荣耀平板5的谷歌三件套
>
> Valine-Admin: 不能每日邮件遗漏补发，原因：函数写半个？
>
> 荣耀平板5最新系统9.1.0.175无法获取谷歌服务，原因：贸易战

### Valine-Admin

{{indent}}博客用的是Valine的三方评论，后台模板用的是zhaojun改完的Valine-Admin，基于免费版Leancloud账户的问题，每日最多运行18小时，所以在0-7点属于休眠状态，这个时刻如果有人评论了，虽然是会唤醒服务，但是是收不到邮件的，就需要邮件补发功能了，经过遗漏掉一条邮件排查掉，[**zhaojun**](https://github.com/zhaojun1998/Valine-Admin) fork [**panjunwen**](https://github.com/DesertsP/Valine-Admin) 的项目之后改完漏掉一个函数`sendNotification`，唉，fork了不要魔改啊，改的都丢三落四，整理完的 `cloud.js` 如下：

```javascript
const AV = require('leanengine');
const mail = require('./utilities/send-mail');
const Comment = AV.Object.extend('Comment');
const request = require('request');
function sendNotification(currentComment) {
    // 通知站长
    mail.notice(currentComment);

    // AT评论通知
    let pid = currentComment.get('pid');

    if (!pid) {
        console.log("这条评论没有 @ 任何人");
        return;
    }

    // 通过被 @ 的评论 id, 则找到这条评论留下的邮箱并发送通知.
    let query = new AV.Query('Comment');
    query.get(pid).then(function (parentComment) {
        if (parentComment.get('mail')) {
            mail.send(currentComment, parentComment);
        } else {
            console.log(currentComment.get('nick') + " @ 了" + parentComment.get('nick') + ", 但被 @ 的人没留邮箱... 无法通知");
        }
    }, function (error) {
        console.warn('好像 @ 了一个不存在的人!!!');
    });
}

AV.Cloud.afterSave('Comment', function (request) {
    let currentComment = request.object;

    return sendNotification(currentComment)
});

AV.Cloud.define('resend_mails', function(req) {
    let query = new AV.Query(Comment);
    query.greaterThanOrEqualTo('createdAt', new Date(new Date().getTime() - 24*60*60*1000));
    query.notEqualTo('isNotified', true);
    // 如果你的评论量很大，可以适当调高数量限制，最高1000
    query.limit(200);
    return query.find().then(function(results) {
        new Promise((resolve, reject)=>{
            count = results.length;
            for (var i = 0; i < results.length; i++ ) {
                sendNotification(results[i]);
            }
            resolve(count);
        }).then((count)=>{
            console.log(`昨日${count}条未成功发送的通知邮件处理完毕！`);
        }).catch((err)=>{
        	console.log(err);
        });
    });
});

AV.Cloud.define('self_wake', function(req) {
    request(process.env.ADMIN_URL, function (error, response, body) {
        console.log('自唤醒任务执行成功，响应状态码为:', response && response.statusCode);
    });
})
```

#### Leancloud免费服务的问题

{{indent}}总所周知，免费服务有限制，比如唤醒只给了15s时间，所以容易启动不了

![image-20200322203850365](https://pic.yqqy.top/blog/20200322204205.png?imageMogr2/format/webp/interlace/1)

{{indent}}解决办法：使用 `linux` 的 `Crontab`自行唤醒，即不用Leancloud定时任务唤醒，只用其补发邮件

### 白嫖1Password

{{indent}}看的某乎的教程直接添链接了，[白嫖1Password家庭版一年~真香!](https://zhuanlan.zhihu.com/p/87575555)

### 荣耀平板5获取谷歌三件套

#### 神奇操作

{{indent}}很神奇，可能是由于贸易战或者缓解原因，有的机型自带GMS，有的不带需要自己装，解决办法是花粉俱乐部参考得到的（即东拼西凑），以下是获取之前的配置

- 安卓9 
- Magic UI 2.1.0
- 版本号 9.1.0.175

**方法：**

{{indent}}**先通过华为手机助手降级到9.0.x.122，然后可以升级，升级到9.1.0.128😀必须128**

{{indent}}安装谷歌服务方法（需PC辅助）

{{indent}}教程适用机型有：华为Mate30系列、华为畅享10Plus、华为nova5/nova5 Pro、华为novai/novai Pro、华为麦芒8、华为畅享9系列/Max等

{{indent}}荣耀9X、荣耀20i、荣耀8X/8X Max、荣耀10青春版等

{{indent}}荣耀平板5、华为平板M6等

{{indent}}助手是用来激活设备管理器的，只要激活了就行了，不用担心惊叹号那些问题。

**步骤如下**

1. 下载并解压谷歌服务包

2. 连接Hisuite（pc华为手机助手）点击数据恢复，找到刚才解压的文件，只恢复应用数据

3. 恢复完成后，查看桌面是否有`谷歌服务助手`APP

4. 打开谷歌服务助手，激活设备管理器   其他不用理会（感叹号的不用管，直接退出APP）

5. 激活完成后重启手机，下载`Go谷歌安装器`，安装三件套，完成，如果可以打开`谷歌play`并可以登陆，那就恭喜了

#### 咦~真香

![image-20200322212144823](https://pic.yqqy.top/blog/20200322212209.png?imageMogr2/format/webp/interlace/1)

![image-20200322212204969](https://pic.yqqy.top/blog/20200322212217.png?imageMogr2/format/webp/interlace/1)

#### 所需工具

- [谷歌服务](https://pan.baidu.com/s/1_8Ic31N8hv0TUKyVpynXaQ)  提取码：~~9rvb~~
- Go谷歌安装器，酷安和豌豆荚都有