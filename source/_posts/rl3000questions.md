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

1. 什么是Semi-Markov Decision Process(SMDP)？

   [原链接](https://zhuanlan.zhihu.com/p/47051292)。分层强化学习里面，这里的上层策略其实是定义在一个SMDP上的，引用一下俞扬老师文章里面的一句话来解释一下。

   > 马尔可夫决策过程中，选择一个动作后，agent 会立刻根据状态转移方程$P$跳转到下一个状态，而在半马尔可夫决策过程(SMDP)中，当前状态到下一个状态的步数是个随机变量 $\tau$ ， 即在某个状态$s$下选择一个动作$a$后，经过$\tau$步才会以一个概率转移到下一个状态$s'$。 此时的状态转移概率是$s$和$\tau$的联合概率$P\left(s^{\prime}, \tau | s, a\right)$。

   文献：周文吉, and 俞扬. "分层强化学习综述."*智能系统学报*12.5 (2017): 590-594.

