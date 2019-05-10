---
title: 强化学习的类别
copyright: true
top: 1
date: 2019-05-10 15:18:23
mathjax: true
keywords:
description:
categories: ReinforcementLearning
tags:
- rl
---

本文讲述了强化学习中各种算法、问题的分类规则。

<!--more-->

# Model



# Policy or Value



# MC or TD



# On-policy or Off-policy

首先需要理解什么是行为策略与目标策略。

行为策略$\mathcal{Behavior Policy}$：

- 采样时间序列$S_{0},A_{0},R_{0},S_{1},A_{1},R_{2},...,S_{n},A_{n},R_{n}$的策略
- 官话：指导个体产生与环境进行实际交互行为的策略
- 未必由一个模型表示

目标策略$\mathcal{TargetPolicy}$:

- 待优化的策略
- 官话：用来评价状态或行为价值的策略或者待优化的策略称为目标策略



同步策略学习$\mathcal{On-Policy}$:

- 简言之，边采样边学习
- 官话：如果个体在学习过程中优化的策略与自己的行为策略是同一个策略时，这种学习方式称为**同步策略学习（on-policy learning）**
- 行为策略与目标策略是同一个

异步策略学习$\mathcal{Off-Policy}$:

- 简言之，你采样我学习
- 官话：如果个体在学习过程中优化的策略与自己的行为策略是不同的策略时，这种学习方式称为**异步策略学习（off-policy learning）**
- 行为策略与目标策略不同，行为策略可能是目标策略的“分身”（双网络结构），或者完全是另一个采样的策略

例如：





|             | SARSA | Q-learning |
|:-----------:|:-----:|:----------:|
| Choosing A' |   π   |      π     |
| Updating Q  |   π   |      μ     |



> [一个以Q-Learning和Sarsa算法做比较的解释](https://stackoverflow.com/a/41420616)

# Stochastic or Deterministic