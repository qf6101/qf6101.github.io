---
layout: post
category : machine learning
tagline: "Supporting tagline"
tags : [word embedding, deep learning]
---
{% include JB/setup %}

##Introduction

word embedding是指通过低维向量来编码词汇，把词典嵌入到一个低维空间。由于实际训练时使用上下文信息来编码词汇，低维空间中的word vectors在代数运算下表现出一定的语义和语法相似性，应用场景丰富。这种将词汇从0-1表示的离散稀疏空间映射到连续低维空间的方法，有点类似于低秩逼近。本文简要描述neural word embedding的动机和常见训练方法。

##Motivations

(1) language model才是终极目标

统计NLP的一个重要任务是训练language model，用以估计给定上下文情况下任意词汇出现的概率$P\(w\|content\)$，从而进一步估计paragraphs的概率。比较熟知方法比如log linear model，而另一种重要的方法就是神经网络。神经网络方法中，隐藏层会编码词汇以及词汇的组合关系，这里的词汇编码其实就是word vectors。

那么问题来了，要模拟某种自然语言的分布，需要一个非常复杂和deep的神经网络架构，一次性训练这个网络并不是很好的办法。word2vec toolkit的作者Mikolov提出首先构建一个简单的网络来编码出质量较高的word vectors，第二步才把这些词向量扔到复杂神经网络中进一步训练language model。

这与deep learning的策略类似，deep learning技术通过autoencoder、RBM等方法首先在浅层网络中训练好参数的初始值，再利用back propagation算法来训练整个网络。我们这里训练的word vectors相当于是language model神经网络中的参数初始值。

(2) 直接应用于NLP的各种任务

由于利用上下文来编码词汇，训练出来的word vectors表现出代数运算下的语义和语法相似度。因此，也可以直接应用到text matching、text classification等场景。

##Methods

从文献来看，训练word vectors的神经网络架构一直在不断简化。简化的原因有两个：(1) 效率问题：复杂的架构会引入大量参数，训练时间过长，也影响质量；(2) 目标的转换：神经网络的训练目标已经从language model转变成了word vectors，具体而言就是词向量的夹角距离表示词的语义和语法相似度(也存在不同但类似的定义)，因而网络结构也要顺势变化，实验发现简单而浅层的网络已经可以应付这一目标。

大多数做word embedding的文献都围绕着两个问题展开研究：architecture and efficiency，下面做些简单介绍。

**ARCHITECTURES**

(1) CBOW and skip-gram

这是Mikolov提出的两个经典架构[^1][^2]，它们的预测方向正好相反。CBOW使用上下文来预测词汇，而skip-gram使用词汇来预测上下文。

CBOW(continues bag-of-words)模型使用向量求和的方式来构建上下文编码，无视了上下文的顺序信息，因而叫做bag of words。如果采用concatenate的方式构建上下文编码，虽然利用了上下文顺序，但是需要更复杂的网络架构和更大的训练语料来支撑。在模型中，上下文编码直接作为softmax的输入来预测任意词汇的概率。

skip-gram模型是反过来用词汇预测上下文，有什么好处呢？在神经网络模型中，word vectors是模型参数。不考虑归一化，输入一个样本(上下文+某词)，CBOW每次更新一个参数(某词)，而skip-gram每次更新多个参数(上下文中的每个词)。这自然会加快信息传递的速度，减少训练时间。

(2) GloVe model

GloVe模型[^3]在经验基础上，进一步明确了词向量训练的代数目标，就是使得两个词向量的点乘逼近一个词在另一个词上下文中出现的概率。GloVe模型首先统计出共现矩阵，然后再基于该矩阵最小化上面两个量之间的差异。相比CBOW模型和skip-gram模型，GloVe只是用了全局统计信息(所以叫global vector model)，可能会丢掉一些细节信息，而且模型优化的余地较小(感觉有点hand-craft了)，也会受高频词的影响(不过这是可以简单改进的)。

**EFFICIENCY**

神经网络后端的softmax是计算复杂度的瓶颈。为了提高word embedding的效率，除了简化网络规模外，降低softmax的训练复杂度是至关重要的。softmax最大的问题是需要更新的参数太多了，与词典规模相同。计算梯度时，做整体归一化的计算量也有相同的规模：$p\(y^{\(i\)} = j \| x^{\(i\)} ; \theta\) = \frac{e^{\theta_j^T x^{\(i\)}}}{\sum_{l=1}^k e^{ \theta_l^T x^{\(i\)}} }$ ($x_i$是上下文向量，$y_i$是词索引，$\theta$是词向量，$k$是词典规模)。下面介绍两种解决方法。

(1) hierarchical softmax

首先构建词典的哈夫曼编码树，词汇分布在叶子节点中，词频大的词具有较短的path，而词频小的词具有较长的path。在估计$P\(w\|content\)$时，将上下文编码从根节点输入，经过对应的path达到词$w$。路径上的每个非叶子节点都是一个基于logistic function的二分分类器。每个分类器包含一组参数，以与word vector相同维度的向量表示。

对于每个叶子节点(对应要预测的词)，它的路径就表示了一组联合概率分布，即这些logistic functions的连乘。根据某个样本的预测词去更新模型，就是更新对应叶子节点路径的联合概率分布函数参数。每个样本进入的时候，都只需要更新若干条路径上的参数(不同模型更新的路径条数不同，下个段落会具体说明)，与原始softmax方法更新整个word vectors矩阵相比，计算复杂度降低至O(path length)。词频高的词包含的信息较少，因此路径上非叶子节点个数也少，这是选择哈夫曼编码树的原因。

CBOW模型中，由于用上下文来用预测词汇，因此每个输入样本(上下文+某词)都只会更新一条路径上的参数；而skip-gram模型中，用词汇来预测上下文，每个输入样本都会更新上下文中每个词对应的路径上的参数。正如上文所说，skip-gram的训练效率更高。

(2) negative sampling

hierarchical softmax相当于是用树的形式对词向量矩阵(即参数矩阵)做了压缩，采用深度遍历的方式更新局部参数。negative sampling则有点像广度遍历，对于每个正样本，都随机采样负样本，将正负样本都扔到logistic functions中进行联合概率估计。采样的个数与词典规模比要小得多，根据Mikolov描述，小样本集的采样数在5~20之间，大样本集在2~5之间。

(3) subsampling of frequent words

上面两个策略都是用来改进softmax的计算复杂度，而subsampling则从样本优化的角度来提高效率，同时也提高训练质量。

直觉上，高频词提供的信息要比低频词少，甚至会影响隐含的模式。因此subsampling就以一定的概率从语料中去掉高频词，这使得训练出的低频词向量具有更好的语义和语法相关性。同时也提高了计算效率。

##References

[^1]: Tomas Mikolov, Kai Chen, Greg Corrado, and Jeffrey Dean. Efficient Estimation of Word Representations in Vector Space. In Proceedings of Workshop at ICLR, 2013.

[^2]: Tomas Mikolov, Ilya Sutskever, Kai Chen, Greg Corrado, and Jeffrey Dean. Distributed Representations of Words and Phrases and their Compositionality. In Proceedings of NIPS, 2013.

[^3]: Jeffrey Pennington, Richard Socher, Christopher D. Manning. GloVe: Global Vectors forWord Representation. In Proceedings of EMNLP, 2014.