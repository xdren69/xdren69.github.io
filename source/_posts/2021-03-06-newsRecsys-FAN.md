---
title: >-
  (FAN)Fairness-aware News Recommendation with Decomposed Adversarial Learning
  论文阅读
tags:
  - news recommendation
toc: true
categories:
  - paper reading
excerpt: 发表于2021 WWW的有关新闻推荐的论文，是目前为止第一篇在新闻推荐领域里讨论fairness的文章
date: 2021-03-06 16:53:29
---


## Fairness介绍



## 新闻推荐中Fairness的问题

<img src="https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202103/03/160930-167891.png" style="zoom:50%;" />

用论文中的图片来解释：这里我们以性别为例子来解释新闻推荐中的fairness问题，通过用户的过往点击历史，模型可以学到用户A,B是女性，用户C,D是男性。如果对用户D进行新闻推荐时，因为用户是男性，模型会根据大多数男性的兴趣为用户进行推荐（即NBA），不会为该用户推荐左侧的新闻。而左侧新闻有关Fashion，正好是用户D的兴趣点。



## 创新点

本文提出了fairness-aware news recommendation approach with decomposed adversarial learning and orthogonality regularization这一方法，即该方法由**解离对抗学习**，**正交正则化**这两种子方法来实现对于fairness的去除。

-   解离对抗学习：以往的新闻推荐方法都是分别通过user model和news model来学习user embedding和news embedding，FAN为了解离bias，于是将user model分为两个部分，分别是bias-aware user model和bias-free user model。对这两个user model进行对抗式的训练，使得bias-aware user model能准确的预测sensitive attributes，而bias-free user model无法预测sensitive attributes。
-   正交正则化：是得bias-aware user model得到的user embedding和bias-free user model得到的user embedding尽量能够相互正交。进一步解离bias



## 模型

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202103/03/161853-804919.png)

user Model部分news Model部分与[NRMS模型]()采用同样的建模方式

其余主要划分为三个模块：

-   Attribute Prediction：

    输入bias-aware user model产生的bias-aware user embedding，通过如下公式对于sensitive attributes进行预测
    $$
    \hat{z}=\operatorname{softmax}\left(\mathbf{W}^{b} \mathbf{u}^{b}+\mathbf{b}^{b}\right)
    $$
    要求预测产生的loss越小越好
    $$
    \mathcal{L}_{G}=-\frac{1}{U} \sum_{j=1}^{U} \sum_{i=1}^{C} z_{i}^{j} \log \left(\hat{z}_{i}^{j}\right)
    $$

-   Adversarial Learning

    输入bias-free user model产生的bias-free user embedding，通过如下公式对于sensitive attributes进行预测
    $$
    \tilde{z}=\operatorname{softmax}\left(\mathrm{W}^{d} \mathrm{u}^{d}+\mathrm{b}^{d}\right)
    $$
    要求预测产生的loss越大越好，即预测越不准确越好
    $$
    \mathcal{L}_{A}=-\frac{1}{U} \sum_{j=1}^{U} \sum_{i=1}^{C} z_{i}^{j} \log \left(\tilde{z}_{i}^{j}\right)
    $$
    
-   Orthogonality regularization

    产生的loss如下：
    $$
    \mathcal{L}_{D}=-\frac{1}{U} \sum_{i=1}^{U} \mid \frac{\mathbf{u}_{i}^{b} \cdot \mathbf{u}_{i}^{d}}{\left\|\mathbf{u}_{i}^{b}\right\| \cdot\left\|\mathbf{u}_{i}^{d}\right\|}
    $$

-   训练时总的Loss
    $$
    \mathcal{L}=\mathcal{L}_{R}+\lambda_{G} \mathcal{L}_{G}+\lambda_{D} \mathcal{L}_{D}-\lambda_{A} \mathcal{L}_{A}
    $$
    其中$\mathcal{L}_{R}$表示推荐系统本身的loss，其计算公式如下：
    $$
    \mathcal{L}_{R}=-\frac{1}{N_{c}} \sum_{i=1}^{N_{c}} \log \left[\frac{\exp \left(\hat{y}_{i}\right)}{\exp \left(\hat{y}_{i}\right)+\sum_{j=1}^{T} \exp \left(\hat{y}_{i, j}\right)}\right.
    $$

## 模型训练

1.  对于每一个样本，通过负采样进行训练，负样本来自于同一对话中未被点击的新闻
2.  训练时使用的user embedding是将bias-free user embedding和bias-aware user embedding相加得到的
3.  Fairness的测量标准



## 实验结果

### Fairness对比

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202103/24/122156-637482.png)

### 推荐指标对比

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202103/24/122322-522008.png)



## 消融实验

<img src="https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202103/03/165459-599313.png" style="zoom:67%;" />

表明模型中每一部分都是有作用的