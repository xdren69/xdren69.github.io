---
title: PinnerSage（未完待续）
date: 2021-04-24 09:38:00
toc: true
categories:
  - paper reading
excerpt: 本文介绍了Pinterest解决召回问题的一系列技术变更
---

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202104/24/094135-426175.png)



推荐系统的架构：召回层（粗排）、排序层（精排）、重排序



## 召回层

>    以下均是从 [负样本为王: 评Facebook的向量化召回算法](https://mp.weixin.qq.com/s?__biz=MzA4NTUxNTE4Ng==&mid=2247491310&idx=1&sn=39e4d96cfaa2d006148d06f6214fc6d4&chksm=9fd79033a8a01925107bc959afb0cd14d97a7d5432b5fc666059bbac8e4559405bfa6f169d53&scene=21)摘录出来的笔记，
>
>   相关论文：
>
>   [1] [Embedding-based Retrieval in Facebook Search]()
>
>   [2] [MOBIUS: Towards the Next Generation of Query-Ad Matching in Baidu’s Sponsored Search](http://research.baidu.com/Public/uploads/5d12eca098d40.pdf)



>   如果说排序是特征的艺术，那么召回就是样本的艺术，特别是***负样本***的艺术

### 如何选择负样本

1.  排序层的目标是目标是“从用户可能喜欢的当中挑选出用户最喜欢的”，是为了优中选优，因此排序是非常讲究所谓的“真负”样本的，即必须拿“曝光未点击”的样本做负样本

2.  召回层的目标是“是将用户可能喜欢的，和海量对用户根本不靠谱的，分隔开”，而**最不靠谱的，往往不是“曝光未点击”的样本**，因为这里牵扯到一个推荐系统里常见的bias，就是我们从线上日志获得的训练样本，已经是上一版本的召回、粗排、精排替用户筛选过的，即已经是对用户“**比较靠谱**”的样本了。拿这样的样本训练出来的模型做召回，**一叶障目，只见树木，不见森林**。

    >   因此目前召回层的做法是：**拿点击样本做正样本，拿随机采样做负样本**。因为线上召回时，候选库里大多数的物料是与用户八杆子打不着的，随机抽样能够很好地模拟这一分布。

3.   随机采样不是指**在整个候选库里等概率抽样**，由于推荐系统中存在二八定律，因此：

    -   当热门物料做正样本时，要降采样，减少对正样本集的绑架。比如，某物料成为正样本的概率如下，其中z(wi)是第i个物料的曝光或点击占比
        $$
        P_{\text {pos }}=\left(\sqrt{\frac{z\left(w_{i}\right)}{0.001}}+1\right) \frac{0.001}{z\left(w_{i}\right)}
        $$

    -   当热门物料做负样本时，要适当过采样，抵消热门物料对正样本集的绑架；同时，也要保证冷门物料在负样本集中有出现的机会。比如，某物料成为负样本的概率如下，其中n(w)是第i个物料的出现次数，而一般取0.75
        $$
        P_{\text {neg }}=\frac{n\left(w_{i}\right)^{\alpha}}{\sum_{j} n\left(w_{j}\right)^{\alpha}}
        $$



### 如何使用hard Negative

#### 增强样本

我们可以将召回模型的召回样例分为三部分，即：

-   匹配度最高的，是以用户点击为代表的，那是正样本
-   匹配度最低的，那是随机抽取的。能被一眼看穿，没难度，所谓的easy negative，达不到锻炼模型的目的
-   所以要选取一部分匹配度适中的作为负样本，能够增加模型在训练时的难度，让模型能够关注细节，这就是所谓的**hard negative**

如何选取**hard negative**：

-   根据业务逻辑来选取
-   **用上一版本的召回模型筛选出“ 没那么相似 ”的对，作为额外负样本，训练下一版本召回模型**



#### 模型融合

用不同难度的negative训练不同难度的模型，再做多模型的融合

-   串行：不同难度的模型独立打分，最终取Top K的分数依据是多模型打分的加权和（各模型的权重是超参，需要手工调整）
-   并行：**其实就是粗排，**候选物料先经过easy model的初筛，再经过hard model的二次筛选，剩余结果再交给下游，更复杂粗排或是精排。



### 全链路优化

-   全链路优化的目标：Ranker是在已有召回算法筛选过的数据的基础上训练出来的，日积月累，也强化了某种刻板印象，即<user,doc>之间的匹配模式，只有符合某个老召回描述的匹配模式，才能得到Ranker的青睐，才有机会曝光给用户，才能排在容易被用户点击的好位置。
-   新召回所引入的<user,doc>之间的匹配模式，突破了ranker的传统认知，曝光给用户的机会就不多。即使曝光了，也会被ranker排在不讨喜的位置，不太容易被用户点击





## GraphSAGE

>   [1] [An Intuitive Explanation of GraphSAGE](https://towardsdatascience.com/an-intuitive-explanation-of-graphsage-6df9437ee64f)
>
>   [2] [【Graph Neural Network】GraphSAGE: 算法原理，实现和应用](https://zhuanlan.zhihu.com/p/79637787)





## PinSAGE





## PinnerSAGE







>   Faiss是Facebook AI团队开源的针对聚类和相似性搜索库，为稠密向量提供高效相似度搜索和聚类，支持十亿级别向量的搜索，是目前最为成熟的近似近邻搜索库。