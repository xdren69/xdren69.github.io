---
title: (KGCN)Knowledge Graph Convolutional Networks for Recommender Systems 论文笔记
date: 2020-11-30 17:22:17
toc: true
categories:
  - paper reading
excerpt: 本文介绍了一篇将商品推荐与知识图谱相结合的论文KGCN，其特点是进行用户和商品间子图的交互
---

## KGCN: Knowledge Graph Convolutional Networks for Recommender Systems

### Intro

每个item都有很多属性，这些属性之间能够相互联系，从而形成一张知识图谱。利用这张知识图谱，我们可以发现item之间的高阶语义信息和结构信息。从而做出更加准确的推荐。对于知识图谱的处理我们提出了KGCN的方法，即将GCN运用到知识图谱中，用以形成对于每个entity（item）的表示。

### 模型

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/13/100654-5848.png)

如上图所示，KGCN在本文中的应用是：规定感受野的跳数H（图中为2），以及每个实体单跳感受野中要考虑的节点个数（图中为2）。则KGCN的计算顺序是：**不学习最外层的节点，先学习红色的节点，后学习蓝色的节点**。

整体算法如下所示：

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/13/101319-125780.png)

>   注意：当计算一个实体的上下文感受野信息时，对于单跳感受野中每个实体的权重设置不同

算法图示如下：

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/13/101544-573368.png)

### 实验结果

与其他baseline的对比结果如下：

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/14/085000-230172.png)

- PER需要人工设计meta-path，因此准确性会低很多

同时通过实验探究了K，H，dimension的大小对于实验准确性的影响

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/13/102721-583322.png)

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/13/102741-163332.png)

### 反思

1. 对于感受野中固定实体的选择采用的是同一的方法，未来可以根据实体的重要性进行选择
2. 将user和item分隔开，只对item端使用KG进行表示学习，没有尝试加入user的信息或者对user也使用KG进行表示学习
