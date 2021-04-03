---
title: (KARN)A Knowledge-Aware Attentional Reasoning Network for Recommendation 论文笔记
date: 2020-12-01 17:20:51
toc: true
categories:
  - paper reading
excerpt: 本文介绍了一篇将商品推荐与知识图谱相结合的论文KARN
---

## KARN: A Knowledge-Aware Attentional Reasoning Network for Recommendation

### Intro

之前的工作要么只考虑使用知识图谱来解释user与item在知识图谱中之间path的合理性，要么只考虑通过用户的历史点击序列来预测用户的兴趣。本文将结合两者进行预测，这种预测方式将提高预测的准确率，具体如下：

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/07/094120-752547.png)

用户Jark的历史记录为3->2->1，如果不考虑历史记录，只根据**1和知识图谱**进行推荐，则由于Titantic的导演是James Cameron，因此可能推荐他执导的另外两部电影Piranha2和Aliens，但是通过**历史记录**可以得到Jark喜欢的电影类型更偏向于Adventure类型的电影，因此更有可能推荐Aliens而不是Piranha2

### 创新点

1. 提出KARN将用户的历史点击序列和知识图谱中的路径信息相结合
2. 对于item进行向量表示时，不仅考虑了textual（tittle）的信息，还考虑了contextual（its one-hop neighbors in KGs）的信息
3. 对于item进行向量表示时，不仅考虑用户的历史点击序列，还通过user和item之间的路径考虑了**user对于该item的潜在意图**

### 模型

#### **对于item的表示**

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/07/103758-690420.png)

其中

- 对于tittle中的每一个词的embedding组成的矩阵用F个卷积核进行卷积操作，用于提取局部语义信息（多个词组成的phrase的含义），最后经过一层变换得到与KG中实体维度相同向量V‘
- 对于该商品在KG中对应的entity的embedding用V来表示
- 对于该商品1-hop之内的neighbors entity采用**平均的方法**来得到向量V’‘

则item的表示最终为[V, V' , V'']

#### **对于user history的表示**

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/07/105331-105180.png)

对于历史点击的item，通过SRA网络来学习，SRA将RNN+attention得到的结果与RNN最后一个隐状态进行拼接来表示user history interest

#### **User potential intent**

即用户对于目标商品的潜在意图，通过所有在知识图谱中由用户到商品的路径来计算得出

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/07/105941-300239.png)

对于每一条路径，e表示实体，r表示关系：

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/07/110015-781535.png)

我们将（e<sub>i</sub>,r<sub>i</sub>）视为一个整体用一个向量来表示，在路径最后补上空关系r<sub>q</sub>，随后将将整条路径用多个这样的向量来表示，最后通过SRA学得整个路径的表示。将所有路径通过attention net得到User对于item的potential intent

#### **整体网络**

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/07/110840-705446.png)

### **实验结果**

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/07/111124-888823.png)

### **反思**

1. 实验存在缺陷，没有与其他使用知识图谱的模型进行对比，即KGCN，KGAT，RippleNet
2. 无法应用于新闻推荐，新闻的待推荐item的数量太多，无法每一个都参与user potential intent的计算
