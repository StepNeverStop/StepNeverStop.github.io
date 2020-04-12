---
title: 蓝猫淘气三千问
copyright: true
mathjax: true
top: 1
date: 2020-04-12 18:31:27
categories: ReinforcementLearning
tags:
- rl
keywords:
description:
---

此篇博文用于记录学习RL或者实现RL方法过程中遇到疑难杂症及相应解决思路。

<!--more-->

# 待解决

1. 如果场景中存在多个图像输入，而且维度不一致，该如何处理呢？假设有汽车有前后两个摄像头，分辨率分别是$Vis_1=10\times10\times3$和$Vis_2=15\times5\times3$，那么该如何设计图像处理过程呢？是表示成$(CNN_1(Vis_1), CNN_2(Vis_2))$呢？还是表示成$CNN(Concat(Vis_1, Resize(Vis_2)))$呢？
2. 如果智能体的动作既包括离散的也包括连续的，该如何处理？

# 已解决

