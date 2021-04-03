---
title: (NCEL)Neural Collective Entity Linking 论文笔记 
tags: 
---

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202103/30/204144-869802.png)

## 创新点

1.  为了节约算力，我们在子图区域内（滑动窗口内）执行图卷积神经网络，而不是整篇文章的实体图内

2.  不再只考虑局部信息之间（entity在wiki上的描述信息和mention的上下文信息）的匹配程度，加入了全局一致性的考量。即：同一篇文章的mention对应的entity之间具有话题一致性

    

## 模型

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202103/30/204222-341232.png)

整个模型一共包含三个部分，即：

-   candidate Generation：我们在这里用字典存储了mention和entity之间的映射关系。我们会选取$\hat{p}\left(e_{i} \mid m_{j}\right)$中的top n个存储进字典
-   feature extraction
-   neural network

### Feature Extraction

分为local features 和 global features两个部分

#### local features

局部特征反映了一个mention的context和它的所有的candidates之间的兼容性

其中$e_{j} \in \Phi\left(m_{i}\right)$（表示mention i 的candidate entities）的local features为两个部分：

1.  ***String Similarity***：we define string based features as follows: the edit distance between mention’s surface form and entity title, and boolean features indicating whether they are equivalent, whether the mention is inside, starts with or ends with entity title and vice versa. 
2.  ***Compatibility***：

#### global features

用于捕捉整篇文章的各个mentions之间的一致性



### Neural Network



## 参考论文

[1] **Bridging Text and Knowledge by Learning Multi-Prototype Entity Mention Embedding**

>   为了解决的问题：将text和entity的语义空间映射在同一个向量空间时，一个mention可能有多个同名的entity，一个entity可能对应多个不同名字的mention。
>
>   解决方法：每个mention的不同sense都对应一个entity，为此我们学习mention sense embedding。这样不同mention的具有相同的sense，此时二者的mention sense相同。

[2] **Named Entity Disambiguation for Noisy Text**：用于计算局部特征中的String Similarity

