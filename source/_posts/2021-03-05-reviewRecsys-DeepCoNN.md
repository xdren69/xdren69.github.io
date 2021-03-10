---
title: (DeepCoNN)Joint Deep Modeling of Users and Items Using Reviews for Recommendation 论文阅读
date: 2021-03-05 15:30:13
toc: true
categories:
  - paper reading
tags:
  - review-based recommendation
excerpt: 发表于WSDM 2017的一篇经典的基于评论进行用户推荐的文章
---

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202103/05/205442-237787.png)



## 摘要

⽤户所写的评论中存在很多可以发掘的有⽤的信息，这些信息可以⽤来减轻数据的稀疏性问题并且提升 推荐系统的质量，然⽽⽬前很多推荐系统都忽略了这个评论中的信息。因此我们提出了DeepCoNN模 型，包含两个平⾏的神经⽹络，这两个⽹络在最后⼀层产⽣交互。其中⼀个⽹络通过⽤户写过的所有评 论来学习⽤户的向量表示；另⼀个⽹络通过学习所有针对同⼀商品的评论来学习商品的向量表示。最后 的共享层⽤来学习商品向量和⽤户向量的交互，其交互⽅式采⽤的分解机技术。实验结果证明 DeepCoNN在过个baseline中都表现的很好。



## 创新

1.  我们提出的DeepCoNN模型是第⼀个通过评论信息来共同建模⽤户⾏为和商品属性，额外的位于两 个神经⽹络之上的共享层链接了两个神经⽹络，通过结合两个平⾏神经⽹络的预测结果来预测⽤户 的评分。 
2.  此篇⽂章的模型使⽤预训练的word embedding来表示⽤户的评论。实验证明这种表示⽅式下的语 义信息和情感态度相⽐于传统的bag-of-word模型更能提升预测准确性。
3.  利⽤评论不仅可以减轻稀疏性问题，还能显著提⾼系统的整体表现。



## 模型

整体的模型结果如下图所示：

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202103/05/153132-977348.png)

其中左侧为⽤于学习⽤户向量表示的神经⽹络；右侧为⽤于学习item向量表示的神经⽹络。左右侧的模 型结构完全⼀样，只是输⼊的数据不⼀样，因此此处着重讲解左侧的模型。左侧模型⼀共由四个layer组 成，即：Look-up layer，Convolution layer，Max-pooling layer，Fully-connected layer。接下来将 分别介绍这⼏个layer的作⽤：

-   Look-up layer：⽤于将每个⽂字转化为对应的word embedding，进⽽将⼀段⽂本转化为⼀个矩 阵。此处不同于之前的bag-of-word，转化为向量矩阵之后依旧保持着原先word之间的顺序。即⾸ 先将⼀个⽤户过往的所有评论组成⼀个document，$d_{1: n}^{u}$，然后将⽂档转化为矩阵：
    $$
    V_{1: n}^{u}=\phi\left(d_{1}^{u}\right) \oplus \phi\left(d_{2}^{u}\right) \oplus \phi\left(d_{3}^{u}\right) \oplus \ldots \oplus \phi\left(d_{n}^{u}\right)
    $$
    其中$\phi\left(d_{k}^{u}\right)$为look-up函数

-   Convolution layer：通过多个卷积核来提取不同的局部语义信息，即
    $$
    z_{j}=f\left(V_{1: n}^{u} * K_{j}+b_{j}\right)
    $$
    其中$K_{j} \in \Re^{c \times t}$为卷积核，*为卷积操作， 为激活函数Rectified Linear Units (ReLUs)，即：
    $$
    f(x)=\max \{0, x\}
    $$
    每个卷积核会依序遍历⼀遍整个document，得到卷积结果：
    $$
    z_{1}, z_{2}, \ldots, z_{(n-t+1)}
    $$

-   Max-pooling layer：对于每⼀个卷积核的卷积结果，选择⼀个最⼤的作为当前卷积核在当前 document下的特征。因此pooling函数的表达式如下：
    $$
    o_{j}=\max \left\{z_{1}, z_{2}, \ldots, z_{(n-t+1)}\right\}
    $$
    此处的n是整个⽂章的单词⻓度，t是窗⼝的⼤⼩。对于所有卷积核做完如上操作得到的结果为：
    $$
    O=\left\{o_{1}, o_{2}, o_{3}, \ldots, o_{n_{1}}\right\}
    $$
    其中$n_{1}$表示卷积核的个数

-   Fully-connected layer：将Max-pooling layer的结果做⼀次全连接的映射，得到的结果即为：
    $$
    \mathbf{x}_{\mathbf{u}}=f(W \times O+g)
    $$

