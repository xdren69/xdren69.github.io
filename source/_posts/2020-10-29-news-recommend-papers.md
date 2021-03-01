---
title: 新闻推荐现有工作的调研(持续更新)
date: 2020-12-5 20:14:29
toc: true
tags:
  - news recommendation
categories:
  - recsys
excerpt: 这是一篇对于新闻推荐现有工作的调研
---



## 新闻推荐的特点

1.  具有较强的时效性，相比于其它推荐任务，不能使用基于ID的MF方法，可用的用户交互数据较少
2.  新闻文章往往简洁、准确。便于使用NLP模型进行处理
3.  新闻中包含很多实体，往往成为文章的keyword；不同于之前的基于item的推荐，往往一个item只对应一个实体



## 新闻推荐的基本思路

对新闻进行分类，具体类别用向量来表示；对于用户的兴趣点采用相同向量空间来表示；最后将两者点乘得到数值表示用户对目标新闻的感兴趣的概率——即分别对news和user学得一个相同向量空间向量表示（不同向量空间则不能使用点乘，可用全连接网络）



## 论文阅读

我们对新闻推荐的方法主要采取了两种分类方法，即是否使用知识图谱，是否使用时间序列。本文所包含的论文汇总如下：

| 题目  | 时间 | 会议   | 包含时间序列 | 包含知识图谱 |
| ----- | ---- | ------ | ------------ | ------------ |
| TEKGR | 2020 | CIKM   |              | 是           |
| KRED  | 2020 | RecSys |              | 是           |
| LSTUR | 2019 | ACL    | 是           |              |
| NPA   | 2019 | KDD    |              |              |
| DKN   | 2018 | WWW    | 是           | 是           |
| DRN   | 2018 | WWW    |              |              |



## TEKGR: News Recommendation with Topic-Enriched Knowledge Graphs 

### Intro

之前基于知识图谱的推荐模型的存在者不足，即：仅仅根据新闻标题中被识别的实体来表示新闻，但往往存在错误。如下表所示，通过训练集可以看出用户感兴趣的是**Marijuana Stock**，而DKN模型通过实体“Cannabis” or “Marijuana”因此会将用户的兴趣点识别为**Politics** and **Crime**，而用户关心的往往是“Prices” or “Stocks”无法被识别为特殊实体。

>   为了解决这个问题，我们给每条新闻加入类别信息，不同于传统的人工加类别信息，我们通过神经网络和知识图谱来学习类别信息

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202012/02/161006-200573.png)

### 模型结构

整体模型分为三层，即：

1.  KG-Based News Modeling Layer（核心）
2.  Attention Layer
3.  Scoring Layer

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202012/02/165742-225544.png)

#### **KG-Based News Modeling Layer**（news embedding）

如下所示：

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202012/02/165942-367237.png)

主要由三个encoder组成，即：

1.  Word-Level News Encoder

    一共有三层组成，即

    -   embedding层
    -   Bi-GRU层，学习共同出现的单词间的联系，而不孤立的提取单个单词的表示
    -   attention层，每一个单词的权重不同

    最终得到word-level的**news representation vector**

2.  Knowledge Encoder 

    为了提取新闻的类别信息，引入了concept的概念，即知识图谱中"is A"的relationship，举例如下：对于新闻标题“Donald Trump vs. Madonna, Everything We Know”，通过知识图谱中的"is A"关系，我们可以得到：Donald Trump is a **politician** and a **CEO** while Madonna is a **singer** and a **feminist**.  通过这些关系，有助于我们判定该新闻为政治类新闻。在这一层中我们先提取出每一个实体的多个"is A"关系对应的实体，称之为"concept"，然后对这些concept进行embedding，最后利用attention机制学习相应的权重。在这个例子中就会为“**politician**”和“**feminist**”设置较高的权重

    最终得到**concept vector**

