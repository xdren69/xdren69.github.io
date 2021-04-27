---
title: learning to rank中的Listwise，Pairwise和Pointwise
date: 2021-04-26 19:29:42
toc: true
categories:
  - recsys
excerpt: 本文介绍了目前三种基于排序的loss生成方式，即Listwise，Pairwise，Pointwise
---



从一个较高的视角来看，Listwise，Pairwise，Pointwise三者之间的主要区别是 ***训练模型* **时，Loss function中考虑的文档的个数

## Pointwise 

Loss function中每次对一个查询只考虑一个文档，此时的任务预测文档的相关性或者相关性得分；

使用的模型一般是分类模型或者线性回归模型；

最终的排序结果是根据文档的相关性得分来实现的；

-   Loss function: MSE、二分类
    $$
    h_{\theta}\left(q_{i}, c_{i, j}\right) \rightarrow y_{i, j}, \text { 其中 } 0 \leq y_{i, j} \leq 1
    $$

-   预测阶段：将 $y_{i, j}$ 作为打分值进行排序



>   **缺陷**
>
>   1.  实现的是精确打分，而不是相对打分，无法实现排序
>   2.  损失函数也没有 model 到预测排序中的位置信息
>   3.  query间doc的不平衡，如query1对应500个文档，query2对应10个文档





## Pairwise

Loss function中每次对一个查询只考虑一对文档（包含一正一负），此时的任务是对这一对文档进行最优的排序，使其符合ground truth；训练的样例为 $\left(q_{i}, c_{i}^{+}, c_{i}^{-}\right)$ 其中$q_{i}$为提问，$c_{i}^{+}$为正确回答，$c_{i}^{-}$为错误回答

模型的目标是：使得模型预测到的逆序对的个数最少

Pairwise的效果比Pointwise的更好，是因为Pairwise更符合排序问题的本质

-   Loss function
    $$
    L=\max \left\{0, \quad m-h_{\theta}\left(q_{i}, c_{i}^{+}\right)+h_{\theta}\left(q_{i}, c_{i}^{-}\right)\right\}
    $$

-   实现方法: RankNet、LambdaRank、LambdaMART



>   **缺陷**：
>
>   1.  doc pair 的数量将是 doc 数量的二次，从而 pointwise 方法存在的 query 间 doc 数量的不平衡性将在 pairwise 类方法中进一步放大
>   2.  pairwise 方法相对 pointwise 方法对噪声标注更敏感，即一个错误标注会引起多个 doc pair 标注错误
>   3.  pairwise 方法仅考虑了 doc pair 的相对位置，损失函数还是没有 model 到预测排序中的位置信息



## Listwise

Loss function每次考虑一个文档列表，模型的目标是得到一个最优的排序序列，训练样本为：查询 $q_{i}$，以及一系列候选句子 $C\left(c_{i 1}, c_{i 2}, \ldots, c_{i m}\right)$ 以及相应的标签 $Y\left(y_{i 1}, y_{i 2}, \ldots, y_{i m}\right)$ 

-   训练：最小化 **S** 和 **Y** 的KL散度，其具体定义分别如下：
    $$
    \begin{array}{l}
    \text { Score }_{j}=h_{\theta}\left(q_{i}, c_{i j}\right) \\
    S=\operatorname{softmax}\left(\left[\text { Score }_{1}, \text { Score }_{2}, \ldots, \text { Score }_{m}\right]\right) \\
    Y=\frac{Y}{\sum_{m}^{j=1} y_{i j}}
    \end{array}
    $$

主要包含两部分的技术

1.  直接优化IR的指标，比如 NDCG: SoftRank, AdaRank
2.  将Loss function定义在某一特征上进行优化: ListNet, ListMLE

>   Listwise方法相比于pariwise和pointwise往往更加直接，它专注于自己的目标和任务，直接对文档排序结果进行优化，因此往往效果也是最好的



## Reference

[1] [Pointwise vs. Pairwise vs. Listwise Learning to Rank](https://medium.com/@nikhilbd/pointwise-vs-pairwise-vs-listwise-learning-to-rank-80a8fe8fadfd)

[2] [Learning to Rank： pointwise 、 pairwise 、 listwise](https://zhuanlan.zhihu.com/p/111636490)