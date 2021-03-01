---
title: 知识图谱与推荐系统
date: 2020-12-07 22:03:20
toc: true
tags:
  - item recommendation
categories:
  - recsys
excerpt: 这是一篇对于推荐系统领域与知识图谱相结合的工作的调研
---

## 论文阅读

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/07/103427-972429.png)

本文所包含的论文汇总：

| 题目        | 时间   | 会议      | 包含时间序列 | 包含知识图谱 | 数据集                                 |
|:---------:|:----:|:-------:|:------:|:------:| ----------------------------------- |
| CKAN      | 2020 | SIGIR   |        | 是      |                                     |
| KARN      | 2020 | AAAI    | 是      | 是      | Amazon review dataset               |
| KNI       | 2020 | DLP-KDD |        | 是      | MovieLens-20M，Book-Crossing，Last.FM |
| KGAT      | 2019 | KDD     |        | 是      |                                     |
| KGCN      | 2019 | WWW     |        | 是      |                                     |
| RippleNet | 2018 | CIKM    |        | 是      |                                     |

对于使用知识图谱的推荐系统，可以被分为两类，即：

- 利用知识图谱进行knowledge graph embedding (KGE)，从而得到包含更多语义信息的item的embedding
- 利用知识图谱中的路径进行path-based methods，提取meta-path/meta-graph

---

## CKAN: Collaborative Knowledge-aware Attentive Network for Recommender Systems

### Intro

文章认为现有的基于KG的方法存在着一些问题，即：

只利用了知识图谱中entity之间的连接信息来丰富item的语义信息，没有利用collaborative signals

对此本文提出新的模型**Collaborative Knowledge-aware Attentive Network (CKAN)**，用于将**collaborative signals**和**knowledge associations**结合起来

>   collaborative signals: which assumes that users with similar behaviors may have similar appetites for items,

### 创新点

1. Heterogeneous propagation: composed of collaboration propagation and knowledge graph propagation, views **interaction** and **knowledge** as information in two different spaces and combines them in a natural way.
2. Knowledge-aware attentive embedding: a brand new knowledge-aware neural attention mechanism proposed for learning different weights of neighbors in KG when they are in different conditions.

### 模型

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202012/07/195138-327292.png)

#### Heterogeneous Propagation Layer

用于融合知识图谱和协同信号

- Collaboration Propagation
  
  - 对于item的neighbor的确定：
    
    $$
    \mathcal{V}_{v}=\left\{v_{u} \mid u \in\left\{u \mid y_{u v}=1\right\} \text { and } y_{u v_{u}}=1\right\}
    $$
    
    $$
    \mathcal{E}_{v}^{0}=\left\{\mathrm{e} \mid\left(v_{u}, \mathrm{e}\right) \in \mathcal{A} \text { and } v_{u} \in \mathcal{V}_{v}\right\}
    $$
  
  - 对于user的neighbor的确定：
    
    
    $$
    \mathcal{E}_{u}^{0}=\left\{\mathrm{e} \mid(v, \mathrm{e}) \in \mathcal{A} \text { and } v \in\left\{v \mid y_{u v}=1\right\}\right\}
    $$
    

- Knowledge Graph Propagation
  
  对于上一阶段得到的seed set，在知识图谱中逐层传播，每一层得到的entity作为一个集合保存
  
      $$
      \mathcal{S}_{o}^{l}=\left\{(h, r, t) \mid(h, r, t) \in \mathcal{G} \text { and } h \in \mathcal{E}_{o}^{l-1}\right\}, \quad l=1,2, \ldots, L
      $$

#### Knowledge-aware Attentive Embedding Layer

对于每一个扩展出来的entity，使用attention进行计算，attention机制使用两层全连接网络实现：

$$
\begin{aligned}
\mathrm{z}_{0} &=\operatorname{ReLU}\left(\mathrm{W}_{0}\left(\mathrm{e}_{i}^{h} \| \mathrm{r}_{i}\right)+\mathrm{b}_{0}\right) \\
\pi\left(\mathrm{e}_{i}^{h}, \mathrm{r}_{i}\right) &=\sigma\left(\mathrm{W}_{2} \operatorname{ReLU}\left(\mathrm{W}_{1} \mathrm{z}_{0}+\mathrm{b}_{1}\right)+\mathrm{b}_{2}\right)
\end{aligned}
$$

最后经过softmax得到如下：

$$
\pi\left(\mathrm{e}_{i}^{h}, \mathrm{r}_{i}\right)=\frac{\exp \left(\pi\left(\mathrm{e}_{i}^{h}, \mathrm{r}_{i}\right)\right)}{\sum_{\left(h^{\prime}, r^{\prime}, t^{\prime}\right) \in \mathcal{S}_{o}^{l}} \exp \left(\pi\left(\mathrm{e}_{i}^{h^{\prime}}, \mathrm{r}_{i}^{\prime}\right)\right)}
$$

其中第0层的计算略有不同，因为第0层的贡献都很大，因此采用如下的方法：

