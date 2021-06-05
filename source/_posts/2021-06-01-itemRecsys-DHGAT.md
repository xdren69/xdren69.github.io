---
title: itemRecsys-DHGAT
date: 2021-06-01 00:25:11
tags:
---

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/20210601002622.png)



提问：

1.  长尾问题的定义
2.  题目中dual的含义
3.  一些边的权重分配



## 本文面对的问题

**商店搜索**中面临的问题：

1.  很多商店的名字很短，无法表述它所卖的东西；很多用户提供的query是模糊不清的，因此存在shop和query间的语义差异

2.  由于用户一般进行的都是对item的搜索，因此缺乏shop和query之间的交互，同时shop与query均存在长尾问题，即很多query和shop出现的次数很少；




## 本文提出的解决方式

本文认为问题1和问题2导致的结果为：我们没有足够的信息去进行query和shop的表征，为了解决这个问题，我们提出两点解决方法：

1.  引入图神经网络（GNN），设计出适合当前应用场景的独特网络：a dual heterogeneous graph attention network，探索汇聚多跳内的节点信息信息来丰富当前节点的信息；
2.  由于item-query的数据十分丰富，我们将item-query的信息迁移至当前的shop-query领域。即通过item的信息来丰富query和shop的信息；

使用的交互数据：user search behaviors, user click-through behaviors, user purchase records.

输入双塔的数据：graph-based representations and text-level representations along with multiple user features



## 整体结构

>   模型主体依旧为双塔结构，两个塔的输出分别为**\<user+query\>**的向量表示和**\<shop\>**的向量表示；

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/20210604102229.png)



>   为了更好地得到query和shop的两个向量表示，我们通过两个子模型**DHGAT**和**TKPS**分别为query和shop学到两个向量，其中：

**TKPS**用于将item所提供的额外txt信息汇聚到query和shop的节点表示中，即进行item---shop，item---query之间的信息融合；

**DHGAT**用于在异构图上进行shop---query，shop---shop，query---query之间的信息传递与融合；



>   接下来，我们首先介绍如何构建异构图，再介绍每个子模块的实现

### 异构图构建

>   文章认为graph的构建是GNN能学到好的emb的基础，我们首先构建出一个反映原始数据间关系的复杂大图（包含多节点，多关系），然后根据**DHGAT**和**TKPS**各自运用到的信息的不同，利用the first-order and second-order proximity**抽取**出适用于不同模型的子图

复杂图中运用到的的节点类型和边的类型有

-   节点：user, query, shop, item
-   边：两种同构，shop---shop，query---query；三种异构，shop---item，query---item，shop---query

复杂图以及子图的抽取过程如下所示：

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/20210604104419.png)

### DHGAT

>   此子模型直接进行query和shop之间的信息传递

从复杂图中提取出的子图为：

![子图1](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/20210604231954.png)

图中包含的信息有：

**节点**：query，shop

**边**：

| 名称          | 来源                                                         | 权重         |
| ------------- | ------------------------------------------------------------ | ------------ |
| query---query | 1. 同一个session之间的所有query构成彼此的同构邻居；<br />2. 导致用户点击同一个shop的query之间； |              |
| shop---shop   | 同一个query下点击的不同shop之间                              |              |
| shop---query  | 通过query点击的不同shop，既可以构建query和shop的异构联系     | 用户的点击数 |

>   注意：对于shop---query边，用于用户大多进行的是query---item，因此这里存在数据的稀疏性，将来会导致长尾现象，以及query和shop之间的语义不匹配的问题；为了解决这个问题，我们将商品搜索的知识迁移过来；即构建second-order proximity，具体为：用户通过一个query购买了一个shop的item，那么我们可以建立起query和shop之间的联系

模型结构图如下所示：

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/20210604103133.png)

我们可以看到，主要由`attention net`以及`HNTM`这两个子模块构成，之所以引入`HNTM`这个模块是因为抽取出的子图1为异构图，一共包含两种类型的节点，在对每一个节点进行信息汇聚时，我们不能单一地对两种节点进行相同的处理；需要通过`HNTM`进行语义间的转化

>   以下以对query节点进行信息汇总为例

