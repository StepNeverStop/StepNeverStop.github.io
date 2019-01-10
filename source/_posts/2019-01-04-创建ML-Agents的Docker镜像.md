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

## 前言

  如果需要在镜像中使用GPU训练,可以将Nvidia的官方镜像作为基础镜像,`Dockerfile`如下:
```
FROM nvidia/cuda:9.0-base-ubuntu16.04
LABEL maintainer "NVIDIA CORPORATION <cudatools@nvidia.com>"

ENV NCCL_VERSION 2.3.7

RUN apt-get update && apt-get install -y --no-install-recommends \
	apt-utils \
        cuda-libraries-$CUDA_PKG_VERSION \
        cuda-cublas-9-0=9.0.176.4-1 \
        libnccl2=$NCCL_VERSION-1+cuda9.0 && \
    apt-mark hold libnccl2 && \
    rm -rf /var/lib/apt/lists/*

RUN apt-get update && apt-get install -y openssh-server

RUN apt-get install -y nano

RUN mkdir /var/run/sshd

RUN echo "root:1234" | chpasswd

RUN sed -i 's/prohibit-password/yes/g' /etc/ssh/sshd_config

EXPOSE 22

ENTRYPOINT ["/usr/sbin/sshd","-D"]
```

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

ENV PYTHONPATH /ml-agents:$PYTHONPATH

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

**为了使用GPU.改写完的Dockerfile如下(不需要看):**
```
FROM nvidia/cuda:9.0-base-ubuntu16.04
LABEL maintainer "Keavnn <https://stepneverstop.github.io>"

ENV NCCL_VERSION 2.3.7

RUN apt-get update && apt-get install -y --no-install-recommends \
	apt-utils \
        cuda-libraries-$CUDA_PKG_VERSION \
        cuda-cublas-9-0=9.0.176.4-1 \
        libnccl2=$NCCL_VERSION-1+cuda9.0 && \
    apt-mark hold libnccl2 && \
    rm -rf /var/lib/apt/lists/*


# ensure local python is preferred over distribution python
ENV PATH /usr/local/bin:$PATH

# http://bugs.python.org/issue19846
# > At the moment, setting "LANG=C" on a Linux system *fundamentally breaks Python 3*, and that's not OK.
ENV LANG C.UTF-8

# runtime dependencies
RUN apt-get update && apt-get install -y --no-install-recommends \
		ca-certificates \
		libexpat1 \
		libffi6 \
		libgdbm3 \
		libreadline6 \
		libsqlite3-0 \
		libssl1.0.0 \
	&& rm -rf /var/lib/apt/lists/*

ENV GPG_KEY 0D96DF4D4110E5C43FBFB17F2D347EA6AA65421D
ENV PYTHON_VERSION 3.6.4

RUN set -ex \
	&& buildDeps=" \
		dpkg-dev \
		gcc \
		libbz2-dev \
		libc6-dev \
		libexpat1-dev \
		libffi-dev \
		libgdbm-dev \
		liblzma-dev \
		libncursesw5-dev \
		libreadline-dev \
		libsqlite3-dev \
		libssl-dev \
		make \
		tcl-dev \
		tk-dev \
		wget \
		xz-utils \
		zlib1g-dev \
# as of Stretch, "gpg" is no longer included by default
		$(command -v gpg > /dev/null || echo 'gnupg dirmngr') \
	" \
	&& apt-get update && apt-get install -y $buildDeps --no-install-recommends && rm -rf /var/lib/apt/lists/* \
	\
	&& wget -O python.tar.xz "https://www.python.org/ftp/python/${PYTHON_VERSION%%[a-z]*}/Python-$PYTHON_VERSION.tar.xz" \
	&& wget -O python.tar.xz.asc "https://www.python.org/ftp/python/${PYTHON_VERSION%%[a-z]*}/Python-$PYTHON_VERSION.tar.xz.asc" \
	&& export GNUPGHOME="$(mktemp -d)" \
	&& gpg --keyserver ha.pool.sks-keyservers.net --recv-keys "$GPG_KEY" \
	&& gpg --batch --verify python.tar.xz.asc python.tar.xz \
	&& rm -rf "$GNUPGHOME" python.tar.xz.asc \
	&& mkdir -p /usr/src/python \
	&& tar -xJC /usr/src/python --strip-components=1 -f python.tar.xz \
	&& rm python.tar.xz \
	\
	&& cd /usr/src/python \
	&& gnuArch="$(dpkg-architecture --query DEB_BUILD_GNU_TYPE)" \
	&& ./configure \
		--build="$gnuArch" \
		--enable-loadable-sqlite-extensions \
		--enable-shared \
		--with-system-expat \
		--with-system-ffi \
		--without-ensurepip \
	&& make -j "$(nproc)" \
	&& make install \
	&& ldconfig \
	\
	&& apt-get purge -y --auto-remove $buildDeps \
	\
	&& find /usr/local -depth \
		\( \
			\( -type d -a \( -name test -o -name tests \) \) \
			-o \
			\( -type f -a \( -name '*.pyc' -o -name '*.pyo' \) \) \
		\) -exec rm -rf '{}' + \
	&& rm -rf /usr/src/python

# make some useful symlinks that are expected to exist
RUN cd /usr/local/bin \
	&& ln -s idle3 idle \
	&& ln -s pydoc3 pydoc \
	&& ln -s python3 python \
	&& ln -s python3-config python-config

# if this is called "PIP_VERSION", pip explodes with "ValueError: invalid truth value '<VERSION>'"
ENV PYTHON_PIP_VERSION 9.0.3

RUN set -ex; \
	\
	apt-get update; \
	apt-get install -y --no-install-recommends wget; \
	rm -rf /var/lib/apt/lists/*; \
	\
	wget -O get-pip.py 'https://bootstrap.pypa.io/get-pip.py'; \
	\
	apt-get purge -y --auto-remove wget; \
	\
	python get-pip.py \
		--disable-pip-version-check \
		--no-cache-dir \
		"pip==$PYTHON_PIP_VERSION" \
	; \
	pip --version; \
	\
	find /usr/local -depth \
		\( \
			\( -type d -a \( -name test -o -name tests \) \) \
			-o \
			\( -type f -a \( -name '*.pyc' -o -name '*.pyo' \) \) \
		\) -exec rm -rf '{}' +; \
	rm -f get-pip.py


RUN apt-get update && apt-get -y upgrade

# xvfb is used to do CPU based rendering of Unity
RUN apt-get install -y xvfb

COPY ml-agents /ml-agents
WORKDIR /ml-agents
RUN pip install .

# port 5005 is the port used in in Editor training.
EXPOSE 5005

RUN cp /etc/apt/sources.list /etc/apt/sources.list.bak
COPY sources.list /etc/apt/sources.list

ENV PYTHONPATH /ml-agents:$PYTHONPATH

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
  
---

## 安装Miniconda

`apt-get update && apt-get install bzip2 -y && cd /data && bash Miniconda3-latest-Linux-x86_64.sh`