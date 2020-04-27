---
title: 配置OverLeaf私人服务器
copyright: true
mathjax: false
top: 1
date: 2020-04-16 22:53:53
categories: 小知识
tags:
- docker
- overleaf
keywords:
description:
---

此文转载自好友[BlueFisher]([https://bluefisher.github.io/2020/04/16/Overleaf-%E6%9C%8D%E5%8A%A1%E5%99%A8%E9%85%8D%E7%BD%AE/](https://bluefisher.github.io/2020/04/16/Overleaf-服务器配置/)。

<!--more-->

1. 首先根据官方教程 [https://docs.docker.com/engine/install/ubuntu/](https://docs.docker.com/engine/install/ubuntu/) 确保服务器已经安装了 Docker，同时根据 [https://docs.docker.com/compose/install/](https://docs.docker.com/compose/install/) 安装 Docker Compose。

2. 拉取最新的 overleaf 服务器版本

   ```bash
   $ docker pull sharelatex/sharelatex
   ```

3. 在用户目录 `~` 下新建文件夹 `~/sharelatex/, ~/sharelatex/sharelatex_data/, ~/sharelatex/mongo_data/, ~/sharelatex/redis_data/`

4. 下载 [docker-compose.yml](https://github.com/overleaf/overleaf/blob/master/docker-compose.yml) 文件，并存在 `~/sharelatex/` 文件夹中

5. 根据需要修改 docker-compose.yml 文件，可以更改服务器映射的端口号 `ports` ，修改 sharelatex, mongo 和 redis 的`volumes` 到步骤3创建的文件夹中

6. 进入 `~sharelatex` 启动 docker-compose.yml

   ```shell
   $ docker-compose up
   ```

7. 由于默认安装的是最小版本 TeXLive，如果要安装完整包，执行

   ```shell
   $ docker exec sharelatex tlmgr install scheme-full
   ```

   或者也可以安装任意的单个包，只需要把 `sheme-full` 替换为包的名称即可

8. 第一次启动镜像后，访问 `/launchpad` 页面设置管理员账号