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



1.  流行性**bias**：现在的推荐系统倾向于为用户推荐多数用户点击的商品，即流行的商品。这就会导致马太效应（流行的商品越来越流行，不流行的商品越来越不流行）
2.  当前的解决方法：采用Inverse Propensity Weighting(IPW) 方法，即减少流行item的权重，增加长尾item的权重。这种方法的缺点是对于权重参数非常敏感，参数很难调整。



## 创新点

1.  从一个全新的角度来测量流行性bias