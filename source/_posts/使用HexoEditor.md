---
title: 使用HexoEditor
top: 0
tags:
  - HexoEditor
  - markdown
  - Electron
categories:
  - 前端
translate_title: using-the-hexoeditor
keywords:
  - hexo-editor
  - hexo
  - hexo-admin
  - hexo后台
description: hexo-editor，hexo，hexo后台，markdown客户端，可以上传图片至七牛云、腾讯云，一键部署
date: 2018-12-26 16:23:04
---

HexoEditor是基于Electron开发的。
可以使用markdown语法并及时预览。
并可以一键发布。
在页面美观度上我认为远胜于hexo-admin。

<!-- more -->

## 外观
![](https://img-qiniu.alwayslay.com/使用HexoEditor/20181227022020408.png)

![](https://img-qiniu.alwayslay.com/使用HexoEditor/20181227014233026.png)

## 克隆仓库
地址：https://github.com/zhuzhuyule/HexoEditor.git

## 安装模块
```
npm install
```

## 运行
```
# 执行后会打开客户端程序
npm start
```

## 为了方便运行，可以设置别名
```
# 我使用的是zsh
vi ~/.zshrc

# 修改下面这一句，并粘贴到文件中
alias blog_editor='cd /项目地址/ && npm start'

# 配置生效
source ~/.zshrc

# 再次启动客户端
blog_editor
```