通过如上的模型可以得到：user和item的向量表示。然后将user和item的向量表示输⼊最后⼀层交互层 可以得到⽤于对于某个商品的评分，交互层使⽤了分解机（Factorization Machine) 机制，公式如下：
$$
J=\hat{w}_{0}+\sum_{i=1}^{|\hat{z}|} \hat{w}_{i} \hat{z}_{i}+\sum_{i=1}^{|\hat{z}|} \sum_{j=i+1}^{|\hat{z}|}\left\langle\hat{\mathbf{v}}_{i}, \hat{\mathbf{v}}_{j}\right\rangle \hat{z}_{i} \hat{z}_{j}
$$
其重要作⽤是对⼀阶特征和⼆阶特征进⾏融合，⽤来预测评分。其中$\left\langle\hat{\mathbf{v}}_{i}, \hat{\mathbf{v}}_{j}\right\rangle$⽤来确定⼆阶特征的权重。



## 训练

⽬标函数为：
$$
J=\hat{w}_{0}+\sum_{i=1}^{|\hat{z}|} \hat{w}_{i} \hat{z}_{i}+\sum_{i=1}^{|\hat{z}|} \sum_{j=i+1}^{|\hat{z}|}\left\langle\hat{\mathbf{v}}_{i}, \hat{\mathbf{v}}_{j}\right\rangle \hat{z}_{i} \hat{z}_{j}
$$
其⽬标就是对于$-\hat{z}_{i}$最⼩化$J$，从⽽通过链式法则实现反向传播，即：
$$
\frac{\partial J}{\partial \hat{z}_{i}}=\hat{w}_{i}+\sum_{j=i+1}^{|\hat{z}|}\left\langle\hat{\mathbf{v}}_{i}, \hat{\mathbf{v}}_{j}\right\rangle \hat{z}_{j}
$$
在这⾥我们使⽤RMSprop，它是梯度下降的⾃适应版本，会根据梯度的绝对值⾃适应的控制步⻓。具体 的做法如下⾯公式所示：
$$
r_{t} \leftarrow 0.9\left(\frac{\partial J}{\partial \theta}\right)^{2}+0.1 r_{t-1}
$$

$$
\theta \leftarrow \theta-\left(\frac{\lambda}{\sqrt{r_{t}}+\epsilon}\right) \frac{\partial J}{\partial \theta}
$$



## 实验

### 数据集

1.  Yelp：⼀个对于饭店评论的数据集，包含1 W条评论和打分 
2.  Amazon：包含来意与亚⻢逊⽹站的对于商品的评论和打分，包含21类商品和143.7 million条评 论，是⽬前最⼤的针对商品评论的数据库 
3.  Beer：这是⼀个对于啤酒的评论数据库

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202103/05/204721-638354.png)



## 实验设置

1.  进⾏卷积时，卷积核的个数$n_{1}$，会从取10-400进⾏实验，最终设置为100

    <img src="https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202103/05/205315-901372.png" style="zoom:50%;" />

2.  最终进⾏分解机前潜在因⼦的数⽬，即$\left|\mathbf{x}_{\mathbf{u}}\right|$和$\left|\mathbf{y}_{\mathbf{i}}\right|$的⼤⼩会取5-100进⾏实验，最终设置为50

    <img src="https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202103/05/205336-557190.png" style="zoom:50%;" />

3.  CNN的窗⼝⼤⼩设置为3

4.  batch size设置为100

5.  学习率设置为0.002 

6.  实验的评价标准：采⽤MSE作为评价标准，即：
    $$
    M S E=\frac{1}{N} \sum_{n=1}^{N}\left(r_{n}-\hat{r}_{n}\right)^{2}
    $$



## 实验结果

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202103/05/205006-514082.png)

通过⽐较可以发现，我们的模型在各个数据库⽅⾯均优于之前的模型，同样我们可以得出如下结论： 

1.  所有的模型在Beer数据库的表现都⽐其他两个数据库好，是因为其他两个数据库⽐较稀疏
2.  考虑了评论的模型⽐没有考虑评论的模型（PMF，MF）表现要好 
3.  同时考虑了评论和rating的模型（CTR，HFT）表现要⽐只考虑评论的模型（LDA）取得的效果好



## 模型分析

为了验证我们的提出的模型的创新点，即 

1.  相互协作的两个神经⽹络能否通过合作有效的从评论中学习特征?

     对此我们提出了如下的实验，DeepCoNN-User and DeepCoNN-Item，即分别替换了item和user 直接为⼀组vector进⾏训练，实验结果证明DeepCoNN的效果最好

2.  word embedding是否有效的挖掘了语义信息和情感信息？

    对此我们提出了如下的实验，DeepCoNN-TFIDF and DeepCoNN-Random，分别以TFIDF和 random作为模型的输⼊来训练模型，最终实验表明word embedding的效果最好 

3.  共享层对于提升预测的准确性有多⼤的帮助？ 

    对此我们做出如下的实验，设计了DeepCoNN-DP模型，即⽤⼀个更简单的⽬标函数来替换分解机 模型，实验证明分解机模型的效果更好