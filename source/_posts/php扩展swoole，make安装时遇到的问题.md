---
title: php扩展swoole，make安装时遇到的问题
top: 0
keywords:
  - swoole
  - php
  - make
  - socket
description: php扩展安装swoole，多线程，make安装时的错误
tags:
  - swoole
  - php
categories:
  - php
translate_title: problems-encountered-in-the-installation-of-php-extension-tool-and-make
date: 2019-01-16 14:50:44
---
使用的系统是mac，每个人安装时可能都不一样，以下是我碰到的问题
最开始我是使用brew安装环境的，但是在执行下面这句话的时候提示："No formula found for "swoole"."
然后使用pecl进行安装 "install failed"
最后选择make编译安装
<!-- more -->

### 下载并编译
```
git clone git@gitee.com:swoole/swoole.git
cd swoole
phpize
./configure
make
```
### 错误
```
/Users/xxx/package/swoole/src/os/sendfile.c:23:5: error: conflicting types for 'swoole_sendfile'
int swoole_sendfile(int out_fd, int in_fd, off_t offset, off_t size)
/Users/xxx/package/swoole/include/swoole.h:2166:5: note: previous declaration is here
int swoole_sendfile(int out_fd, int in_fd, off_t offset, size_t size);
```
然后我就去看了这两个文件
```
# sendfile.c
int swoole_sendfile(int out_fd, int in_fd, off_t *offset, off_t size)

# swoole.h
#if defined(HAVE_KQUEUE) || !defined(HAVE_SENDFILE)
int swoole_sendfile(int out_fd, int in_fd, off_t *offset, size_t size);
#else
```

然后我发现最后的这个size的类型不一致，我尝试将下面的 # size_t 改为 off_t
最后make成功了

### 最后
^_^