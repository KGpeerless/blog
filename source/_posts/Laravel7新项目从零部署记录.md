---
title: Laravel7新项目从零部署记录
top: 0
keywords:
  - php
  - Laravel
  - dcat
description: 记录一下新项目的部署日志，方便日后维护项目，避免版本更迭之后忘记了
tags:
  - php
  - Laravel
  - 部署
categories:
  - php
  - Laravel
  - dcat
translate_title: larave7-new-project-zero-deployment-record
date: 2021-09-24 10:39:56
---

在此记录一下新项目从零开始的部署日志，方便日后的维护。避免版本更迭之后忘记了。

#### Homestead开发环境
php 7.4
mysql 8.0.26
laravel 7.x
dcat 2.x

#### 服务器
Ubuntu 20.04.3

<!-- more -->

# 简介
## 说明
`$ 前缀为服务器命令，> 为本地命令`，本篇不具有教学性，只是个人的记录。

# Laravel项目安装到部署

## 1.Laravel项目安装
```
> composer global require laravel/installer
> composer create-project --prefer-dist laravel/laravel 项目名称 7.*
```

安装完成`laravel`之后，需要配置`env`，如果该项目根目录下没有的话，`cp .env.example .env`

```
// 配置数据库
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=xxx
DB_USERNAME=homestead
DB_PASSWORD=secret
```
## 2.dcat-admin安装
安装 `dcat-admin`
```
> cd {项目名称}
> composer require dcat/laravel-admin:"2.*" -vvv
> php artisan admin:publish
> php artisan admin:install
```

1. `app/Admin/routes.php` 文件用来配置后台路由。`app/Admin/bootstrap.php` 是 `dcat-admin` 的启动文件，使用方法请参考文件里面的注释.
2. `app/Admin/Controllers` 目录用来存放后台控制器文件，该目录下的 
3. `HomeController.php` 文件是后台首页的显示控制器，`AuthController.php` 为后台管理员登录鉴权控制器。
4. `app/Admin/Metrics/Examples` 里面存放的是数据统计卡片(Metric Card) 的示例代码.

#### 语言包
语言包文件在 `/resources/lang` 下

## dcat-admin config/admin.php 一些文件配置说明
1. `'helpers' => [ 'enable' => true ]` 上线前要改为false，将后台中的开发工具菜单隐藏
2. 如果有使用https，那么这个`'https' => env('ADMIN_HTTPS', false)`要在`.env`文件中增加一行`ADMIN_HTTPS=true`，否则登录会有问题

# 服务器配置

## 服务器购买
> 比较推荐的两家云服务器商

[Ucloud]('https://www.ucloud.cn/') 优刻得是我目前正在用的，这家公司的活动比较多，像618、双11、双12等等都会有力度比较大的活动，像之前买了一台测试服务器，2核2G一年是60多。

[阿里云]('https://www.aliyun.com/')作为大厂服务器也是比较靠得住，但是就是活动比较匮乏。

[腾讯云]('https://cloud.tencent.com/')的话没有使用过，不予评价

[百度云]('https://www.baidu.com/s?ie=utf-8&f=8&rsv_bp=1&tn=baidu&wd=%E5%82%BB%E9%80%BC%E7%BD%91%E7%AB%99&oq=%25E5%2582%25BB%25E9%2580%25BC&rsv_pq=ca1702fa00043f10&rsv_t=d629jI2rGOdLI9pKo4gwCfNvoC4zqlaAWasWtBUuK43zA49%2FBdgOtJJQuzs&rqlang=cn&rsv_enter=1&rsv_dl=tb&rsv_sug3=10&rsv_sug1=1&rsv_sug7=100&rsv_sug2=0&rsv_btype=t&inputT=1359&rsv_sug4=1457')极度不推荐，真是🌶️🐓站，你但凡在他家办了什么业务，一年四季没完没了给你打电话，哪怕你不用他家的服务器了，也要一直问你再用谁家的，多少钱。但是！！第二天还tm会给你打电话问你昨天问你的问题，服务器买的谁家。永远不会给你做备注，没完没了打。

