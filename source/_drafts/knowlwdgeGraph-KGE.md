---
title: 知识图谱嵌入
tags:
---

之前的技术主要是使用仅仅**基于观测到的事实**进行嵌入，但在这个过程中，仅要求学习的嵌入在每个单独的事实中是兼容的，因此学习到的嵌入向量对于下游任务可能没有足够的预测性。因此，越来越多的研究开始利用其他类型的信息，例如**实体类型**，**关系路径**，**文本描述**，甚至**逻辑规则**等，以学习到更有预测性的嵌入。



## 基于观测到的事实进行嵌入

每个fact (h,r,t) 对应一个得分![[公式]](https://www.zhihu.com/equation?tex=f_r(h%2C+t)) ，在KG中观察到的facts得分高于未观察到的，根据得分函数定义方式的不同，可被分为两类：**translational distance models** （翻译距离模型）和 **semantic matching models**（语义匹配模型），前者使用基于距离的评分函数，而后者使用基于相似性的评分函数。



### translational distance models

#### TransE

>   简单高效，**在同一空间中将实体和关系表示为向量**。例如 JamesCameron + DirectorOf ≈ Avatar

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/20210430152814.png)

1.  得分函数定义为h+r与t的（负）距离： $f_{r}(h, t)=-\|h+r-t\|_{1 / 2}$ . 当 (h,r,t) holds, 该得分高
2.  **TransE模型在处理一对多，多对一，多对多关系时有问题**，e.g.一对多为例，Alfred Hitchcock + Director Of ≈Psycho，Rebecca，Rear Window，一个导演对应多部电影，虽然这些电影属于不同实体，但是学到的表示向量都是非常相似的，这显然不合理。



#### TransH

>   为每一种relation引入一种**relation-specific超平面**，每个关系r用矢量r表示，在一个以 ![[公式]](https://www.zhihu.com/equation?tex=w_r) 为**法向量**的超平面上，实体h,t投射到该超平面上

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/20210430152913.png)

1.  给定一个事实(h,r,t), 首先将实体表示 h，t投影到超平面上:  
    $$
    h_{\perp}=h-w_{r}^{T} h w_{r}, t_{\perp}=t-w_{r}^{T} t w_{r}
    $$

2.  如果（h,r,t）成立，则有在超平面上该关系成立： 
    $$
    h_{\perp}+r \approx t_{\perp}
    $$

3.  得分函数定义为：
    $$
    f_{r}(h, t)=-\left\|h_{\perp}+r-t_{\perp}\right\|_{2}^{2}
    $$

4.  TransH 通过超平面转换，使实体在不同关系中发挥不同的作用。



#### TransR

>   与TransH的思想类似，不过它引入的是**relation-specific 空间**，而不是超平面

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/20210430153144.png)

1.  给定一个事实(h,r,t), 首先将实体表示 h，t投影到特定空间：
    $$
    h_{\perp}=M_{r} h, t_{\perp}=M_{r} t
    $$

2.  得分函数依然定义为：
    $$
    f_{r}(h, t)=-\left\|h_{\perp}+r-t_{\perp}\right\|_{2}^{2}
    $$

3.  TransR可以表述更加复杂的模型，但由于要为每个关系都构建一个投影矩阵 $M_{r}$ ，其参数为（ ![[公式]](https://www.zhihu.com/equation?tex=d\times+k) ），因此运算复杂度高于TransE和TransH。



### Semantic matching models

**语义匹配模型**构造基于相似性的得分函数，通过匹配嵌入实体和关系间的潜在语义来衡量事实的合理性。

#### RESCAL模型，也叫双线性模型



#### TATEC 模型





### 模型训练



#### 封闭世界

>   如果目前未表示，则表示为否定；否则表示为肯定

#### 开放世界

>   对于未表示的命题，则显示推理不出来；导致能够推理出来的信息量大大减少

通过特定方式生成负例，不是随机采样，采用 **logistic loss**
$$
\min _{\Theta} \sum_{\tau \in \mathrm{D}+\cup \mathrm{D}^{-}} \log \left(1+\exp \left(-y_{h r t} \cdot f_{r}(h, t)\right)\right)
$$
或者使用pairWise Loss  
$$
\min _{\Theta} \sum_{\tau+\in \mathbb{D}+\tau^{-} \in \mathbb{D}^{-}} \max \left(0, \gamma-f_{r}(h, t)+f_{r^{\prime}}\left(h^{\prime}, t^{\prime}\right)\right)
$$

#### 局部封闭世界

