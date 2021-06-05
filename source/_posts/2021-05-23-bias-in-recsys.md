---
title: 推荐系统中的bias（综述）
date: 2021-05-23 11:09:08
tags:
---



## bias产生的原因



## 对于bias的分类

>   这篇文章中一共总结了7种bias，我们将推荐系统的整个循环抽象为如下的Data，Model，User之间的交互；同时对这7种bias进行定位，根据bias产生的位置，我们对bias进行分类，如下所示：

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/20210604163705.png)



### 产生于User->Data部分

此处的偏差产生于用户与商品的交互数据中

>   此处一共产生了4种bias，我们根据相应的feedback分为 explicit feedback 和 implicit feedback 两类分别来介绍，其中explicit feedback包含**selection bias**，**conformity bias**，而implicit feedback包含**exposure bias**，**position bias**

首先介绍一下explicit feedback，即该feedback能够明确反馈用户对于某一item的喜好，一般是打分或者评论；而implicit feedback只能反映用户与item的交互情况，比如click，purchase，browse等，不能反馈用户是否喜爱某一商品

#### selection bias

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/20210524101611.png)

>   通过之前的研究，即研究人员随机抽取一些商品发给用户进行评价，将评价结果与用户平时做出的评价进行对比，结果如上图所示，得出的结论是：
>
>   用户倾向于评价两种物品：1. 用户喜欢的物品；2.极好或者极差的物品；因此用户对于物品的评价不具有随机性。因此我们可以看出两个分布是不同的，即rating的缺失不是随机的，the rating data is often missing not at random (MNAR)

 

#### conformity bias

>   用户的评价倾向于与大众相同，之前的研究显示，用户在浏览过其它用户的评价前后所做出的评价是不同的



#### exposure bias

>   此类bias主要是针对负样本的产生的，即商品只暴露在部分商品之下，可以将暴露未交互视为负样本；但是不能将未暴露视为负样本

这种曝光误差主要来源于以下几个方面：

1.  previous mode bias：哪些数据能够被曝光取决于之前推荐系统的推荐策略
2.  selection bias：曝光给用户的商品是根据用户的兴趣和选择而产生的
3.  由于生成推荐列表时考虑了用户的背景信息，所以可能由于用户的背景信息所导致（地域，社交圈）
4.  popularity bias：流行的商品更容易被推荐给更多的人



#### position bias

>   在整个推荐给用户的list中，用户一般只会与位置靠前的item产生隐式交互，而不考虑每个item的相关性（即对于推荐列表中商品的喜欢程度）



### 产生于Model部分

此类偏差产生于模型的训练中

#### Inductive Bias

>   此类偏差的产生是：训练人员为了模型能更好地学习目标函数和泛化训练数据而做出一些假设。这些假设通过引入认为设计的bias来实现



### 产生于Model->User部分

此类偏差产生于推荐系统给用户推荐商品的过程中，即产生于推荐结果中

#### Popularity Bias

>   流行商品被推荐的频率大于原本推荐该商品的频率。

这种bias导致的结果是：

1.  降低对于用户的个性化推荐程度，损害喜欢小众商品的用户的购物体验；
2.  导致长尾问题；
3.  降低了推荐结果的公平性；



#### Unfairness

>   fairness的定义：absence of any prejudice or favoritism towards an individual or a group based on their intrinsic or acquired traits. 
>
>   其中bias与fairness的区别在于：bias存在于系统的任何一个部分；fairness则是相对于user或者item而言的



## 当前对于不同Bias的解决

### selection bias

>   在评价指标方面进行改进

在传统的unbiased的情况下，我们对于模型预测用户rating的能力的评价指标为：
$$
R(\hat{Y})=\frac{1}{U \cdot I} \sum_{u=1}^{U} \sum_{i=1}^{I} \delta_{u, i}(Y, \hat{Y})
$$
其中$\delta_{u, i}(Y, \hat{Y})$具体为下列计算方法之一：
$$
\begin{aligned}
\text { MAE: } & \delta_{u, i}(Y, \hat{Y})=\left|Y_{u, i}-\hat{Y}_{u, i}\right| \\
\text { MSE: } & \delta_{u, i}(Y, \hat{Y})=\left(Y_{u, i}-\hat{Y}_{u, i}\right)^{2} \\
\text { Accuracy: } & \delta_{u, i}(Y, \hat{Y})=1\left\{\hat{Y}_{u, i}=Y_{u, i}\right\} .
\end{aligned}
$$
但是由于我们训练模型时使用到的samples是observed，在selection bias存在的情况下，这些samples是无法完全代表整体用户rating行为的分布的，即empirical risk和true risk之间是存在差异的。即：
$$
\mathbb{E}_{O}\left[\hat{R}_{\text {naive }}(\hat{Y})\right] \quad \neq R(\hat{Y})
$$
因此我们可以改进对于系统的评价指标，用来消除empirical risk和true risk之间的差异；

目前具体存在如下的两种做法：

#### propensity score

出自论文[Recommendations as Treatments: Debiasing Learning and Evaluation]()

这里我们使用 $P_{u, i}=P\left(O_{u, i}=1\right)$ 作为user对于item的propensity score，对于它的定义为：user对于item的rating行为被观测到的概率，论文中对于propensity score有专门的计算模型，这里不再叙述；最终将评价指标优化为：
$$
\hat{H}_{I P S}(\hat{r} \mid P)=\frac{1}{n m} \sum_{(u, i): O_{u, i}=1} \frac{\delta_{u, i}(r, \hat{r})}{P_{u, i}}
$$


#### ATOP

出自论文[Training and testing of recommender systems on data missing not at random]()

