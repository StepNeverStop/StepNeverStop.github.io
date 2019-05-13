---
title: conda环境和pip包的转移
copyright: true
top: 1
date: 2019-04-14 16:52:41
categories: Conda
tags:
- conda
---

本文记录了如何导出、导入自己的conda环境，对于pip安装的包如何导出、导入。

<!--more-->

# Conda 环境

1. 激活环境
`conda activate [env_name]`
2. 导出环境
`conda env export > [env_filename].yaml`
当前环境将被保存在定义的`.yaml`文件中
3. 导入环境
`conda env create -f [env_filename].yaml`
移植过来的conda环境只安装了原环境中使用`conda install`等命令安装的包, 使用`pip`命令安装的包需要重新安装

# pip包

1. 导出
`pip freeze > requirements.txt`
2. 导入
`pip install -r requirements.txt`
