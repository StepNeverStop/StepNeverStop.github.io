---
title: Asynchronous Methods for Deep Reinforcement Learning
copyright: true
mathjax: true
top: 1
date: 2019-05-30 15:22:13
categories: ReinforcementLearning
tags:
- rl
keywords:
description:
---

本文提出了A3C模型，即Asynchronous Advantage Actor-Critic，是A2C的异步版本，使用CPU多核而不用GPU进行训练，文中说效果比使用GPU反而更好。

推荐：

- -

<!--more-->

# 简介

论文地址：[https://arxiv.org/pdf/1602.01783.pdf](https://arxiv.org/pdf/1602.01783.pdf)

本文提出了一个概念简单、异步梯度优化的轻量级深度强化学习训练框架。

优点：

- 稳定训练
- 在Atari游戏上相比当时最先进的算法，使用一个多核CPU比使用GPU训练时间减半
- 成功适用于很多连续运动学控制问题，例如图像输入的3D迷宫

> the sequence of observed data encountered by an online RL agent is non-stationary, and on-line RL updates are strongly correlated. By storing the agent’s data in an experience replay memory, the data can be batched or randomly sampled from different time-steps. Aggregating over memory in this way reduces non-stationarity and decorrelates updates, but at the same time limits the methods to off-policy reinforcement learning algorithms 

文中指出，on-policy方法训练不稳定，数据相关性很强，off-policy机制结合经验池机制减轻了训练的不稳定性和数据相关性。

经验池的缺点：

1. 占用内存，增加计算量
2. 需要off-policy算法

本文为深度强化学习提供了一个非常不同的范例，不使用经验池机制，而使用异步并行的方法在多个**相同环境**中执行多个智能体。（同时训练多个不同环境没有进行描述和实验）

这种异步并行方式的优点是：

1. 实现减轻数据相关性的效果，使训练稳定
2. 可用于大范围on-policy算法，如Sarsa，n-step方法，A-C方法，也可用于off-policy方法，如Q-learning
3. 利用深度神经网络设计算法，保持鲁棒性与有效性（不予置评）
4. 不使用GPU，只使用多核CPU，反而训练时间短
5. 比大规模分布式需要更少的资源占用（内存、算力等）
6. **相比于[Gorila](https://arxiv.org/pdf/1507.04296.pdf)异步训练方式，本文中的训练方式不需要中心服务器，其使用的是共享内存模式**

# 文中精要

由于本文思想非常简单，所以精简描述论文精华

本文使用**[Hogwild!](https://arxiv.org/pdf/1106.5730.pdf)**方式进行异步梯度下降

Hogwild! 这个方法的提出也很偶然，容我步步道来：

我们使用函数对样本集进行拟合如下图所示

![](./asynchronous-methods-for-drl/regression.png)

当我们想要判断函数是否拟合的不错，我们往往使用损失函数来衡量，损失越小，则代表函数拟合得越好（但，过拟合不是我们想要的）。

![](./asynchronous-methods-for-drl/lossfunction.png)

为了减小损失，我们常用梯度下降算法来优化。标准的梯度下降原理如下图所示，如果函数为凸函数，且更新步长很小，那么在有限步长内总可以下降至函数最小点，即，获得使损失函数最小的参数$\theta^{\ast}$

![](./asynchronous-methods-for-drl/gd.png)

之后出现了SGD，也就是随机梯度下降，这种方法差不多在60年代提出，由于思想过于简单，迭代次数很长，一直不被主流优化算法接受。但是，当大数据时代到来时，SGD变成了很普遍的优化方法。

SGD的算法流程如下：

- 选一个初始参数向量$\theta$和正步长$\alpha$
- 循环直到满足结束条件：
  - 从训练集中随机选择一个样本$x_{i}$
  - 更新参数$\theta \leftarrow\left(\theta-\alpha \nabla L\left(f_{\theta}\left(x_{i}\right), y_{i}\right)\right)$

SGD的优点：

1. 少内存占用。SGD不需要所有的样本集进行计算梯度，只需要从样本集中抽取一个样本进行训练。频繁的采样操作可以使用高速缓存来加速训练。
2. 收敛至**可接受的解**速度很快。其实我们不希望看到过拟合，当然也不希望看到欠拟合，SGD正好是这两个极端的trade-off。SGD可以很快的收敛到一个较好的解，相对于样本集较好的解比相对于样本集最好的解的泛化能力可要强得多。下图展示了SGD与标准梯度下降的损失函数曲线比较。

![](./asynchronous-methods-for-drl/sgdvsgd.png)

看图像可能会觉得SGD并没有严格下降，有时会有损失上升的倾向，但是，总体来看，这种方法最终也是可以收敛到最小值的。总体上，它使损失进行了下降。

- 每个线程从训练集随机抽取一个样本$x_{i}$
  - 锁参数$\theta$
  - 线程读参数$\theta$
  - 线程更新参数$\theta \leftarrow\left(\theta-\alpha \nabla L\left(f_{\theta}\left(x_{i}\right), y_{i}\right)\right)$
  - 解锁



- 每个线程从训练集随机抽取一个样本$x_{i}$
  - ~~锁参数$\theta~$~~
  - 线程读参数$\theta$
  - 线程更新参数$\theta \leftarrow\left(\theta-\alpha \nabla L\left(f_{\theta}\left(x_{i}\right), y_{i}\right)\right)$
  - ~~解锁~~

## 伪代码

首先介绍one-step Q-Learning的训练模式

![](./asynchronous-methods-for-drl/a1stepq.png)

![](./asynchronous-methods-for-drl/anstepq.png)

![](./asynchronous-methods-for-drl/a3c.png)

# 引用

> [Parallel Machine Learning with Hogwild!](https://medium.com/@krishna_srd/parallel-machine-learning-with-hogwild-f945ad7e48a4)