---
title: (TEKGR)News Recommendation with Topic-Enriched Knowledge Graphs 论文阅读
date: 2021-03-01 20:52:47
toc: true
categories:
  - paper reading
tags:
  - news recommendation
excerpt: 发表于2020 CIKM的有关新闻推荐的论文
---

### Intro

之前基于知识图谱的推荐模型的存在者不足，即：仅仅根据新闻标题中被识别的实体来表示新闻，但往往存在错误。如下表所示，通过训练集可以看出用户感兴趣的是**Marijuana Stock**，而DKN模型通过实体“Cannabis” or “Marijuana”因此会将用户的兴趣点识别为**Politics** and **Crime**，而用户关心的往往是“Prices” or “Stocks”无法被识别为特殊实体。

>   为了解决这个问题，我们给每条新闻加入类别信息，不同于传统的人工加类别信息，我们通过神经网络和知识图谱来学习类别信息

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202012/02/161006-200573.png)



### 模型结构

整体模型分为三层，即：

1.  KG-Based News Modeling Layer（核心）
2.  Attention Layer
3.  Scoring Layer

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202012/02/165742-225544.png)

#### **KG-Based News Modeling Layer**（news embedding）

如下所示：

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202012/02/165942-367237.png)

主要由三个encoder组成，即：

1.  Word-Level News Encoder

    一共有三层组成，即

    -   embedding层
    -   Bi-GRU层，学习共同出现的单词间的联系，而不孤立的提取单个单词的表示
    -   attention层，每一个单词的权重不同

    最终得到word-level的**news representation vector**

2.  Knowledge Encoder 

    为了提取新闻的类别信息，引入了concept的概念，即知识图谱中"is A"的relationship，举例如下：对于新闻标题“Donald Trump vs. Madonna, Everything We Know”，通过知识图谱中的"is A"关系，我们可以得到：Donald Trump is a **politician** and a **CEO** while Madonna is a **singer** and a **feminist**.  通过这些关系，有助于我们判定该新闻为政治类新闻。在这一层中我们先提取出每一个实体的多个"is A"关系对应的实体，称之为"concept"，然后对这些concept进行embedding，最后利用attention机制学习相应的权重。在这个例子中就会为“**politician**”和“**feminist**”设置较高的权重

    最终得到**concept vector**

3.  KG-Level News Encoder

    分为三个步骤：

    -   根据标题提取出实体，将相应的实体映射到知识图谱中
    -   将实体向外延伸2-hop，并从原始的知识图谱中根据这些实体得到相应的子图
    -   为tittle中的各个实体添加topical relation，得到一张新的子图，在图中使用GNN算法得到news的表示

    最终得到KG-level的**news representation vector**

最终得到的news embedding为，word-level和KG-level的**news representation vector**相结合的结果

#### Attention Layer（user embedding）

通过attention学习目标news与user的历史点击的news的相关程度，最终得到user的embedding

#### Scoring Layer

将user embedding和news embedding进行点击，得到点击率

### 实验结果

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202012/02/222126-924376.png)