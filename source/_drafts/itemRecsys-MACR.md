---
title: (MACR)Model-Agnostic Counterfactual Reasoning for Eliminating Popularity Bias in Recommender System 论文阅读
tags:
  - item recommendation
  - bias
toc: true
categories:
  - paper reading
excerpt: 何向南组提出的利用反事实推断来解决商品推荐问题中的流行性偏差问题
---

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202103/19/213431-921586.png)



1.  流行性**bias**：存在于**协同过滤**方法中，现在的推荐系统倾向于为用户推荐多数用户点击的商品，即流行的商品。这就会导致马太效应（流行的商品越来越流行，不流行的商品越来越不流行）。模型在通过训练数据进行训练时，也会学得：推荐更加流行的商品，会得到更低的loss。这会导致用户得到的推荐是流行的商品，而不是个性化需求的商品！
2.  当前的解决方法：采用Inverse Propensity Weighting(IPW) 方法，即减少流行item的权重，增加长尾item的权重。这种方法的缺点是：
    -   对于权重参数非常敏感，参数很难调整
    -   有些流行的商品确实很匹配用户的兴趣，但是却被盲目地降低了权重



## 创新点

1.  从一个全新的角度来测量流行性bias，即将流行性作为商品的一种属性，考虑这种属性直接对于ranking score的影响（在后期推理阶段将其删去）。同时回答反事实问题：如果模型只考虑商品属性，ranking score会得到怎样的结果。

2.  构建cause-effect graph（因果图）来建模有哪些因素会影响最终用户点击的概率，在这里我们假设了三种cause: user-item matching, item popularity, and user activity。其中user activity建模的是：用户对于流行性的喜好程度。在**训练**过程中，我们通过多任务学习来探究每一个cause的贡献；在**测试**过程中，我们通过执行反事实推断来消除商品流行性的影响。

    <img src="https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202103/23/092804-142795.png" style="zoom:67%;" />

    >   因果推理阶段：
    >
    >   反事实推理：想象一个平行世界（反事实世界），用户购买商品的因素仅仅是因为商品的流行性和用户对于流行性的接纳程度。从总的排序得分中减去反事实世界中的得分。

    <img src="https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202103/23/095511-258196.png" style="zoom:67%;" />



## 先导概念







## 模型



### 预测阶段

由于测试数据集和训练数据集都存在流行性偏差，因此我们采用新的方式来评估我们的模型



