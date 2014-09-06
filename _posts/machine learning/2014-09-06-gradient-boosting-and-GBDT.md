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

> GBDT：一种gradient boosting的算法实现，即利用决策树或回归树来实现弱分类器。

##Gradient Boosting

(1) 输入training set：$(x, y) = \{x_i, y_i\}_1^N$

(2) 

$$F(x) = \sum_i^N h(x)$$

to be continued...