3.  KG-Level News Encoder

    分为三个步骤：

    -   根据标题提取出实体，将相应的实体映射到知识图谱中
    -   将实体向外延伸2-hop，并从原始的知识图谱中根据这些实体得到相应的子图
    -   为tittle中的各个实体添加topical relation，得到一张新的子图，在图中使用GNN算法得到news的表示

    最终得到KG-level的**news representation vector**

最终得到的news embedding为，word-level和KG-level的**news representation vector**相结合的结果

#### Attention Layer（user embedding）

通过attention学习目标news与user的历史点击的news的相关程度，最终得到user的embedding

#### Scoring Layer

将user embedding和news embedding进行点击，得到点击率

### 实验结果

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202012/02/222126-924376.png)



---

## KRED: Knowledge-Aware Document Representation for News

### Intro

正如文章的名字Knowledge-Aware Document Representation for News Recommendations，这篇文章更注重的是创建一个位于任务上游的news representation，为位于下游的新闻推荐子任务提供类似BERT在NLU领域的工具。

### 创新点

1.  认为现存的文档理解模型要么是没有考虑知识图谱信息，比如BERT；要么是依赖于特殊的文章编码模型，以至于缺乏泛化能力和效率，比如DKN。本篇文章**将知识图谱与BERT相结合**
2.  对于每一个实体在经过知识图谱表示层后，又加入了上下文信息，即context embedding层

### 模型

整体模型分为三层，分别是：

1.  Entity Representation Layer：使用Knowledge Graph Attention (KGAT) Network，将知识图谱中当前实体周围实体的信息汇聚与当前实体，用于增强对于当前实体的表示。其具体公式如下：

    <div>
    $$\mathbf{e}_{\mathcal{N}_{h}}=\operatorname{ReLU}\left(\mathbf{W}_{0}\left(\mathbf{e}_{h} \oplus \sum_{(h, r, t) \in \mathcal{N}_{h}} \pi(h, r, t) \mathbf{e}_{t}\right)\right)$$ 
    </div>

    其中N<sub>h</sub>表示以h为头部实体的三元组，$\pi(h, r, t)$表示注意力权重，用来控制周围节点传播多少信息量到中心节点，其计算方式如下：

    <div>
        $$\pi_{0}(h, r, t)=\mathbf{w}_{2} \operatorname{Re} L U\left(\mathbf{W}_{1}\left(\mathbf{e}_{h} \oplus \mathbf{e}_{r} \oplus \mathbf{e}_{t}\right)+\mathbf{b}_{1}\right)+b_{2}$$
    </div>

    <div>
        $$\pi(h, r, t)=\frac{\exp \left(\pi_{0}(h, r, t)\right)}{\sum_{\left(h, r^{\prime}, t^{\prime}\right) \in \mathcal{N}_{h}} \exp \left(\pi_{0}\left(h, r^{\prime}, t^{\prime}\right)\right)}$$
    </div>

2.  Context Embedding Layer：为每一个实体向量加入位置、频率、类别这三种信息，通过wise-to-wise的直接相加

3.  Information Distillation Layer：使用类似self-attention的机制来得到news representation

![模型](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202010/29/223438-489569.png)

随后，为了提升这个上游模型的性能，对该模型进行Multi-Task的学习（包含Category Classification、Popularity Prediction、Local News Detection、Item Recommendation、Item-to-item Recommendation），一共分为两个阶段：

-   stage1：we alternately train different tasks every few mini-batches. 
-   stage2：we only include the target task’s data to finalize a task-specific model. 

### 反思

1.  对于知识图谱的使用：其中对于知识图谱的使用也是在news representation的阶段，处于整体模型的第一层

---



## NPA: 





## LSTUR: 







---

## DKN: Deep Knowledge-Aware Network for News Recommendation

### 目前新闻推荐的痛点

1.  时效性强，无法使用传统的基于ID的协同过滤的方法
2.  用户的兴趣往往涉及许多，我们需要动态的测量用户的兴趣
3.  新闻中往往包含许多实体和常识，可以借助来进行知识图谱上的推理