**attention net**
$$
\mathbf{h}_{\mathcal{N}_{o}(q)}^{t}=\sum_{i \in \mathcal{N}_{o}(q)} \alpha_{q, i}^{t} \mathbf{h}_{i}^{t-1}
$$

$$
\alpha_{q, i}^{t}=\frac{\exp \left(\operatorname{Leak} y \operatorname{ReL} U\left(\mathbf{a}_{q, t}^{\top}\left[\mathbf{h}_{q}^{t-1} \| \mathbf{h}_{i}^{t-1}\right]\right)\right)}{\sum_{k \in \mathcal{N}_{o}(q)} \exp \left(\operatorname{Leak} y \operatorname{ReL} U\left(\mathbf{a}_{q, t}^{\top}\left[\mathbf{h}_{q}^{t-1} \| \mathbf{h}_{k}^{t-1}\right]\right)\right)}
$$



**HNTM**（heterogeneous neighbor transformation matrix）
$$
\mathbf{h}_{\mathcal{N}_{e}(q)}^{t}=\sum_{j \in \mathcal{N}_{e}(q)} \alpha_{q, j}^{t} \mathcal{W}_{Q S} \mathbf{h}_{j}^{t-1}
$$

$$
\alpha_{q, j}^{t}=\frac{\exp \left(\operatorname{Leak} y \operatorname{ReL} U\left(\mathbf{b}_{q, t}^{\top}\left[\mathbf{h}_{q}^{t-1} \| \mathcal{W}_{Q S} \mathbf{h}_{j}^{t-1}\right]\right)\right)}{\sum_{k \in \mathcal{N}_{e}(q)} \exp \left(\operatorname{Leak} y \operatorname{ReL} U\left(\mathbf{b}_{q, t}^{\top}\left[\mathbf{h}_{q}^{t-1} \| \mathcal{W}_{Q S} \mathbf{h}_{k}^{t-1}\right]\right)\right)}
$$

对同构节点信息与异构节点信息进行汇总时，依旧采用相同的`attention net`
$$
\mathbf{h}_{\mathcal{N}(q)}^{t}=\beta_{\mathcal{N}_{o}(q)}^{t} \mathbf{h}_{\mathcal{N}_{o}(q)}^{t}+\beta_{\mathcal{N}_{e}(q)}^{t} \mathbf{h}_{\mathcal{N}_{e}(q)}^{t}
$$

$$
\beta_{\mathcal{N}_{*}(q)}^{t}=\frac{\exp \left(\operatorname{Leak} y \operatorname{ReL} U\left(\mathbf{c}_{q, t}^{\top}\left[\mathbf{h}_{q}^{t-1} \| \mathbf{h}_{\mathcal{N}_{*}(q)}^{t}\right]\right)\right)}{\sum_{k=\left\{\mathcal{N}_{o}(q), \mathcal{N}_{e}(q)\right\}} \exp \left(\text { LeakyReLU }\left(\mathbf{c}_{q, t}^{\top}\left[\mathbf{h}_{q}^{t-1} \| \mathbf{h}_{k}^{t}\right]\right)\right)}
$$

将汇聚信息与原节点信息进行融合的方式为：
$$
\mathbf{h}_{q}^{t}=\operatorname{Leak} y \operatorname{Re} L U\left(\mathcal{W}_{q}^{t} \cdot\left[\mathbf{h}_{q}^{t-1} \| \mathbf{h}_{\mathcal{N}(q)}^{t}\right]\right)
$$


### TKPS

>   由于shop的名字的长度的限制，query长度的限制，存在shop和query之间语义表示不足的问题，为了解决这个问题，我们引入了item节点，利用item的信息来丰富shop和query的信息

因此我们从复杂图中抽取的子图为：

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/20210604233620.png)

图中包含的信息有：

**节点**：query，shop，item

**边**：

| 名称         | 来源                                       | 权重         |
| ------------ | ------------------------------------------ | ------------ |
| query---item | 建立同一query下购买的item与query之间的联系 | 用户的点击数 |
| shop---item  | 建立同一shop下所有item与shop的联系         | 1            |

模型的结构为：

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/20210604103105.png)





## 实现细节

