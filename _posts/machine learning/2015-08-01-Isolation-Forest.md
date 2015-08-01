---
layout: post
category : machine learning
tagline: "Supporting tagline"
tags : [machine learning, anomaly detection, outlier detection]
---
{% include JB/setup %}

##Introduction

Isolation Forest（简称iForest）[^1]是一种孤立点检测算法，与LOF等传统方法相比具有更高的检测质量和检测效率。它在效率上的优势尤为明显，甚至可以作为在线检测工具。

下文分为4个部分：首先介绍孤立点检测的应用领域；第二部分介绍iForest的算法步骤；第三部分分析iForest奏效的原因；最后一部分讨论孤立点检测与稀有类发现的差异。

##Domain Applications

孤立点检测在信息安全领域尤为重要，常用于欺诈检测、分析控制等问题，能及时发现异常交易、异常用户和异常信息流。不仅如此，孤立点检测还可以作为数据预处理手段，例如可以为文本分类器剔除错误标记的语料，为聚类任务去噪。

##Algorithms

iForest是一种二阶段算法（说到二阶段，是不是想到了FP-Growth）。

第一阶段，构建$t$个iTree组成的森林。其中，每个iTree都只使用从$n$个整体数据中均匀采样的$\psi$个样本。构建iTree的步骤非常简单：每次随机选一个属性和分割值，对样本点做二分划分；迭代左右子节点，直至达到终止条件（不可分或高度超过$\log\psi$）。时间复杂度是$O(t\psi\log(\psi))$。

第二阶段，对被检测样本计算abnormaly score。该得分的取值范围在0~1之间，得分越高，是孤立点的可能性越大。这个得分的定义是算法的关键，也是采用随机划分方式来构建iForest的原因。样本点$x$的abonormaly score定义如下式所示，其中：$h(x)$是$x$在iTree $h$中检索到的节点的高度；$E[\cdot]$是对所有$t$个iTree取均值；$c(\psi)$是$\psi$个点构建的二分搜索树的平均路径长度（它是标准化项）。所以，第二阶段其实是对每个iTree进行检索，并将检索结果($h(x)$)带入到式子中计算得分。检测所有n个点的时间复杂度是$O(nt\log(\psi))$。

$$
s(x)=2^{-\frac{E[h(x)]}{c(\psi)}}
$$

对于高维数据，iForest做了改进，每个iTree分别采用Kurtosis test对属性做了筛选。随机划分决策时，只考虑那些容易引起空间位置突变的属性。（在文本分类任务中还可以采用卡方检验，结合那些对分类有意义的特征）。此外，实验表明，算法的两个参数（$t$和$\psi$）对于孤立点检测结果的影响较为稳定。

##Effectiveness Analysis

这里要分析两件事情：

- 为什么这样定义abnormaly score？

数据挖掘方法中的定义（Definition）通常来源于两种依据。第一种是Observation，第二种是Theorem。前者靠直觉和观测，后者靠假设和推导。iForest属于第一种，由于孤立点different和few的特点，加之观测到孤立点在构建iTree的过程中较早到达叶子节点（$h(x)$值较小），才有了这样的定义。

- iForest为什么会比LOF等传统算法管用？

（1）浅层原因：首先，LOF等算法需要计算k-nearest-neighbor（简称kNN）信息，因而它费时；其次，对于compact的abnormalies区域，LOF对于孤立点的定义不是很适用；最后，在高维数据中，前两个问题更为突出。

（2）深层原因：上文中，我提到了FP-Growth算法。它之所以会比经典的Apriori算法快这么多，是因为它在第一阶段抽取了挖掘频繁模式的充足信息并压缩到了一棵FP-Tree中。大量没用的信息被扔掉了，因为他们对于挖掘目标没有帮助。相比之下，Apriori是在大海里捞针。iForest做了FP-Growth同样的事情。反过来，LOF定义孤立点时用到了kNN；但是kNN包含的信息太多了，它除了可以用来定义孤立点外，还可以定义区域密度、完成聚类任务[^2]等等。

##Comparison with Rare Category Detection

之所以提到稀有类检测[^3]，是因为iForest已经可以部分地检测稀有类。iForest对于那些compact的abnormalies区域有检测作用，这是LOF等传统方法做不到的。而这样的区域也是稀有类检测问题的目标区域。

但是，稀有类检测的问题定义是找到那些突发的密集区域，这样的区域也可能嵌入在整体数据的内部，这是iForest无法检测到的。稀有类检测的应用领域，包括路由器的突发性流量检测等。

##References

[^1]: Fei Tony Liu $et\ al.$ Isolation Forest. In Proceedings of ICDM, 2008.

[^2]: Alex Rodriguez $et\ al.$ Clustering by fast search and find of density peaks. Science, 27 June 2014.

[^3]: Hao Huang $et\ al.$ CLOVER: a faster prior-free approach to rare-category detection. Knowledge and information systems 35 (3), 713-736.
