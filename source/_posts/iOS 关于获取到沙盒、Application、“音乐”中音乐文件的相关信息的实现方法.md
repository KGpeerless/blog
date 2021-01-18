---
title: iOS 关于获取到沙盒、Application、“音乐”中音乐文件的相关信息的实现方法
top: 0
keywords:
  - iOS
description: iOS 关于获取到沙盒、Application、“音乐”中音乐文件的相关信息的实现方法
tags:
  - iOS
categories:
  - iOS
translate_title: >-
  how-to-get-the-information-of-music-files-in-sandbox,-application-and-music-by-ios
date: 2020-11-25 14:02:34
---

作者：[法克君](https://www.jianshu.com/u/10202a801ac6)
发布时间：2018.03.22 09:47:07

新项目需要通过iTunes和WiFi传输的方式向APP中传入音乐文件进行播放的功能。刚开始的时候只能获取到歌曲名和相应的路径，之后通过资料实现了相关功能。现在已经可以获取到音乐的歌名，歌手，专辑名，大小，时长和播放路径等信息。在这里进行一个记录，方便以后查看。同时将方法封装起来，上传到Git上，方便自己以后查看，也可以给需要的人提供借鉴。[点我跳转](https://github.com/NSLoger/GetSandboxMusic)

<!-- more -->

### 说明
类中有三个方法，分别是获取手机中iPod库中的音乐，获取手机中本APP沙盒中的音乐，获取手机中本APP自带的音乐。

### 获取iPod库中的音乐方法
```
+(NSArray *)getLocalMusicListMsg;
```

### 获取沙盒中音乐信息
```
+(NSArray *)getDucumentMusicListMsg;
```

### 获取APP中自带音乐
```
+(NSArray *)getApplicationMusicListMsg;
```