---
layout: post
category : machine learning
tagline: "Supporting tagline"
tags : [gradient boosting, GBDT]
---
{% include JB/setup %}

##Introduction

本文简要描述了gradient boosting算法框架和GBDT (gradient boosting desicion tree)算法。

##Basic Concepts

> boosting: 提升思想，即通过训练并组合一组弱分类器，来构造一个强分类器。

> gradient boosting：一种boosting算法框架，即利用gradient descent策略逐一训练弱分类器。

> GBDT：一种gradient boosting的算法实现，即利用决策树来实现弱分类器。

##Gradient Boosting Processes

**Given:** (1) 训练集 \\( S_0 := (x, y) = \\{x_t, y_t\\}_{t=1}^N \\); (2) 损失函数 \\( L(\cdot) \\)

**Output:** 一组弱分类器的线性组合 \\( F(x) = \sum_{i=0}^M \rho_i h_i(x) \\)

**Variables:** 前\\( j \\)个分类器的线性组合 (即\\( j \\)状态的最优模型) \\( F_j(x) := \sum_{i=0}^j \rho_i h_i(x) \\) where \\(j \geq i \\)

**Method:** 依次训练弱分类器 \\( h_i(x) \\)，并估计权重 \\(\rho_i \\)

(1) 训练 \\( h_0(x) \\): 基于原始训练集 \\( S_0 \\) 估计模型参数，并且 \\( \rho_0 = 1 \\)

(2) 训练 \\( h_i(x)\\) (\\( 0 < i \leq M \\))

(2.1) 生成训练集 
$$
S_i[t] = \left( x_t, -\left[\frac{\partial L(y, F(x_t))}{\partial F(x_t)}\right]_{F(x)=F_{i-1}(x)} \right)
$$

(2.2) 基于数据集\\( S_i \\) 估计模型参数

(2.3) 估计分类器权重 
$$
\rho_i = \underset{\rho}{\operatorname{arg\,min}} \sum_{t=1}^N L\left(y_t, F_{i-1}(x_t) + \rho h_i(x_t)\right)
$$

(3) 返回组合分类器 $$F_M(x)$$

##Explainations on Gradient Boosting

(1) 训练\\( h_i(x) \\)是为了纠正\\( F_{i-1}(x) \\)的误差，更好的fit数据。

(2) 对损失函数求\\( F(x_i) \\)的梯度时，\\( F(x_j) \\) (\\( i \neq j \\))都相当于常数。

(3) 损失函数其实是\\( F(x) \\)的functional，可以理解为子空间。要最小化损失函数，就是要找子空间的最低处。求梯度，就是找向着最低处的下降方向。在那些下降快的$$x_t$$位置，我们加大了该点\\( S_i[t]$y \\)的惩罚 (梯度取负号)，因为它一般来说距离最低处更远。

(4) gradient相对于residual，对于噪声数据不敏感。

## GBDT

GBDT是用决策树实现弱分类器的gradient boosting算法。为了防止过拟合，提高泛化能力，采取了以下一些策略。

(1) 限制叶子节点的个数，e.g., 叶子节点个数为2时，每个分类器都是个大裤衩树桩(decision stump)。同时也可以限制叶子节点中的元素个数不要太少。

(2) shrinkage: 在\\( \rho h(x) \\)前再乘上一个系数 (e.g., 0.05)，这时迭代次数M会增加。

(3) stochastic gradient boosting: 即采样，不用全样本训练。random forests也是这个思路。