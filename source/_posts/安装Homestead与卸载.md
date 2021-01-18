---
title: 安装Homestead与卸载
top: 0
keywords:
  - Laravel
  - Homestead
  - 安装环境
  - 虚拟机
  - 卸载环境
  - Vagrant
  - VirtualBox
description: 安装Homestead虚拟机环境教程以及完全删除教程
tags:
  - Homestead
  - Vagrant
  - VirtualBox
categories:
  - 虚拟机
translate_title: install-homestead-and-uninstall
date: 2019-02-15 16:39:39
---

macOS系统下的安装介绍，windows系统的话可以参考一下
macOS系统在下载VirtualBox的时候要看一下本机的系统。

<!-- more -->

## 下载VirtualBox
VirtualBox：[下载地址](https://www.virtualbox.org/wiki/Downloads)
目前本教程所使用的Homestead版本是7.8.0，支持VirtualBox5.2.x版本的，以及低版本（我忘记了有哪几个版本）。

macOs Mojave系统需要使用VirtualBox5.2 高版本系列，低版本的安装时会提示系统不支持。

## 下载Vagrant
Vagrant：[下载地址](https://www.vagrantup.com/downloads.html)

## 下载Homestead Box
[下载地址](http://download.fsdhub.com/lc-homestead-6.1.1-2018090400.zip)
下载完成后，进行解压
```
> cd lc-homestead-6.1.1-2018090400
> vagrant box add metadata.json
```

## 下载Homestead管理脚本
使用git下载homestead管理脚本
```
> cd ~
> git clone https://git.coding.net/summerblue/homestead.git Homestead
> cd Homestead
> git checkout v7.8.0
> bash init.sh
```
编辑homestead配置文件的放在另外一篇。

## Vagrant命令
| 命令 | 说明 |
| ------ | ------ |
| vagrant up | 启动 |
| vagrant halt | 关闭 |
| vagrant ssh | 通过ssh连接虚拟机 |
| vagrant provision | 重新应用vagrant设置 |
| vagrant reload | 重启虚拟机 |

这些命令可以一起用，比如`vagrant reload --provision`，`vargant up && vagrant ssh`