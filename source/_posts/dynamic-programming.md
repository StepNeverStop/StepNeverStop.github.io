---
title: 动态规划
copyright: true
top: 1
date: 2019-05-12 20:16:22
mathjax: true
keywords: 
description: 
categories: ReinforcementLearning
tags:
- rl
---

本文介绍了强化学习问题中最简单基本的算法——动态规划（Dynamic Programming），介绍了贝尔曼方程在该算法中的应用。

<!--more-->

# DP的基本概念

> 动态规划(dynamic programming)是运筹学的一个分支，是求解决策过程(decision process)最优化的数学方法。20世纪50年代初美国数学家R.E.Bellman等人在研究多阶段决策过程(multistep decision process)的优化问题时，提出了著名的最优化原理(principle of optimality)，把多阶段过程转化为一系列单阶段问题，利用各阶段之间的关系，逐个求解，创立了解决这类过程优化问题的新方法——动态规划。1957年出版了他的名著《Dynamic Programming》，这是该领域的第一本著作。——[百度百科]([https://baike.baidu.com/item/%E5%8A%A8%E6%80%81%E8%A7%84%E5%88%92/529408?fr=aladdin](https://baike.baidu.com/item/动态规划/529408?fr=aladdin))

动态规划-DP算法指的不是单一一个算法，而是**一系列可以在给定满足MDP的完全可知环境模型中计算出最优策略的算法**。

DP的特点：

- Model-Based
- Value-Based
- Off-Policy(这个比较牵强，因为DP不涉及采样、预测，完全靠planning)

DP具有很重要的理论基础作用，但是在现在的强化学习问题中，DP并不常使用，主要原因有二：

- 需要完全可知的模型，状态空间、动作空间离散，状态转移、奖励函数可知且确定
- 计算量很大(每次更新都需要完全规划所有可能性)

在一些表格型的问题中，如完全可知的迷宫，可以使用DP，但是要解决人类现实世界极其复杂的问题、任务，DP可能就有些力不从心啦。

其实，所有的强化学习算法都可以被认为是在**不完全可知的环境**中使用**少量计算**得到如DP效果一样的策略（最优策略）。

# 算法



## 策略迭代 Policy Iteration



## 值迭代 Value Iteration



## PI与VI的比较



