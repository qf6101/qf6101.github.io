---
layout: post
category : machine learning
tagline: "Supporting tagline"
tags : [EM Algorithm]
---
{% include JB/setup %}

##Introduction

EM(Expectation-Maximization)与Gradient Boosting类似，其实是一种算法框架。当算法模型中存在隐藏变量，并且无法把隐藏变量积分掉(i.e., 不能转化成closed-form)时，EM采取迂回策略求解。

##A Simply Comprehensible Inference

似然函数\\( p(x;\theta) \\)化作包含隐藏变量的形式\\( \sum\limits_{z} p(x,z;\theta) \\)后，可以做如下分解。其中，\\( \theta \\)是模型参数，\\( q(z) \\)是隐藏变量\\( z \\)的分布函数。

$$
\ln p(x;\theta) = \mathcal{L}(q;\theta) + KL(q \parallel p)
$$

右式的两部分分别定义如下。

$$
\mathcal{L} (q;\theta) = \sum\limits_{z} q(z) \ln \{ \frac{p(x,z;\theta)}{q(z)} \}
$$

$$
KL(q \parallel p) = - \sum\limits_{z} q(z) \ln \{ \frac{p(z|x;\theta)}{q(z)} \}
$$

由于\\( KL(q \parallel p) \geq 0 \\)，因此 \\( \mathcal{L} (q;\theta) \\)是对数似然\\( \ln p(x;\theta) \\)的下界。为了抬高下界，可以令\\( KL(q \parallel p) = 0 \\)，即用\\( z \\)在数据集\\( x \\)和模型参数\\( \theta^{(i)} \\)(表示第\\( i \\)次迭代中\\( \theta \\)的估计值，\\( \theta^{(0)} \\)是初始参数值)已知情况下的后验概率来估计\\( q(z) \\)。这就是E-step，后验概率通常可以用贝叶斯公式展开求解。**E-step:**

$$
q(z) = p(z|x; \theta^{(i)})
$$

在抬高下界的约束下，我们再去最大化\\( \mathcal{L} (q;\theta) \\)(此时只有\\( \theta \\)是变量)。这就是M-step，即估计新的\\( \theta^{(i+1)} \\)来最大化对数似然\\( \ln p(x;\theta) \\)(此时\\( \ln p(x;\theta) = \mathcal{L} (q;\theta) \\))的期望。**M-step:**

$$
\theta^{(i+1)} = \underset{\theta}{\operatorname{arg\,max}}\ \mathcal{L} (L) (q; \theta)
$$

循环迭代，通过不断推高对数似然的下界，来最大化它。

##A More General Form

事实上，E-step(**expectation**)是指计算对数似然的期望表达式，M-step(**maximization**)是指估计新的参数\\( \theta \\)来最大化对数似然的期望表达式值。因此，E-step可以直接表示成用后验概率来估计\\( q(z) \\)时，对数似然的期望形式(期望就是乘上分布函数求积分)。观察发现，这个期望表达式就是\\( \mathcal(L) (q;\theta) \\)的分子部分，而负熵则是分母部分。由于熵与参数变量\\( \theta \\)无关，在M-step中对参数求梯度时它是常数，梯度为0。

$$
Q(\theta|\theta^{(i)}) = \operatorname{E}_{z \sim p(z|x;\theta^{(i)})}\left[ \ln p(x,z;\theta)  \right] = \sum\limits_{z} p(z|x;\theta^{(i)}) \ln p(x,z;\theta) = \mathcal{L} (q;\theta) - H(p(z|x;\theta^{(i)}))
$$

M-step就是最大化上面的期望表达式值，采用拉格朗日乘数法估计\\( \theta \\)(有时还会包含一些约束条件)。对\\( \theta \\)中的某具体参数求梯度时，其他参数即为常数，表达式会大大简化。

$$
\theta^{(i+1)} = \underset{\theta}{\operatorname{arg\,max}} \ Q(\theta|\theta^{(i)}) \
$$

注意，极大似然估计时，基于样本独立同分布的假设，要将每个样本的对数似然相加\\( \theta^{(i+1)} = \underset{\theta}{\operatorname{arg\,max}} \sum\limits_{t} \sum\limits_{z} p(z^{(t)}|x;\theta^{(i)}) \ln p(x^{(t)},z^{(t)};\theta) \\)。
