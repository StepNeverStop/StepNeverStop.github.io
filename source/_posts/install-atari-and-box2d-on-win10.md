---
title: 在Windows 10系统上安装gym等环境
copyright: true
mathjax: false
top: 1
date: 2019-10-17 15:04:58
categories:	小知识
tags:
- rl
- gym
keywords:
description:
---

在Win 10系统安装gym，atari，Box2D等环境

<!--more-->

# 环境

- win 10 专业版
- python 3.6

# 安装gym

```bash
git clone https://github.com/openai/gym
cd gym
pip install -e .
```

# 安装atari

```bash
pip install --no-index -f https://github.com/Kojoley/atari-py/releases atari_py
```

# 安装box2d

在[https://www.lfd.uci.edu/~gohlke/pythonlibs/#pybox2d](https://www.lfd.uci.edu/~gohlke/pythonlibs/#pybox2d)下载相应的`.whl`文件

![](./install-atari-and-box2d-on-win10/box2d.png)

*注意分清python版本与操作系统位数*

之后再`cmd`中输入：

```bash
cd download_dir
pip install [name].whl
```