$$
\mathrm{e}_{o}^{(0)}=\frac{\sum_{\mathrm{e} \in \mathcal{E}_{o}^{0}} \mathrm{e}}{\left|\mathcal{E}_{o}^{0}\right|}
$$

- 对于item而言：
  
  $$
  \mathrm{e}_{v}^{(\text {origin})}=\frac{\sum_{\mathrm{e} \in\{\mathrm{e} \mid(\mathrm{e}, v) \in \mathcal{A}\}} \mathrm{e}}{|\{\mathrm{e} \mid(\mathrm{e}, v) \in \mathcal{A}\}|}
  $$
  $$
  \mathcal{T}_{v}=\left\{\mathrm{e}_{v}^{(\text {origin})}, \mathrm{e}_{v}^{(0)}, \mathrm{e}_{v}^{(1)}, \ldots, \mathrm{e}_{v}^{(L)}\right\}
  $$

- 对于user而言：
  
  $$
  \mathcal{T}_{u}=\left\{\mathrm{e}_{u}^{(0)}, \mathrm{e}_{u}^{(1)}, \ldots, \mathrm{e}_{u}^{(L)}\right\}
  $$

#### Model Prediction Layer

- Sum aggregator：将上一个模块的每一层向量直接相加
  
  $$
  \operatorname{agg}_{s u m}^{(o)}=\sigma\left(\mathrm{W}_{a} \cdot \sum_{e_{o} \in \mathcal{T}_{o}} \mathrm{e}_{o}+\mathrm{b}_{a}\right)
  $$

- Pooling aggregator：去上一模块中所有层的向量中每一维度选取最大的
  
  $$
  a g g_{\text {pool}}^{(o)}=\sigma\left(\mathrm{W}_{a} \cdot \text {pool}_{\max }\left(\mathcal{T}_{o}\right)+\mathrm{b}_{a}\right)
  $$

- Concat aggregator：将每一层向量进行拼接
  
  $$
  a g g_{\text {concat}}^{(o)}=\sigma\left(\mathrm{W}_{a} \cdot\left(\mathrm{e}_{o}^{\left(i_{1}\right)}\left\|\mathrm{e}_{o}^{\left(i_{2}\right)}\right\| \ldots \| \mathrm{e}_{o}^{\left(i_{n}\right)}\right)+\mathrm{b}_{a}\right)
  $$

点击率的预测函数：

$$
\hat{y}_{u v}=\mathrm{e}_{u}^{\top} \mathrm{e}_{v}
$$

### 实验结果

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202012/08/101653-986811.png)

## KARN: A Knowledge-Aware Attentional Reasoning Network for Recommendation

### Intro

之前的工作要么只考虑使用知识图谱来解释user与item在知识图谱中之间path的合理性，要么只考虑通过用户的历史点击序列来预测用户的兴趣。本文将结合两者进行预测，这种预测方式将提高预测的准确率，具体如下：

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/07/094120-752547.png)

用户Jark的历史记录为3->2->1，如果不考虑历史记录，只根据**1和知识图谱**进行推荐，则由于Titantic的导演是James Cameron，因此可能推荐他执导的另外两部电影Piranha2和Aliens，但是通过**历史记录**可以得到Jark喜欢的电影类型更偏向于Adventure类型的电影，因此更有可能推荐Aliens而不是Piranha2

### 创新点

1. 提出KARN将用户的历史点击序列和知识图谱中的路径信息相结合
2. 对于item进行向量表示时，不仅考虑了textual（tittle）的信息，还考虑了contextual（its one-hop neighbors in KGs）的信息
3. 对于item进行向量表示时，不仅考虑用户的历史点击序列，还通过user和item之间的路径考虑了**user对于该item的潜在意图**

### 模型

#### **对于item的表示**

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/07/103758-690420.png)

其中

- 对于tittle中的每一个词的embedding组成的矩阵用F个卷积核进行卷积操作，用于提取局部语义信息（多个词组成的phrase的含义），最后经过一层变换得到与KG中实体维度相同向量V‘
- 对于该商品在KG中对应的entity的embedding用V来表示
- 对于该商品1-hop之内的neighbors entity采用**平均的方法**来得到向量V’‘

则item的表示最终为[V, V' , V'']

#### **对于user history的表示**

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/07/105331-105180.png)

对于历史点击的item，通过SRA网络来学习，SRA将RNN+attention得到的结果与RNN最后一个隐状态进行拼接来表示user history interest

#### **User potential intent**

即用户对于目标商品的潜在意图，通过所有在知识图谱中由用户到商品的路径来计算得出

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/07/105941-300239.png)

对于每一条路径，e表示实体，r表示关系：

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/07/110015-781535.png)

我们将（e<sub>i</sub>,r<sub>i</sub>）视为一个整体用一个向量来表示，在路径最后补上空关系r<sub>q</sub>，随后将将整条路径用多个这样的向量来表示，最后通过SRA学得整个路径的表示。将所有路径通过attention net得到User对于item的potential intent

#### **整体网络**

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/07/110840-705446.png)

### **实验结果**

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/07/111124-888823.png)

### **反思**

