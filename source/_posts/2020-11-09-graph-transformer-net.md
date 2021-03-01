---
title: Graph Transformer Networks 论文阅读
date: 2020-11-09 17:04:13
toc: true
tags:
  - GNN
  - deep learning
categories:
  - paper reading
excerpt: 这是一篇关于图自动剪裁的论文，通过soft selection剪裁节点间无用的联系，新增有用的联系
---

### 目前挑战

目前GNN被设计来在**固定和同质图**上学习节点表示，在以下情况下会存在缺陷：

1.  图中的连接存在错误
2.  包含不同类型节点和边的异质图中

目前的解决办法是使用两阶段方法：

1.  **手动设计**meta-path，需要专家和领域知识
2.  通过meta-path将异质图转化为同质图，然后使用GNN

本文是对上述方法的改进



### 主要贡献

1.  提出Graph Transformer Networks（GTN），其特点是：能够产生新的图结构，即识别出原本未连接的节点间的有用连接，从而学得更好的节点表示，**不需要依赖领域知识**
2.  新图的生成是可解释的，自动生成meta-path，不需要人为设定，meta-path的生成更加有效



### 先置概念

meta-path：

对于关系：$A \stackrel{A P}{\longrightarrow} P \stackrel{P C}{\longrightarrow} C$，其meta-path为$A_{A P}A_{P C}$



### 模型

异质图中存在多种关系，用一个邻接矩阵表示一种关系，整个异质图用K个邻接矩阵来表示（一共有K种关系）

#### Graph Transformer (GT) Layer

核心部分是Graph Transformer (GT) Layer，由两个步骤组成：

1.  GT layer从邻接矩阵集合$\mathbb{A}$中软选择两个邻接矩阵Q<sub>1</sub>,Q<sub>2</sub>
2.   通过两个关系矩阵Q<sub>1</sub>,Q<sub>2</sub>，学到新的图结构

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/10/083427-987240.png)

对于权重向量$\boldsymbol{W}_{\phi}^{1}$和$\boldsymbol{W}_{\phi}^{2}$（1\*1\*k）分别进行softmax操作，然后作为1\*1的卷积核对异构邻接矩阵集合中的全部K个邻接矩阵进行卷积操作。最后分别得到两个新的邻接矩阵**Q<sub>1</sub>,Q<sub>2</sub>**，将两个矩阵相乘得到二阶meta-path图$A^{(1)}$。每进行一次新矩阵间的相乘阶数都会升高一阶。

#### 整体模型

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/10/084807-669776.png)

-   相比于上面介绍的Graph Transformer (GT) Layer，整体模型使用multi-channel（使用了C个通道），这样最后得到的$\mathbb{A}^{(l)}$就是由C个邻接矩阵组成的图集和。对于每一个矩阵分别进行卷积，得到C个向量表示，将这C个向量表示拼接起来得到目标node的表示。

-   为了得到L-hop间的信息，一共设计了L个multi-channel矩阵，这样就可以考虑到L-hop范围内的信息。





### 反思

1.  可以尝试使用到知识图谱中，对知识图谱进行剪裁，前提是**知识图谱中的关系的种类是有限的**



### reference

