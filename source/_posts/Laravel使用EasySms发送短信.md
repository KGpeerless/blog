---
title: Laravel使用EasySms发送短信
top: 0
keywords:
  - Laravel
  - EasySms
  - php
  - 短信
description: Laravel使用EasySms发送短信
tags:
  - Laravel
  - EasySms
  - php
categories:
  - Laravel
  - EasySms
  - php
translate_title: laravel-uses-easysms-to-send-text-messages
date: 2021-09-28 16:56:13
---
Laravel使用EasySms发送短信

本文使用环境
PHP 7.4
Laravel 7.*

EasySms github地址 [点击跳转](https://github.com/overtrue/easy-sms)

<!-- more -->

## 安装
### composer安装
```
composer require "overtrue/easy-sms"
```

## 配置
### 阿里云短信
```
// App\Services\EasySmsService.php
// 可以配置多个网关，我这边只做了阿里云
<?php

namespace App\Services;

use App\Models\Sms;
use Overtrue\EasySms\EasySms;

class EasySmsService {
    public $config;
    public $easySms;

    public function __construct()
    {
        $this->config = [
            // HTTP 请求的超时时间（秒）
            'timeout' => 5.0,
        
            // 默认发送配置
            'default' => [
                // 网关调用策略，默认：顺序调用
                'strategy' => \Overtrue\EasySms\Strategies\OrderStrategy::class,
        
                // 默认可用的发送网关
                'gateways' => [
                    'aliyun',
                ],
            ],
            // 可用的网关配置
            'gateways' => [
                'errorlog' => [
                    'file' => '/tmp/easy-sms.log',
                ],
                'aliyun' => [
                    'access_key_id' => 'LTAI5tPySz1rXxxxxxxxx',
                    'access_key_secret' => 'ujodisgPFrgHEkrCmxxxxxxx',
                    'sign_name' => 'XXX', // 短信签名
                ],
            ],
        ];

        $this->easySms = new EasySms($this->config);
    }
}
```

## 使用
### 基础使用
```
public function sendCode($mobile, $code)
{
    // 可以加一些业务逻辑，已发送的验证码保存到数据库中，进行验证

    return $this->easySms->send($mobile, [
        'content'  => '您的验证码为：${code}，请勿泄露于他人！',
        'template' => 'SMS_2122xxxx',
        'data' => [
            'code' => $code,
        ],
    ]);

}
```

### 进阶使用
```
$this->easySms->send($mobile, [
    'content'  => function($gateway){
        // 当网关为阿里云时
        if ($gateway->getName() == 'aliyun') {
            return '您的验证码为：${code}，请勿泄露于他人！';
        }
        
        return '您的验证码为：${code}，请勿泄露于他人！';
    },
    'template' => function($gateway){
        // 当网关为阿里云时
        if ($gateway->getName() == 'aliyun') {
            return 'SMS_2122xxxx';
        }
        
        return 'SMS_21333xxxx';
    },
    'data' => function($gateway){
        return [
            'code' => 6379
        ];
    },
]);
```