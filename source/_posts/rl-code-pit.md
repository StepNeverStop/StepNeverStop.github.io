---
title: 在DRL路上踩过的坑
copyright: true
mathjax: true
top: 1
date: 2020-05-21 17:31:45
categories: ReinforcementLearning
tags:
- RL
keywords:
description:
---

本博客用于记录在RL学习过程中踩过的坑点。

<!--more-->

---



1. 确定性策略梯度算法如DPG、DDPG、TD3等在优化Actor网络时是否引入动作噪声

   **通过大量实验发现，在重新选择动作优化Actor网络时不需要引入噪声，直接使用确定性动作即可，引入噪声会影响算法的性能，甚至导致策略不收敛，这是当我在调试分层强化学习算法HIRO时发现的。**

   之前的版本是：

   ```python
   if self.is_continuous:
       mu = self.actor_net(feat)
       pi = tf.clip_by_value(mu + self.action_noise(), -1, 1)
   else:
       logits = self.actor_net(feat)
       logp_all = tf.nn.log_softmax(logits)
       gumbel_noise = tf.cast(self.gumbel_dist.sample([batch_size, self.a_counts]), dtype=tf.float32)
       _pi = tf.nn.softmax((logp_all + gumbel_noise) / self.discrete_tau)
       _pi_true_one_hot = tf.one_hot(tf.argmax(_pi, axis=-1), self.a_counts)
       _pi_diff = tf.stop_gradient(_pi_true_one_hot - _pi)
       pi = _pi_diff + _pi
   q_actor = self.q_net(feat, pi)
   ```

   现在的版本是：

   ```python
   if self.is_continuous:
       mu = self.actor_net(feat)
   else:
       logits = self.actor_net(feat)
       _pi = tf.nn.softmax(logits)
       _pi_true_one_hot = tf.one_hot(tf.argmax(logits, axis=-1), self.a_counts, dtype=tf.float32)
       _pi_diff = tf.stop_gradient(_pi_true_one_hot - _pi)
       mu = _pi_diff + _pi
   q_actor = self.q_net(feat, mu)
   ```

   

2. 