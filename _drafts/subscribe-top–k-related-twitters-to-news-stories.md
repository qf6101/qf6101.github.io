---
layout: post
category : information retrieval
tagline: "Supporting tagline"
tags : [information retrieval, query processing, skipping technique]
---
{% include JB/setup %}

##Introduction

本文简要描述了Shraer $et\ al.$在VLDB 2013上的一个工作[^1]：依据内容相关性，将twitters实时注册到news stories中(billions to millions per day)，维护每个story始终有top-$k$个最相关的twitters。

本文分为两个部分：(1) primitive techniques：描述信息检索系统中的两种查询处理策略(TAAT and DAAT)以及查询处理中的skipping技术；(2) their contributions[^1]：描述在常规查询处理的基础上做改进，构建一个top-$k$ publish-subscribe系统。

##常规查询的两种策略

(1) 查询处理的问题设置如下。

**Given：**(1) inverted index $I$ of stories $S$, (2) query $u=\\{u_1,u_2,...,u_n\\}$ and (3) $k$

**Return：**top-$k$ related story set $R$ to $u$

**Methods：**TAAT, DAAT, etc.

无论使用哪种查询策略，都会首先在索引中找到$u$对应的posting lists $\\{L_1,L_2,...,L_n\\}$(每个term对应一个list)。实际的搜索空间就是这个list set。

(2) Term-at-a-time (TAAT)

TAAT采用纵向扫描的策略：从上到下依次遍历每个posting list，遍历过程中对相应的document增加得分，最后将document按得分逆序输出(截取前$k$个)。

(3) Document-at-a-time (DAAT)

DAAT采用横向扫描的策略：(a) 事先将posing list中的documents按doc id升序排序，并对每个posting list都保留一个当前位置指针；(b)对于所有的当前位置指针，取doc id最小的那个，计算该document的得分，并将相应的指针往后移动一位；(c) 重复上述b过程直至所有posting list都被遍历完。最后将documents按得分逆序输出。

(4) Discussions

TAAT和DAAT各自的优点也是对方的缺点：(a) TAAT需要在内存中保存所有documents的分值累加器，而DAAT只需要保存一个size为$k$的min-heap；(b) 由于posting list一般是顺序存储在disk blocks中的，因此DAAT需要在blocks之间跳跃寻址，而TAAT不用。

##常规skipping技术

(1) 什么是skipping技术

skipping技术的种类很多，目的是为了提高效率，主要思想是在search过程中跳过一些对结果无影响或影响较小的posting lists或postings。

比如，approximately search会选择跳过一些page rank值较小的documents；或者将生僻的terms作为必要查询条件(先search那些生僻terms对应的posting lists，这样一来其他posting lists可以跳过好多无关的documents)。

下面两节分别描述TAAT和DAAT策略是怎样exactly skip posting lists的。主要思想都是确定一个upper bound(UB for short)，然后估计会不会连UB都够不着。

(2) skipping in TAAT

首先定义posting list的upper score：$ms\\(L_i\\)=max ps(s,i)$。其中，$ps(s,i):=\frac{cs(s,u_i)}{u_i}$表示term $u_i$对于$cs(s,\cdot)$的贡献值，分母是$u_i$在s中的权重(比如tf)。$cs(s,u_i)$是content based score，可以用tf-idf(这时ps即为idf)、cosine、BM25作为measures。

TAAT检查下面的条件，如果满足就一次性skip掉剩下所有的posting lists。

(3) skipping in DAAT

(4) Discussions

通俗地讲，TAAT以term为单位进行skip，而DAAT以document为单位进行skip。实际应用中，$|u|$较小，而posting list的avg. length较长，因此DAAT的效率会比TAAT的效率高。

##Reference

[^1]: Alexander Shraer $et\ al.$ Top-k Publish-Subscribe for Social Annotation of News. In Proceedings of VLDB, 2013.