---
layout: post
category : machine learning
tagline: "Supporting tagline"
tags : [word embeeding, deep learning]
---
{% include JB/setup %}

##Introduction

Neural word embedding使用上下文来编码word，编码信息在词之间发生多次传递和更新后(thru NN-architecture)，训练得到的word vectors在某些代数运算下表现出一定的语义和语法意义。例如，向量cosine可以衡量语义(语法)相似度，而向量相减则表达某些语义(语法)关系。

本文分为两部分：(1) Applications, 描述作者在训练和使用distributed word representation时的一些实践经验；(2) Explorations, 描述作者对于word embedding扩展工作的简单理解 (i.e., paragraph embedding, semantic relations based on word embedding)。

##Applications

###Preprocessing

作者使用word2vec toolkit (c语言版本) 来训练词向量，但构造算法输入和使用算法参数的方法稍有特别。

(1) word2vec以两个换行符间的词序列作为一个处理单元，如果其中包含的词数超过1000，则将其分裂成两个单元，以此类推。算法会在处理单元内部滑动窗口（窗口大小一般为5-10）来获取上下文。

(2) 构造输入的一般方法：corpus为一个文件，每个document为一行，document经过分词后以空格分隔，按原始顺序排列。

(3) 作者的方法：同样，corpus为一个文件，每个document为一行。对document做keyword extraction，得到words及其weights。过滤掉权重低的词，剩下的词以空格分隔，以任意顺序排列。调整窗口大小为1000，即采用document中的所有词来构造其中每个词的上下文。

(4) 这样做的好处：**舍弃语法关系，侧重语义关系。**通过实验发现，这样生成的词向量再去计算knn，在语义上的准确率有明显提高。下面是一个例子，左侧是一般方法的结果，右侧是作者方法的结果。

to be continued。。。

###Classification Task

###Matching Task

##Explorations