## 服务器配置ssh-key，快速登录
配置ssh-key之后可以进行快速的登录，不再需要密码。

```
// 生成ssh-key 服务器和本地都需要执行该命令，本地如果执行过则不需要，服务器的ssh用于之后的github项目配置
$ ssh-keygen -t rsa -C "your_email@example.com"

// 复制本地ssh-key
> cat ~/.ssh/id_rsa.pub

// ssh-key
AAAAB3NzaC1yc2EAAAADAQABAAABAQDwGH+9X5hDch+sYL2lwBBZT
ghi2aCqnofSMgCQCvq5wBjZ0En/tiafEU/K4P5Jh9Y+08DVJL5miy
G2Zm1JLZcUyBxqZVT4ApAOkl1vUFl78ArTO/yKSZakZDmtzQD4jCn
8RVCb1kTCDlhl7IGHN0yHoLUKanwYGwBJskowJeUAY/uk5Xuy/cem
VY1UxE+D3s9WhFtD5lJP5O7UueTRyXc5aLTH6/oz9HgdWXh7u71wJ
dUkH+CExdsnXmXB5E3nvb your_eamil@example.com

$ cd ~/.ssh/
$ vi authorized_keys

// 将刚才的内容粘贴进去，就完成配置了
```

# 服务器环境安装

## PHP7.4
### - php安装
1. `apt-get update` 更新资源
2. `apt-get install software-properties-common`
3. `sudo add-apt-repository ppa:ondrej/php` 添加源
4. `sudo apt install php7.4 php7.4-common php7.4-cli` 安装php7.4 和所需的扩展
5. `sudo apt install php7.4-fpm` 安装php7.4fpm
6. `php -v`验证是否安装成功，有版本号出现就代表安装成功

### - 安装php扩展
> 这里真是一波三折，过程太艰辛了，可能只是个人出现这些问题吧，如果遇到如下问题可参考解决

在`安装扩展`之前，需要安装`libssl-dev`
```
// 会报错 libssl-dev : Depends: libssl1.1 (= 1.1.1f-1ubuntu2.8) but 1.1.1f-1ubuntu2.9 is to be installed
$ sudo apt-get -y install libssl-dev

// 需要安装包管理工具 aptitude
$ sudo apt-get -y install aptitude
```

> 使用aptitude降级libssl-dev

```
root@iZ0jl1e2vzz9e96ot20b5xZ:~# sudo aptitude install libssl-dev
The following NEW packages will be installed:
  libssl-dev{b}
0 packages upgraded, 1 newly installed, 0 to remove and 29 not upgraded.
Need to get 1,584 kB of archives. After unpacking 8,008 kB will be used.
The following packages have unmet dependencies:
 libssl-dev : Depends: libssl1.1 (= 1.1.1f-1ubuntu2.8) but 1.1.1f-1ubuntu2.9 is installed
The following actions will resolve these dependencies:

     Keep the following packages at their current version:
1)     libssl-dev [Not Installed]



Accept this solution? [Y/n/q/?] n // 选n，如果选y 跟 apt-get install -y libssl-dev 是一样的
The following actions will resolve these dependencies:

     Downgrade the following packages:
1)     libssl1.1 [1.1.1f-1ubuntu2.9 (now) -> 1.1.1f-1ubuntu2.8 (focal-security, focal-updates)]



Accept this solution? [Y/n/q/?] y // 接受降级
The following packages will be DOWNGRADED:
  libssl1.1
The following NEW packages will be installed:
  libssl-dev
0 packages upgraded, 1 newly installed, 1 downgraded, 0 to remove and 29 not upgraded.
Need to get 2,904 kB of archives. After unpacking 8,008 kB will be used.
Do you want to continue? [Y/n/?] y // 继续
Get: 1 http://mirrors.cloud.aliyuncs.com/ubuntu focal-updates/main amd64 libssl1.1 amd64 1.1.1f-1ubuntu2.8 [1,320 kB]
Get: 2 http://mirrors.cloud.aliyuncs.com/ubuntu focal-updates/main amd64 libssl-dev amd64 1.1.1f-1ubuntu2.8 [1,584 kB]
Fetched 2,904 kB in 0s (12.4 MB/s)
Preconfiguring packages ...
dpkg: warning: downgrading libssl1.1:amd64 from 1.1.1f-1ubuntu2.9 to 1.1.1f-1ubuntu2.8
(Reading database ... 116025 files and directories currently installed.)
Preparing to unpack .../libssl1.1_1.1.1f-1ubuntu2.8_amd64.deb ...
Unpacking libssl1.1:amd64 (1.1.1f-1ubuntu2.8) over (1.1.1f-1ubuntu2.9) ...
Selecting previously unselected package libssl-dev:amd64.
Preparing to unpack .../libssl-dev_1.1.1f-1ubuntu2.8_amd64.deb ...
Unpacking libssl-dev:amd64 (1.1.1f-1ubuntu2.8) ...
Setting up libssl1.1:amd64 (1.1.1f-1ubuntu2.8) ...
Setting up libssl-dev:amd64 (1.1.1f-1ubuntu2.8) ...
Processing triggers for libc-bin (2.31-0ubuntu9.2) ...
```

