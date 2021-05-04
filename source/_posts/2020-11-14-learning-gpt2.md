---
title: 对于GPT-2模型的学习(未完待续)
date: 2020-11-14 10:46:22
toc: true
categories:
  - NLP
tags:
  - deep learning
excerpt: 这是一篇对于GPT-2模型的学习笔记
---

>   GPT-2与BERT都是Transformer模型的衍生物，其中BERT是仅仅对Transformer模型中的encoder部分进行改造；而GPT-2是对Transformer模型中的decoder部分进行改造

对于Transfoemer的理解可以看之前看[这里](https://xdren69.github.io/2020/10/13/learning-transformer/)，BERT的理解可以看[这里](https://xdren69.github.io/2020/10/18/learning-Bert/)，接下来我们将详细介绍GPT-2，我们将分为如下几个部分介绍：

1.  整体模型：大致了解模型的整体结构
2.  Decoder详解：详解单个decoder中的计算原理
3.  GPT-2的应用：详解模型如何在实际中进行应用



## 整体模型

![GPT-2](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/15/161733-313323.png)

使用了Transformer的decoder部分，将多个decoder堆叠构成了decoder栈