---
title: seo站点优化
top: 0
tags:
  - seo
  - translat
categories:
  - 前端
translate_title: seo-site-optimization
date: 2018-12-27 23:42:30
keywords: [seo, hexo, seo优化]
description: seo优化，seo站点优化，翻译url并修改访问地址，添加nofollow属性。
---

seo站点优化主要是为了让百度爬虫可以爬到我们的网站，增加权重，让我们的网站排名靠前。
尽可能对每片文章添加keywords、description的描述，可以增加网站的权重。

<!-- more -->

## 添加sitemap
> 首先对你的文章生成sitemap

```
npm install hexo-generator-sitemap --save #sitemap.xml适合提交给谷歌搜素引擎
npm install hexo-generator-baidu-sitemap --save #baidusitemap.xml适合提交百度搜索引擎
```

> 站点_config.yml文件中添加一下代码

```
# 自动生成sitemap
sitemap:
  path: sitemap.xml
baidusitemap:
  path: baidusitemap.xml
```

> 最后修改站点配置文件_config.yml

```
# URL
## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
url: https://域名.com
```

## 优化访问地址
hexo站点的默认访问地址为：sitename/year/mounth/day/title
但是这样四层url结构不利于seo，爬虫将会爬不到我们的文章，所以我们可以将url改为：sitename/title.html

```
url: http://你的网站
root: /
permalink: :title.html
permalink_defaults:
```

但是我们现在这样还是不利于爬虫的爬去文章，因为我们的title是中文的，接下来我们要把url上的中文标题进行翻译。

---

> 首先我们进行安装翻译插件

```
npm install hexo-translate-title --save
```

> 配置站点的_config.yml
> 这里推荐使用baidu_with_appid，google经常会翻译不出来，造成undifined.html
> 正常情况下 google > baidu > youdao

```
translate_title:
  translate_way: google # google,youdao,baidu_with_appid,baidu_no_appid
  is_need_proxy: false   # true | false 是否使用代理
  # proxy_url: http://localhost:50018 # 你的代理地址
  # youdao_api_key: '' # 你的 youdao_api_key
  # youdao_keyfrom: xxxx-blog # 你的 youdao_keyfrom
  # baidu_appid: '' # 你的 baidu_appid
  # baidu_appkey: '' # 你的 baidu_appkey
```

> 我们再回过头将_config.yml中的permalink进行修改

```
# 将url换为翻译过的标题
permalink: :translate_title.html
```

## nofollow
nofollow标签的作用就是让搜索引擎不要抓去网页上带有nofollow属性的出站链接，目的是减少这些出站分散我们网站的权重

> 安装nofollow插件，自动对出站链接添加nofollow属性，有效阻止权重分散

```
npm install hexo-autonofollow --save
```

> 配置_config.yml, 开启nofollow，对以下链接不添加nofollow属性

```
nofollow:
    enable: true
    exclude:
    - www.xxx.com
```

## 最后
拜拜！
