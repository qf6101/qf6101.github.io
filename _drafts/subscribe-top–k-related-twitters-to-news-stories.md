---
layout: post
category : machine learning
tagline: "Supporting tagline"
tags : [information retrieval, query processing, skipping technique]
---
{% include JB/setup %}

##Introduction

本文简要描述了Shraer $et\ al.$在VLDB 2013上的一个工作[^1]：依据内容相关性，将twitters实时注册到news stories中(billions to millions per day)，维护每个story有top-$k$个最相关的twitters。

本文分为两个部分：(1) primitive techniques：描述信息检索系统中的两种查询处理策略(TAAT and DAAT)以及查询处理中的skipping技术；(2) their contributions：描述在常规查询处理的基础上做改进，构建一个top-$k$ publish-subscribe系统。

##常规查询的两种策略

查询处理的问题设置如下。

**Given：**(1) inverted index $I$ of stories $S$, (2) query $u=\\{u_1,u_2,...,u_n\\}$ and (3) $k$

**Return：**top-$k$ related story set $R$ to $u$

**Methods：**TAAT or DAAT

(1) Term at a time (TAAT)

对于$u$

(2) Document at a time (DAAT)

##常规skipping技术

##References

[^1]: Alexander Shraer $et\ al.$ Top-k Publish-Subscribe for Social Annotation of News. In Proceedings of VLDB, 2013.