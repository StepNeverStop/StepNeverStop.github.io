---
title: 创建ML-Agents的Docker镜像
copyright: true
top: 1
date: 2019-01-04 10:38:59
categories:
- Docker
- Unity
tags:
- docker
- unity
- ml-agents

---

# 创建ML-Agents的Docker镜像

## ML-Agents v0.6.0

![](/2019/01/04/创建ML-Agents的Docker镜像/Snipaste_2019-01-04_10-44-58.png)

### 环境

本机环境

- ML-Agents 0.6.0
- Windows 10 专业版
- docker client version 18.09.0
- docker server version 18.09.0

平台
- [机器学习平台](http://10.0.4.228)

### 创建镜像

1. 打开`~/ml-agents-0.6.0/`目录,看到有一个官方给定的`Dockerfile`
![](/2019/01/04/创建ML-Agents的Docker镜像/Snipaste_2019-01-04_10-52-49.png)
2. 直接`Build`,在该目录下运行`docker build -t [name]:[tag] .`,一定要注意最后的`.`,很**重要**
![](/2019/01/04/创建ML-Agents的Docker镜像/Snipaste_2019-01-04_11-29-30.png)
3. 新建一个`sources.list`文件,为镜像内换源,因为将来有可能需要在容器内安装某些包,有一些国外的资源往往会下载失败,所以需要**换源**
- 新建一个`sources.list`
- 用文本编辑器打开,写入以下内容
![](/2019/01/04/创建ML-Agents的Docker镜像/Snipaste_2019-01-04_11-36-31.png)
```
# deb cdrom:[Ubuntu 16.04 LTS _Xenial Xerus_ - Release amd64 (20160420.1)]/ xenial main restricted
deb-src http://archive.ubuntu.com/ubuntu xenial main restricted #Added by software-properties
deb http://mirrors.aliyun.com/ubuntu/ xenial main restricted
deb-src http://mirrors.aliyun.com/ubuntu/ xenial main restricted multiverse universe #Added by software-properties
deb http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted multiverse universe #Added by software-properties
deb http://mirrors.aliyun.com/ubuntu/ xenial universe
deb http://mirrors.aliyun.com/ubuntu/ xenial-updates universe
deb http://mirrors.aliyun.com/ubuntu/ xenial multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-updates multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse #Added by software-properties
deb http://archive.canonical.com/ubuntu xenial partner
deb-src http://archive.canonical.com/ubuntu xenial partner
deb http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted multiverse universe #Added by software-properties
deb http://mirrors.aliyun.com/ubuntu/ xenial-security universe
deb http://mirrors.aliyun.com/ubuntu/ xenial-security multiverse
```
- 保存退出
4. 新建一个`DockerfilePlus`,在官方生成的基础镜像上安装一些可以在平台上运行的包,`openssh-server`,联网工具`net-tools`,心爱的`apt-file`等等
- 新建一个`DockerfilePlus`
- 用文本编辑器打开,输入以下内容
![](/2019/01/04/创建ML-Agents的Docker镜像/Snipaste_2019-01-04_12-01-32.png)
```
FROM hub.hoc.ccshu.net/wjs/mlunityv060:v0.1

RUN cp /etc/apt/sources.list /etc/apt/sources.list.bak
COPY sources.list /etc/apt/sources.list

RUN apt-get update && apt-get install -y \
        apt-file \
        nano \
        net-tools \
        iputils-ping \
        openssh-server \
        apt-utils \
    && rm -rf /var/lib/apt/lists/* \
    && mkdir /var/run/sshd \
    && echo "root:1234" | chpasswd \
    && sed -i 's/prohibit-password/yes/g' /etc/ssh/sshd_config

EXPOSE 22

ENTRYPOINT ["/usr/sbin/sshd","-D"]
```
5. 在`DockerfilePlus`所在文件夹下,执行`build -t [name]:[tag] -f DockerfilePlus .`

### PUSH镜像

`docker push [name]:[tag]`
![](/2019/01/04/创建ML-Agents的Docker镜像/Snipaste_2019-01-04_12-02-47.png)

### 测试镜像

- 登录[机器学习平台](http://10.0.4.228),没有使用平台的可以在本地使用`docker run`直接开启容器
- 先测试使用容器的方式
  - 创建容器
  ![](/2019/01/04/创建ML-Agents的Docker镜像/Snipaste_2019-01-04_13-28-19.png)
  将要运行和储存的文件夹放在数据卷`data`下,这个目录要在运行时由`--docker-target-name`指定
  - 等待容器创建成功
  ![](/2019/01/04/创建ML-Agents的Docker镜像/Snipaste_2019-01-04_13-31-15.png)
  - 容器创建成功后进入容器
  ![](/2019/01/04/创建ML-Agents的Docker镜像/Snipaste_2019-01-04_13-31-52.png)
  - 执行`mlagents-learn trainer_config.yaml --docker-target-name=data/unity-volume --env=3dball --train --run-id=test --save-freq=5000 | tee /data/unity-volume/log.txt`,如果不想在屏幕输出,可以在后边加上`>/dev/null`
  ![](/2019/01/04/创建ML-Agents的Docker镜像/Snipaste_2019-01-04_15-16-44.png)
  ![](/2019/01/04/创建ML-Agents的Docker镜像/Snipaste_2019-01-04_15-18-40.png)