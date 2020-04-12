---
title: 关于安装一些库的问题
copyright: true
mathjax: false
top: 1
date: 2020-03-19 14:30:16
categories: 小知识
tags:
- pip
---

本篇博客主要用以记录在各种环境安装各种库可能会遇到的问题及其解决方案，以便以后应急查询。

<!--more-->

# pip

## 由于网速不好，导致TimeOut

这种情况一般是由于pip要安装的库的默认源在国外服务器，导致网速只有几kb。可以考虑使用国内镜像站来安装或更新库。

比如`pip install tensorflow -i [国内镜像站的地址]`

更新时也可以用`pip install --upgrade tensorflow -i [国内镜像站的地址]`

常用的国内镜像站地址有：
- 阿里云 https://mirrors.aliyun.com/pypi/simple/
- 中科大 https://pypi.mirrors.ustc.edu.cn/simple/
- 清华 https://pypi.tuna.tsinghua.edu.cn/simple/
- 豆瓣 https://pypi.douban.com/simple/

如果报错，可以试试如下命令：

`pip install tensorflow -i [国内镜像站的地址] --trusted-host [国内镜像站官网]`

比如：

`pip install tensorflow -i http://pypi.douban.com/simple --trusted-host pypi.douban.com`