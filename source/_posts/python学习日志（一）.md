---
title: python学习日志（一）/ 安装篇
top: 0
keywords:
  - python
description: python的学习日志
tags:
  - python
categories:
  - python
translate_title: python-learning-log-(i)-/-installation
date: 2021-10-25 16:56:27
---

记录一下python的学习过程

python 3.9

<!-- more -->

## python
`macOS` 本身是自带`python`的几个版本，我是安装的最新的3.9

### 安装
我这里使用的是homebrew安装的
`brew install python3.9`

安装完成后要添加环境变量和别名，以引导系统使用哪个版本的python

```
> vi ~/.zshrc
# 如果没有安装zshrc的话，使用以下这句
> vi ~/.bash_profile

# 添加这一行
export PATH=${PATH}:/usr/local/Cellar/python3/3.6.2/bin

# 配置文件更新
> source ~/.zshrc
# 同上
> source ~/.bash_profile
```

安装完成后，进行版本验证

```
> python
> Python 3.9.1 (default, Jan  8 2021, 17:15:36)
[Clang 11.0.0 (clang-1100.0.33.17)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>>
```

出现以上信息为安装完成，并且正确使用3.9版本python

### 虚拟环境
使用虚拟环境的原因，是因为python的应用程序有时需要使用特定版本的库，比如应用程序A需要使用特定模块的1.0版本，应用程序B使用2.0版本，此问题的解决方案就是创建虚拟环境。

```
python3 -m venv tutorial-env
```

这会创建`tutrial-env`目录，并且里面包含python解释器

运行这个虚拟环境，windows上
```
tutorial-env\Scripts\activate.bat
```
在Unix或者MacOS上，运行：
```
source tutorial-env/bin/activate
```

### pip管理包

> 关于pip search

pip search 命令无法使用了，从2020年12月开始， search功能就开始出现搜索慢和断断续续的失败问题。

原因是search搜索后端服务查询请求量激增(不确定是恶意使用)达到了一定瓶颈，而目前的搜索能力无法满足这样的请求能力，终于导致了search服务不可用。

所以，Python才会决定暂停search功能，而随着时间的推移，现有的搜索功能实现只能通过更换一种新的(或者扩展)搜索能力(主机、或者服务架构)以满足search服务，但这不是短时间、或者需要投入很多工作量和资金才能解决。 到了2021年5月问题依然存在，所以做了一个决定永久禁用XMLRPC搜索功能。

#### 使用国内源
> linux

如果本地没有`~/.pip/`这个目录，就手动创建一个
```
mkdir ~/.pip
touch ~/.pip/pip.conf

vi ~/.pip/pip.conf
# 输入以下内容
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
```

#### 安装常用第三方包
Requests，Http请求包
```
pip install requests
```

bs4，将复杂的HTML文档转换成一个树形结构的DOM
```
pip install beautifulsoup4
```

pandas 数据结构分析
```
pip install pandas
```
