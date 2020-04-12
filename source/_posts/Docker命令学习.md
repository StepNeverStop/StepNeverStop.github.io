---
title: Docker常用以及组合命令学习
copyright: true
top: 1
date: 2019-01-03 13:52:04
categories: Docker
tags:
- docker

---

本文记录了使用Docker过程中的一些常用的、复杂的命令。

<!--more-->

- 停止并删除正在运行的容器
`docker rm $(docker stop $(docker ps -aq))`
如果是在windows操作系统，可能会执行出错，可以切换至powershell或者使用命令：
`FOR /f "tokens=*" %i IN ('command 1') DO [command 2] %i`
- 查看容器的长ID
`docker inspect -f '{?{.ID}}' [name]`
**去掉命令中的`?`,因为双括号会转义失败**
- 宿主机向容器内传输文件/文件夹
`docker cp 本地文件路径 ID全称:容器路径`
- 容器传输文件/文件夹到宿主机
`docker cp ID全称:容器文件路径 本地路径`
- 修改本地已有镜像的名字
`docker tag [ImageID] [NewImageNmae]:[tag]`
- 创建新的镜像
`docker build -t [name]:[tag] -f [dockerfile_path] .`
- 开启一个容器
`docker run -it(交互模式) -d(后台运行) --name [name] [name]:[tag]`
如果需要启用`NVIDIA GPU`，可以加上命令参数`--gpus [all or device=0]`
- 进入一个正在运行的容器
`docker exec -it [name] [command]`
- 从容器生成镜像
`docker commit [container name or id] [image name:version]`
- 把docker容器中的文件传输到本机
`docker cp [container name or id]:[path] [本机path]`
- 把本机文件传输到docker容器内
`docker cp [本机path] [container name or id]:[path]`


