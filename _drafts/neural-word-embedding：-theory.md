---
layout: post
category : machine learning
tagline: "Supporting tagline"
tags : [word embedding, deep learning]
---
{% include JB/setup %}

##Introduction

word embedding的意思是通过低维向量来编码词汇，把词汇集合嵌入到一个低维空间。由于实际训练时使用上下文信息来编码词汇，低维空间中的word vectors在代数运算下能表现出一定的语义和语法相似度，应用场景丰富。将词汇从0-1表示的离散稀疏空间映射到连续低维空间，有点类似于低秩化方法。本文简要描述neural word embedding的动机和常用训练方法。

##Motivations

(1) language model才是终极目标

统计NLP的一个重要任务是训练language model，用以估计给定上下文情况下某一词汇出现的概率，从而进一步估计sentence的概率。比较经典方法比如log linear model，而另一种常用的方法就是神经网络。神经网络方法中，隐藏层会编码词汇以及词汇的组合关系，词汇编码其实就是word vectors。

那么问题来了，要模拟某种自然语言的分布，需要一个非常复杂和deep的神经网络架构，一次性训练好这个网络并不是很好的办法。word2vec的作者Mikolove想到的一个策略是首先构建一个简单的网络来编码出质量较高的word vectors，再把这些词向量扔到复杂网络中来训练language model。

这与deep learning的策略类似，deep learning技术通过auto encoder、RBM等方法首先在浅层网络中训练好参数的初始值，再利用back propagation来训练整个网络。我们这里训练的word vectors相当于是language model网络的参数初始值。

(2) 直接应用于NLP的各种任务

由于利用上下文来编码词汇，训练出来的word vectors表现出代数运算下的语义和语法相似度。因此，也可以直接应用到text matching、text classification等场景。

##Methods

1. architectures



2. efficiency

##References

[^1]: Alexander Shraer $et\ al.$ Top-k Publish-Subscribe for Social Annotation of News. In Proceedings of VLDB, 2013.