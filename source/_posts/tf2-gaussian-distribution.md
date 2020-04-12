---
title: Tensorflow2.0中的高斯分布及其概率
copyright: true
mathjax: true
top: 1
date: 2020-04-08 10:50:12
categories: TensorFlow
tags:
- tf2
- rl
keywords:
description:
---

此篇博文用于记录和描述一些高斯分布的基本特性以及在tensorflow2.0中的不同之处。

<!--more-->

# 对角协方差高斯分布

对角协方差矩阵： Diagonal Covariance Matrix

多元高斯分布：multivariate Gaussian distribution

拥有对角协方差的多元高斯分布，其变量的概率密度等于各个变量的一元高斯概率密度之积。

假设对角协方差矩阵是如下形式：
$$
\Sigma =
\left(
  \begin{array}{cccc}
    \sigma_{1} & 0 & \cdots & 0 \\
    0 & \sigma_{2} & \cdots & 0 \\
    \vdots & \vdots & \ddots & \vdots \\
    0 & 0 & \cdots & \sigma_{k}
  \end{array}
\right)
$$
那么，多元高斯函数的概率密度函数被定义为：
$$
f_{x}\left(x_{1}, \ldots, x_{k}\right)=\frac{\exp \left(-\frac{1}{2}(\vec{x}-\vec{\mu})^{T} \Sigma^{-1}(\vec{x}-\vec{\mu})\right)}{\sqrt{|2 \pi \Sigma|}}
$$
变量替换：
$$
\vec{y}=\vec{x}-\vec{\mu}
$$
对角协方差的逆可以表示为：
$$
\begin{aligned}
\Sigma^{-1} &=\left(\begin{array}{cccc}
\sigma_{1}^{2} & 0 & \cdots & 0 \\
0 & \sigma_{2}^{2} & \cdots & 0 \\
\vdots & \vdots & \ddots & \vdots \\
0 & 0 & \cdots & \sigma_{k}^{2}
\end{array}\right)^{-1} \\
&=\left(\begin{array}{cccc}
\frac{1}{\sigma_{1}^{2}} & 0 & \cdots & 0 \\
0 & \frac{1}{\sigma_{2}^{2}} & \cdots & 0 \\
\vdots & \vdots & \ddots & \vdots \\
0 & 0 & \cdots & \frac{1}{\sigma_{k}^{2}}
\end{array}\right)
\end{aligned}
$$
代入上面式子，可以得到：
$$
\begin{align}
	f_x(x_1, \ldots, x_k) = \frac{\exp \Big(-\frac{1}{2} ( \frac{y_1^2}{\sigma_1^2} + \frac{y_2^2}{\sigma_2^2} + \ldots + \frac{y_k^2}{\sigma_k^2} ) \Big)}{\sqrt{(2\pi)^{k}|\Sigma|}}
\end{align}
$$
其中，分母的变化是由于矩阵秩的性质。对角协方差矩阵的秩可以展开写成：
$$
\begin{align}
	|\Sigma| &=
		\begin{vmatrix}
			\sigma_1^2 & 0 & \cdots & 0 \\
			0 & \sigma_2^2 & \cdots & 0 \\
			\vdots & \vdots & \ddots & \vdots \\
			0 & 0 & \cdots & \sigma_k^2 \\
		\end{vmatrix} \\
		&= \sigma_1^2 \cdot \sigma_2^2 \cdots \sigma_k^2
		\end{align} %]]>