1. 实验存在缺陷，没有与其他使用知识图谱的模型进行对比，即KGCN，KGAT，RippleNet
2. 无法应用于新闻推荐，新闻的待推荐item的数量太多，无法每一个都参与user potential intent的计算

## KGCN: Knowledge Graph Convolutional Networks for Recommender Systems

### Intro

每个item都有很多属性，这些属性之间能够相互联系，从而形成一张知识图谱。利用这张知识图谱，我们可以发现item之间的高阶语义信息和结构信息。从而做出更加准确的推荐。对于知识图谱的处理我们提出了KGCN的方法，即将GCN运用到知识图谱中，用以形成对于每个entity（item）的表示。

### 模型

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/13/100654-5848.png)

如上图所示，KGCN在本文中的应用是：规定感受野的跳数H（图中为2），以及每个实体单跳感受野中要考虑的节点个数（图中为2）。则KGCN的计算顺序是：**不学习最外层的节点，先学习红色的节点，后学习蓝色的节点**。

整体算法如下所示：

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/13/101319-125780.png)

>   注意：当计算一个实体的上下文感受野信息时，对于单跳感受野中每个实体的权重设置不同

算法图示如下：

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/13/101544-573368.png)

### 实验结果

与其他baseline的对比结果如下：

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/14/085000-230172.png)

- PER需要人工设计meta-path，因此准确性会低很多

同时通过实验探究了K，H，dimension的大小对于实验准确性的影响

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/13/102721-583322.png)

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/13/102741-163332.png)

### 反思

1. 对于感受野中固定实体的选择采用的是同一的方法，未来可以根据实体的重要性进行选择
2. 将user和item分隔开，只对item端使用KG进行表示学习，没有尝试加入user的信息或者对user也使用KG进行表示学习

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

## KGAT: Knowledge Graph Attention Network for Recommendation

## RippleNet: Propagating User Preferences on the Knowledge Graph for Recommender Systems

### Intro

现在的基于知识图谱的推荐系统基本上分成两种方法，即：

1. embedding-based methods: 利用知识图谱中目标entity周围的entity的embedding的信息来丰富单个entity的信息，一般使用GCN算法或者GAT算法
2. path-based methods: 一般需要人工设计meta-path/meta-graph，因此模型学习效果的好坏很大程度依赖于meta-path/meta-graph的设计，并且meta-path/meta-graph很难在学习的过程中被优化

而RippelNet将两者相结合，既考虑了embedding的问题，又考虑了path的问题。解决这一问题的方法被称为preference propagation，借助KG，更加有利于发现用户的高层潜在兴趣。

>   For each user, RippleNet treats his historical interests as a seed set in the KG, then extends the user’s interests iteratively along KG links to discover his hierarchical potential interests with respect to a candidate item.

### 模型

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202012/04/215206-491006.png)

对于一次点击历史中的一个实体，其知识图谱中的rippel如下所示：

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202012/04/215346-56410.png)

对于每一次迭代的entity的集合定义如下：
$$
\mathcal{E}_{u}^{k}=\left\{t \mid(h, r, t) \in \mathcal{G} \text { and } h \in \mathcal{E}_{u}^{k-1}\right\}, \quad k=1,2, \ldots, H
$$
其中：
$$
\mathcal{E}_{u}^{0}=\mathcal{V}_{u}=\left\{v \mid y_{u v}=1\right\}
$$
即一开始的seed集合是一个用户所有点击的历史记录中的item对应的entity

>   以第一轮rippel传播为例

每一个传播到的entity对应的权重的计算方式如下：
$$
p_{i}=\operatorname{softmax}\left(\mathrm{v}^{\mathrm{T}} \mathbf{R}_{i} \mathbf{h}_{i}\right)=\frac{\exp \left(\mathrm{v}^{\mathrm{T}} \mathbf{R}_{i} \mathbf{h}_{i}\right)}{\sum_{(h, r, t) \in \mathcal{S}_{u}^{1}} \exp \left(\mathrm{v}^{\mathrm{T}} \mathrm{Rh}\right)}
$$
每一轮传播过后得到的用户表示（局部）为：
$$
\mathbf{o}_{u}^{1}=\sum_{\left(h_{i}, r_{i}, t_{i}\right) \in \mathcal{S}_{u}^{1}} p_{i} \mathbf{t}_{i}
$$
最后通过局部用户表示得到的全局用户表示为：
$$
\mathbf{u}=\mathbf{o}_{u}^{1}+\mathbf{o}_{u}^{2}+\ldots+\mathbf{o}_{u}^{H}
$$
点击率预测方法为：
$$
\hat{y}_{u v}=\sigma\left(\mathbf{u}^{\mathrm{T}} \mathbf{v}\right)
$$

### 特殊

- 使用了Memory Networks

### 实验结果

#### 对于baseline的实验

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202012/04/221542-863577.png)

#### 对于hop number的实验

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202012/04/221629-782447.png)

### case study

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202012/04/222853-559517.png)

### future work

1. further investigate the methods of characterizing entity-relation interactions;
2. design non-uniform samplers during preference propagation to better explore users’ potential interests and improve the performance.
