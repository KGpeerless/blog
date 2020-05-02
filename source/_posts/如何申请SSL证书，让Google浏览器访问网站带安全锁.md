---
title: 如何申请SSL证书，让Google浏览器访问网站带安全锁
keywords:
  - SSL
  - https
  - nignx
  - 部署https
  - nginx SSL
description: 如何申请SSL，将服务器改为https，nginx如何部署ssl
top: 0
tags:
  - SSL
  - https
  - nignx
categories:
  - 服务器
translate_title: >-
  how-to-apply-for-ssl-certificate,-let-google-browser-visit-website-with-security-lock
date: 2018-12-29 00:05:47
---

目前市面上有很多免费可用的SSL证书，单个子域名免费一年，以下是链接。
但是可以多次申请。

<!-- more -->

[阿里云](https://common-buy.aliyun.com/?spm=5176.7968328.1266638..77cb1232rCOaQV&commodityCode=cas#/buy)
[百度云](https://cloud.baidu.com/product/ssl.html)
[腾讯云](https://buy.cloud.tencent.com/ssl?fromSource=trustasiassl)
[七牛云](https://portal.qiniu.com/certificate/apply?ref=www.qiniu.com)

## 进行购买

> 选择Symantec 免费型DV SSL

![购买证书服务](https://img-qiniu.alwayslay.com/如何申请SSL证书，让Google浏览器访问网站带安全锁/20181229121524488.png)

> 跳过上面付款的步骤，开始证书申请

![申请证书](https://img-qiniu.alwayslay.com/如何申请SSL证书，让Google浏览器访问网站带安全锁/20181229121842103.png)


1. 输入证书绑定域名，因为我们买的是单域名版，所以只能填写blog.alwayslay.com这种子域名
2. 验证方式，因为我的域名不在阿里云，所以选择手工DNS验证，不推荐使用文件验证，太麻烦了。


![验证配置信息](https://img-qiniu.alwayslay.com/如何申请SSL证书，让Google浏览器访问网站带安全锁/20181229122634660.png)

> 接下来进入你的域名解析界面

![添加域名解析记录](https://img-qiniu.alwayslay.com/如何申请SSL证书，让Google浏览器访问网站带安全锁/20181229122944873.png)

![点击验证-》提交审核](https://img-qiniu.alwayslay.com/如何申请SSL证书，让Google浏览器访问网站带安全锁/20181229123047478.png)

点击提交审核后耐心等待把，一般1个工作日内就会通知通过的。

## nignx配置
对nginx其他的配置就不多写了，只贴SSL相关的配置的代码。
首先将SSL证书文件上传至服务器
```
server
{
    listen 80;
    listen 443 ssl http2;
    server_name 你的域名;
    index index.html;
    root 项目存放地址;

    ssl_certificate /证书存放地址/证书名称.pem;
    ssl_certificate_key /证书存放地址/证书密钥名称.key;
}
```
至此服务器的配置结束了，进入下一环节

## 网站配置
我们将nginx配置好以后，访问我们的网站虽然是https了，但是可能还是有红色不安全的标记，这个是因为我们项目中还存在着其他http链接的图片、css、js等，将这些链接替换为https，在刷新你的网站就会有安全锁了。

## 最后
拜拜！