$$
最后，代入原概率密度方程并化简：
$$
\begin{align}
	f_x(x_1, \ldots, x_k) &= \frac{\exp \Big(-\frac{1}{2} ( \frac{y_1^2}{\sigma_1^2} + \frac{y_2^2}{\sigma_2^2} + \ldots + \frac{y_k^2}{\sigma_k^2} ) \Big)}{\sqrt{(2\pi)^{k}\sigma_1^2 \cdot \sigma_2^2 \cdots \sigma_k^2}} \\
	&= \frac{\exp \Big(-\frac{1}{2} ( \frac{y_1^2}{\sigma_1^2} + \frac{y_2^2}{\sigma_2^2} + \ldots + \frac{y_k^2}{\sigma_k^2} ) \Big)}{\sqrt{2\pi\sigma_1^2 \cdot 2\pi\sigma_2^2 \cdots 2\pi\sigma_k^2}} \\
	&= \frac{\exp\Big(-\frac{y_1^2}{2\sigma_1^2} \Big)}{\sqrt{2\pi\sigma_1^2}}  \cdot \frac{\exp\Big(-\frac{y_2^2}{2\sigma_2^2} \Big)}{\sqrt{2\pi\sigma_2^2}}  \cdots \frac{\exp\Big(-\frac{y_k^2}{2\sigma_k^2} \Big)}{\sqrt{2\pi\sigma_k^2}} \\
	&= \frac{\exp\Big(-\frac{(x_1-\mu_1)^2}{2\sigma_1^2} \Big)}{\sqrt{2\pi\sigma_1^2}}  \cdot \frac{\exp\Big(-\frac{(x_2-\mu_2)^2}{2\sigma_2^2} \Big)}{\sqrt{2\pi\sigma_2^2}}  \cdots \frac{\exp\Big(-\frac{(x_k-\mu_k)^2}{2\sigma_k^2} \Big)}{\sqrt{2\pi\sigma_k^2}} \\
	&= f_1(x_1) \cdot f_2(x_2) \cdots f_k(x_k)
\end{align} %]]>
$$
这样就得到了，对角协方差矩阵的多元高斯分布，其变量的联合概率密度等于各个变量独立高斯分布概率密度的连积。

# TensorFlow 2.0 Gaussian Functions

```python
import numpy as np
import tensorflow as tf
import tensorflow_probability as tfp

dt = tf.float32

mu = tf.constant([1., 2.], dtype=dt)		# 均值
sigma = tf.constant([1., 2.], dtype=dt)	# 标准差
covariance = tf.constant([							# 协方差矩阵
  [1., 0.],
  [0., 4.]
])
x = tf.constant([0.92, 2.03], dtype=dt)	# 模拟一个样本
```

测试一下TF中几个不同高斯分布的1. 采样形式；2. 样本概率的表示。定义一个函数：

```python
def test_gaussian(dist, y):
  '''
  param dist: 传入的高斯分布
  param y: 传入的样本
  '''
  x = dist.sample()
  print(x)	# 输出分布的采样
  p = dist.prob(x)
  print(p)	# 输出采样样本的概率
  p_y = dist.prob(y)
  print('prob: ', p_y)	# 输出样本的概率
  print('sum_prob: ', tf.reduce_sum(p_y))	# 输出样本的概率之和
  print('prod_prob: ', tf.reduce_prod(p_y))	# 输出样本的概率之积
  log_p_y = dist.log_prob(y)
  print('log_prob: ', log_p_y)	# 输出样本的概率对数
  print('sum_log_prob: ', tf.reduce_sum(log_p_y))	# 输出样本的概率对数之和
  print('prod_log_prob: ', tf.reduce_prod(log_p_y))	# 输出样本的概率对数之积
```



## Normal

`test_gaussian(tfp.distributions.Normal(loc=mu, scale=sigma), x)`，其参数中的scale为标准差。

输出为：

```
tf.Tensor([-0.24812889  2.8150756 ], shape=(2,), dtype=float32)
tf.Tensor([0.18307646 0.1835755 ], shape=(2,), dtype=float32)
prob:  tf.Tensor([0.3976677  0.19944869], shape=(2,), dtype=float32)
sum_prob:  tf.Tensor(0.5971164, shape=(), dtype=float32)
prod_prob:  tf.Tensor(0.07931431, shape=(), dtype=float32)
log_prob:  tf.Tensor([-0.9221385 -1.6121982], shape=(2,), dtype=float32)
sum_log_prob:  tf.Tensor(-2.5343368, shape=(), dtype=float32)
prod_log_prob:  tf.Tensor(1.4866701, shape=(), dtype=float32)
```