### News encoder

对于每一个news tittle一共要学习三个层面得embedding，分别是word embedding, entity embedding以及contextual entity embedding，其中entity embedding和contextual entity embedding的学习如下：

#### Entity embedding

>   利用知识图谱丰富文本表示信息，即知识蒸馏的过程

![Illustration of knowledge distillation process](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/24/213012-28708.png)

1.  先对Tittle中的文本进行entity linking，得到一些实体
2.  将识别出来的实体映射到原始的知识图谱中，丰富实体间的关系，并提取出子图
3.  通过TransE，TransH，TransR，TransD中的一种得到每一个entity的embedding



#### Contextual entity embedding

![Illustration of context of an entity in a knowledge graph](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/24/213434-262642.png)

考虑每个entity在知识图谱中one-hop范围内的所有entity，采用如下方法作为目标entity的Contextual entity embedding：

<div>
    $\operatorname{context}(e)=\left\{e_{i} \mid\left(e, r, e_{i}\right) \in \mathcal{G}\right.$ or $\left.\left(e_{i}, r, e\right) \in \mathcal{G}\right\}$
</div>

<div>
    $\overline{\mathrm{e}}=\frac{1}{\mid \text {context}(e) \mid} \sum_{e_{i} \in \operatorname{context}(e)} \mathbf{e}_{i}$
</div>

### 模型

![Illustration of the DKN framework](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/24/210742-912650.png)

输入：one piece of candidate news and one piece of a user’s clicked news

输出：click-through rate (CTR) prediction

>   此处的user embedding对于不同的candidate news是不同的，因为这里对不同的candidate news使用了注意力机制，对于不同的用户历史记录使用了不同的权重

#### 核心组件：KCNN

模型源自于Kim CNN，如下图所示：

![A typical architecture of CNN for sentence repre-sentation learning](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/24/211047-642292.png)

扩展到本模型中：

![KCNN](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/24/211234-262676.png)

特点：

1.  多通道：将word embedding, entity embedding, and contextual entity embedding of news作为news编码的三个通道
2.  word-entity-aligned：将每一个word与相应的entity对齐（疑问：有些单词没有entity与之对应，需要检查代码）
3.  使用多个filter



#### User encoder: Attention-based User Interest Extraction

![user encoder](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/30/163417-344969.png)

我们认为用户的每一次历史点击对于预测用户是否对于candidate news感兴趣的贡献度不同；因此使用attention机制来学习不同的贡献度，其计算方法如下：

<div>
$$
s_{t_{k}^{i}, t_{j}}=\operatorname{softmax}\left(\mathcal{H}\left(\mathbf{e}\left(t_{k}^{i}\right), \mathbf{e}\left(t_{j}\right)\right)\right)=\frac{\exp \left(\mathcal{H}\left(\mathbf{e}\left(t_{k}^{i}\right), \mathbf{e}\left(t_{j}\right)\right)\right)}{\sum_{k=1}^{N_{i}} \exp \left(\mathcal{H}\left(\mathbf{e}\left(t_{k}^{i}\right), \mathbf{e}\left(t_{j}\right)\right)\right)}
$$
</div>

$t_{k}^{i}$表示用户i的第k次点击；$t_{j}$表示candidate news；$\mathcal{H}$表示DNN网络



#### 实验结果

![对比其他模型](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/24/221727-85238.png)



![消融实验](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/24/221758-419990.png)

---



## 目前新闻推荐的缺点

1.  将用户未点击均视为负样例，容易陷入信息茧房

2.  利用知识图谱对news进行embedding时，有时候title中的entity并不能表示表示新闻的类别以及全部内容

3.  知识图谱对entity进行embedding时，向外发散到其他实体时，目前两种主要做法是：

    1.  无差别发散；
    2.  加入权重进行发散，即GAT；

    但是没有对知识图谱进行裁剪，噪音还是很大的