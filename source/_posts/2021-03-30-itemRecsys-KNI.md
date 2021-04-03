---
title: (KNI)An End-to-End Neighborhood-based Interaction Model for Knowledge-enhanced Recommendation 论文笔记
date: 2020-11-26 17:23:19
toc: true
categories:
  - paper reading
excerpt: 本文介绍了一篇将商品推荐与知识图谱相结合的论文KNI，其特点是进行用户和商品间子图的交互
---

## KNI: An End-to-End Neighborhood-based Interaction Model for Knowledge-enhanced Recommendation

### 创新点

- 针对之前的模型，我们提出了其存在的early summarization problem，针对这一问题，我们采用Neighborhood Interaction (NI) model，相比较早期**仅仅只用**item的向量和user的向量计算点积率。我们这里使用**item的neighborhood**和**user的neighborhood**进行计算，可以捕捉珍贵和复杂的结构信息

    >   “early summarization”：仅将user/item自身及周围的邻居信息全部汇聚到一个向量，这种方式的缺点是没有利用到item/user的local structures，即item neighborhoods与user neighborhoods之间的联系没有捕捉到。只捕捉到了目标item与user之间这一条边的关系

- 为了进一步丰富节点间的连接信息和高阶结构化信息，我们引入了知识图谱，用来增加节点间的连接。因此我们称这种方法为KNI

### 模型

#### NI模型

>   计算user对于item期望值的模型

$$
\begin{aligned}
\text { Attention: } \hat{y}_{u, v} &=\left\langle\sum_{i \in N_{u}} \alpha_{u, i} \mathbf{x}_{i}, \sum_{j \in N_{v}} \alpha_{v, j} \mathbf{x}_{j}\right\rangle \\
&=\sum_{i \in N_{u}} \sum_{j \in N_{v}} \alpha_{u, i} \alpha_{v, j}\left\langle\mathbf{x}_{i}, \mathbf{x}_{j}\right\rangle
\end{aligned}
$$

其中$N_{u}$为user的neighborhoods，$N_{\mathcal{V}}$为item的neighborhoods。简写如下：

$$
\hat{y}=\mathrm{~A} \odot \mathrm{Z}
$$

其中：

$$
\sum_{i, j} \mathrm{~A}_{i, j}=1, \mathrm{Z}_{i, j}=\left\langle\mathrm{x}_{i}, \mathrm{x}_{j}\right\rangle
$$

对于其中权重的计算，论文中提出如下**bi-attention network**：

$$
\alpha_{i, j}=\operatorname{softmax}_{i, j}\left(\mathbf{w}^{\top}\left[\mathbf{x}_{u}, \mathbf{x}_{i}, \mathbf{x}_{v}, \mathbf{x}_{j}\right]+b\right)
$$

#### node embedding模型

>   通过GCN，GAT来学到对于user/item的embedding，用学得的embedding来替代NI模型中的$\mathbf{X}_{i}, \mathbf{X}_{j}$

假设当前计算的是node u的表示，则i是所有与u在one-hop之内相连的节点，最外层的节点$\mathbf{X}_{j}$用初始向量进行计算，讲过一次卷积得到node i的表示

$$
\mathbf{x}_{i}^{1}=\sigma\left(\frac{1}{\left|N_{i}\right|} \sum_{j \in N_{i}} \mathbf{w}^{1} \mathbf{x}_{j}+\mathbf{b}^{1}\right)
$$
将node i的表示带入node u的卷积计算中，得到node u的表示

$$
\mathbf{x}_{u}^{2}=\sigma\left(\frac{1}{\left|N_{u}\right|} \sum_{i \in N_{u}} \mathbf{w}^{2} \mathbf{x}_{i}^{1}+\mathbf{b}^{2}\right)
$$

#### 引入知识图谱的NI模型

>   即KNI模型

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/13/211722-856032.png)

在user和item之间加入KG的entity来扩充图中的关系和实体

#### 整体计算流程

1. 通过GNN逐层传递周围2-hop内的信息到目标节点，计算两个节点的embedding

    ![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/13/212035-678806.png)

2. 通过计算到的embedding，计算目标节点的neighborhood之间的期望得分

    ![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/13/212531-251331.png)

### 实验结果

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/13/213545-632304.png)

实验证明NI模型的计算是有用的

### 反思

1. 将user-item之间的交互转化为neighbor-neighbor之间的交互，摆脱了双塔模型，相当于基于图的计算