这个分布为各个变量的独立高斯分布，有$n$个变量，则采样出$n$个值，其概率为每个变量各自的概率，如$n=2$，上面结果给出概率为：prob:  tf.Tensor([0.3976677  0.19944869], shape=(2,), dtype=float32)。

## MultivariateNormalDiag

`test_gaussian(tfp.distributions.MultivariateNormalDiag(loc=mu, scale_diag=sigma), x)`，其参数中的scale_diag为协方差矩阵对角线的平方根，也就是每个变量对应的标准差。

输出为：

```
tf.Tensor([2.550062  1.5906484], shape=(2,), dtype=float32)
tf.Tensor(0.02343988, shape=(), dtype=float32)
prob:  tf.Tensor(0.07931432, shape=(), dtype=float32)
sum_prob:  tf.Tensor(0.07931432, shape=(), dtype=float32)
prod_prob:  tf.Tensor(0.07931432, shape=(), dtype=float32)
log_prob:  tf.Tensor(-2.5343366, shape=(), dtype=float32)
sum_log_prob:  tf.Tensor(-2.5343366, shape=(), dtype=float32)
prod_log_prob:  tf.Tensor(-2.5343366, shape=(), dtype=float32)
```

这个分布于各变量独立的高斯分布相同，其协方差矩阵为对角矩阵，与Normal不同的是，其概率为每个变量各自概率之积，即联合概率。其概率对数为每个变量各自概率对数之和。

prob = Normal: prod_prob

log_prob = Normal: sum_log_prob

## MultivariateNormalFullCovariance

`test_gaussian(tfp.distributions.MultivariateNormalFullCovariance(loc=mu, covariance_matrix=covariance), x)`，其参数中的covariance_matrix为协方差矩阵，因此如果使用`MultivariateNormalDiag`并指定对角线参数为`scale_diag=[1,2]`，那么实现相同的分布使用`MultivariateNormalFullCovariance`应指定协方差矩阵参数为`covariance_matrix=[[1,0],[0,2]]`。

输出为：

```
tf.Tensor([3.1393874 1.2191284], shape=(2,), dtype=float32)
tf.Tensor(0.007478423, shape=(), dtype=float32)
prob:  tf.Tensor(0.07931432, shape=(), dtype=float32)
sum_prob:  tf.Tensor(0.07931432, shape=(), dtype=float32)
prod_prob:  tf.Tensor(0.07931432, shape=(), dtype=float32)
log_prob:  tf.Tensor(-2.5343366, shape=(), dtype=float32)
sum_log_prob:  tf.Tensor(-2.5343366, shape=(), dtype=float32)
prod_log_prob:  tf.Tensor(-2.5343366, shape=(), dtype=float32)
```

这个分布为多个变量的联合高斯分布，如果设置协方差矩阵为对角矩阵，且为`MultivariateNormalDiag`对角参数的平方，则两个分布一致。

## TruncatedNormal

```python
tfp.distributions.TruncatedNormal(
    loc, scale, low, high, validate_args=False, allow_nan_stats=True,
    name='TruncatedNormal'
)
```

这个分布相比`Normal`多了两个参数：low和high，如果样本＞high或者＜low，则其概率为0，对数概率为-inf。使用这个分布采样的样本也在low和high之间。

```python
dist = tfp.distributions.TruncatedNormal(loc=[0., 1.], scale=1.0, low=[-1., 0.], high=[1., 1.])
print(dist.prob([1.1, -0.1]))
print(dist.log_prob([1.1, -0.1]))

结果：
tf.Tensor([0. 0.], shape=(2,), dtype=float32)
tf.Tensor([-inf -inf], shape=(2,), dtype=float32)
```

