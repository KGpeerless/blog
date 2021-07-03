---
title: Laravel使用EasyWechat
top: 0
keywords:
  - PHP
  - Laravel
  - EasyWechat
description: 记录Laravel使用EasyWechat的经历
tags:
  - PHP
  - Laravel
  - EasyWechat
categories:
  - PHP
  - Laravel
  - EasyWechat
translate_title: laravel-uses-easywechat
date: 2021-01-20 17:45:13
---
以下讲述为最基础版本

开发环境 Homestead：
   php 7.4
   Laravel 7.28.4
   EasyWechat 4.x
   composer

<!-- more -->

## 安装EasyWechat
### 注意事项
> php >= 7.0
> php curl 扩展
> php OpenSSL 扩展
> php SimpleXML 扩展
> php fileinfo 扩展

### 安装方法
```
$ composer require overtrue/wechat:~4.0 -vvv
```
## 使用方法

### 控制器
```
// app/Http/Controller/Api/WechatController.php
...

use EasyWechat\Factory;

class WechatController extends Controller {
    private $mp;

    public function __construct(Request $request) {
        $mpConfig = [
            'app_id' => 'xxx',
            'secret' => 'xxx',
            'token'   => 'xxx',
            'aes_key' => 'xxxx',
            'response_type' => 'array',
        ];

        $this->mp = Factory::officialAccount($mpConfig);
    }

    public function getMessage()
    {
        $this->mp->server->push(function ($message) {
            switch ($message['MsgType']) {
                case 'event':
                    return '收到事件消息';
                    break;
                case 'text':
                    return '收到文字消息';
                    break;
                case 'image':
                    return '收到图片消息';
                    break;
                case 'voice':
                    return '收到语音消息';
                    break;
                case 'video':
                    return '收到视频消息';
                    break;
                case 'location':
                    return '收到坐标消息';
                    break;
                case 'link':
                    return '收到链接消息';
                    break;
                case 'file':
                    return '收到文件消息';
                // ... 其它消息
                default:
                    return '收到其它消息';
                    break;
            }
        });

        $response = $this->mp->server->serve();

        return $response;
    }
}
```

### 路由
> 此处注意，路由一定要使用any，不要用get，否则会导致微信公众平台的服务器配置启动成功，但是在公众号中为“公众号服务出现故障”

```
Route::any('wechat/message', 'WechatController@getMessage')
    ->name('api.wechat.message');
```

> 以下为路由使用get所引起的错误

![错误](http://img-qiniu.alwayslay.com/Laravel使用EasyWechat/20210120061036761.png)
