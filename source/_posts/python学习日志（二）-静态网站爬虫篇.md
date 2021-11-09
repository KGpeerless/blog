---
title: python学习日志（二）/ 静态网站爬虫篇
top: 100
keywords:
  - python
  - 爬虫
  - 静态网站
description: 学习过程中，第一次尝试爬虫
tags:
  - python
  - 爬虫
categories:
  - python
  - 爬虫
translate_title: python-learning-log-(ii)-/-static-website-crawler
date: 2021-10-26 15:02:29
---

学习过程中，第一此尝试爬虫。

使用包
requests           2.26.0
beautifulsoup4     4.10.0
parsel             1.6.0

<!-- more -->

## 爬自己blog的主页标题
> 代码部分

```
# -*- coding:UTF-8 -*-
import requests
from bs4 import BeautifulSoup

if __name__ == '__main__':
    target = 'https://www.alwayslay.com'
    response = requests.get(url=target)
    response.encoding = 'utf-8'
    html = response.text
    bf = BeautifulSoup(html, "html.parser")
    articles = bf.find_all('article', class_="articles")

    a_bf = BeautifulSoup(str(articles[0]), "html.parser")
    aList = a_bf.find_all('a', class_="article-title")
    for each in aList:
        print(each.string, target + each.get('href'))
```
> 结果

![](https://img-qiniu.alwayslay.com/python学习日志（二）-静态网站爬虫篇/20211026030949999.png)


## 爬豆瓣影评讨论
> 需求，爬豆瓣影评讨论，中间部分内容

![](https://img-qiniu.alwayslay.com/python学习日志（二）-静态网站爬虫篇/20211026032136668.png)

豆瓣有设置反爬虫，所以需要设置user-agent，这也是我刚开始碰见的困难，不明白为什么按照网上的事例无法正常爬取。

对`requests`设置`headers`
```
headers = {
    'user-agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/94.0.4606.71 Safari/537.36'
}
response = requests.get(url=url, headers=headers)
```

接下来要分析当前静态页面的层级，没有使用`bs4`，而是使用的`parsel`，会有另外一篇单独介绍`parsel`的文章，以下简单介绍一下。

`parsel`是一个功能库，支持三种方法

- css() 
- xpath()
- re()

![](https://img-qiniu.alwayslay.com/python学习日志（二）-静态网站爬虫篇/20211026032848717.png)

要获取的节点为 table > tr > td > a

```
adatas = selector.css('table tr td')

# 但是为了更准确的定位到想要爬取的节点，使用ID选择器
css('#posts-table tr td')

# 而且我们只取标题，那么只取第一个td的内容就可以了
css('#posts-table tr td:nth-child(1)')
```

> 代码部分

```
url = 'https://movie.douban.com/subject/26603847/discussion/'
headers = {
    'user-agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/94.0.4606.71 Safari/537.36'
}
response = requests.get(url=url, headers=headers)
selector = parsel.Selector(response.text)

adatas = selector.css('#posts-table tr td:nth-child(1)')

for key, data in enumerate(adatas):
    # 不打印热门讨论
    if key == 0:
        continue

    text = data.css('a::text').get()
    if text:
        print(text.strip())
```

> 结果

![](https://img-qiniu.alwayslay.com/python学习日志（二）-静态网站爬虫篇/20211026032040868.png)