> 开始安装`php7.4-dev` 和 `php-pear`

`sudo apt-get install -y php7.4-dev` // phpize需要
`sudo apt-get install -y php-pear` // pecl需要

> 安装`libmagic-dev`

`sudo apt-get -y install libmagic-dev`

> 安装`gd` 和 `zip`

`sudo apt-get install php7.4-gd`
`sudo apt-get install php7.4-zip`

> 安装其余扩展

`sudo apt-get -y install php7.4-fpm php7.4-mysql php7.4-curl php7.4-json php7.4-mbstring php7.4-xml php7.4-intl `

## mysql 8
### - mysql 安装
```
root@iZ0jl1e2vzz9e96ot20b5xZ:~# apt install mysql-server

......

root@iZ0jl1e2vzz9e96ot20b5xZ:~# mysql_secure_installation

Securing the MySQL server deployment.

Connecting to MySQL using a blank password.

VALIDATE PASSWORD COMPONENT can be used to test passwords
and improve security. It checks the strength of password
and allows the users to set only those passwords which are
secure enough. Would you like to setup VALIDATE PASSWORD component? //要安装验证密码插件吗?

Press y|Y for Yes, any other key for No: n // 我选的n
Please set the password for root here.

New password: // 输入新密码
Re-enter new password: // 重复确认新密码

By default, a MySQL installation has an anonymous user,
allowing anyone to log into MySQL without having to have
a user account created for them. This is intended only for
testing, and to make the installation go a bit smoother.
You should remove them before moving into a production
environment.

Remove anonymous users? (Press y|Y for Yes, any other key for No) : y  // 删除匿名账户
Success.


Normally, root should only be allowed to connect from
'localhost'. This ensures that someone cannot guess at
the root password from the network.

Disallow root login remotely? (Press y|Y for Yes, any other key for No) : n // 不禁止root管理员从远程登录

 ... skipping.
By default, MySQL comes with a database named 'test' that
anyone can access. This is also intended only for testing,
and should be removed before moving into a production
environment.


Remove test database and access to it? (Press y|Y for Yes, any other key for No) : y // 删除test数据库并取消对它的访问权限
 - Dropping test database...
Success.

 - Removing privileges on test database...
Success.

Reloading the privilege tables will ensure that all changes
made so far will take effect immediately.

Reload privilege tables now? (Press y|Y for Yes, any other key for No) : y // 刷新授权表，让初始化后的设定立即生效
Success.

All done!
```

`systemctl status mysql` 检查mysql安装情况，出现下图内容为成功
![](https://img-qiniu.alwayslay.com/Laravel7新项目从零部署记录/20211011032336922.png)

### - 配置远程登录
使用`mysql -u root -p`命令，`Enter Password`处输入刚才设置的密码，登录mysql，开始设置远程登录

`mysql 8.0.13`的时候改了`远程登录`的`授权方式`

```
// 创建账户，密码为你的mysql登录密码
mysql > create user 'root'@'%' identified by '登录密码';

// 赋予权限，with grant option这个选项表示该用户可以将自己拥有的权限授权给别人
mysql > grant all privileges on *.* to 'root'@'%' with grant option

// 改密码&授权超用户，flush privileges 命令本质上的作用是将当前user和privilige表中的用户信息/权限设置从mysql库(MySQL数据库的内置库)中提取到内存里
mysql > flush privileges;
```

> 补一个旧版本的授权方式


```
mysql > grant all privileges on *.* to 'root'@'%' identified by '登录密码' with grant option;
mysql > flush privileges;
```

如果按以上步骤操作完成之后还无法链接，去购买服务器的网站的`控制台`查看`安全组策略`，看看是否有开放`3306端口`。

## Nginx

安装Nginx
```
$ sudo apt-get install -y nginx
```
如果安装完成，nginx服务将会自动启动，可以使用以下命令来进行验证。
```
root@iZ0jl1e2vzz9e96ot20b5xZ:~# sudo systemctl status nginx
● nginx.service - A high performance web server and a reverse proxy server
     Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
     Active: inactive (dead)
       Docs: man:nginx(8)
```

## 部署项目

### - 存放地址
规范自己的文件存放地址是必要的。

个人习惯

```
/www/wwwroot/  项目存放地址
/www/wwwlog/   项目日志地址
/www/ssl/      项目SSL证书地址
```


### - 部署

我的nginx配置文件目录为`/etc/nginx/sites-enabled/`，文件以网站域名命名，如：`www.alwayslay.com`

以下是laravel项目基础部署代码

```
server {
    listen 80;
    server_name xxx.com;
    root /www/wwwroot/xxx/public;
    index index.php index.html;
    
    // 伪静态
    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }
    
    location ~ \.php$ {
        fastcgi_pass   unix:/run/php/php7.4-fpm.sock;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include        fastcgi_params;
    }
}
```

#### -- 伪静态
部署Laravel需要伪静态，伪静态最主要的目的就是美化Url和提高seo。

比如：
静态地址 `https://www.alwayslay.com/`
动态地址 `https://www.alwayslay.com?a=1&b=2`
伪静态 `https://www.alwayslay.com/a/1/b/2`

#### -- php / fastcgi_pass
Nginx处理PHP请求就是通过与php-fpm进程间通信实现的。nginx与php-fpm进程间的通信有两种方式，用哪种取决你的php-fpm的配置：

> TCP

TCP是IP加端口，可以用于跨服务器。配置如下：
```
// 目录：/etc/php/7.4/fpm/pool.d/wwww.conf
php-fpm.conf: listen = 127.0.0.1:9000
// 目录：/etc/nginx/site-enabled/*
nginx.conf: fastcgi_pass = 127.0.0.1:9000
```

> UNIX Domain Socket

不经过网络，只能用于nginx和php-fpm在同一个服务器场景。配置如下：
```
// 目录：/etc/php/7.4/fpm/pool.d/wwww.conf
php-fpm.conf: listen = /run/php/php-fpm.sock
// 目录：/etc/nginx/site-enabled/*
nginx.conf: listen = unix:/run/php/php-fpm.sock
```
其中php-fpm.sock是一个文件，由php-fpm生成

如果配置不正确的话，访问时将会返回502

## 总结
linux自带apache，需要改端口，避免于nginx冲突

好久没有自己搭建了，平时写外包项目图省事，一般都是直接宝塔发布上线，这次从零开始发现了不少的问题，也弄了差不多一天的时间

