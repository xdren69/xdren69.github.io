---
title: (DKN)Deep Knowledge-Aware Network for News Recommendation 论文阅读
date: 2021-03-01 20:59:03
toc: true
categories:
  - paper reading
tags:
  - news recommendation
excerpt: 发表于2020 WWW的有关新闻推荐的论文
---

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

$\operatorname{context}(e)=\left\{e_{i} \mid\left(e, r, e_{i}\right) \in \mathcal{G}\right.$ or $\left.\left(e_{i}, r, e\right) \in \mathcal{G}\right\}$


$\overline{\mathrm{e}}=\frac{1}{\mid \text {context}(e) \mid} \sum_{e_{i} \in \operatorname{context}(e)} \mathbf{e}_{i}$


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

$$
s_{t_{k}^{i}, t_{j}}=\operatorname{softmax}\left(\mathcal{H}\left(\mathbf{e}\left(t_{k}^{i}\right), \mathbf{e}\left(t_{j}\right)\right)\right)=\frac{\exp \left(\mathcal{H}\left(\mathbf{e}\left(t_{k}^{i}\right), \mathbf{e}\left(t_{j}\right)\right)\right)}{\sum_{k=1}^{N_{i}} \exp \left(\mathcal{H}\left(\mathbf{e}\left(t_{k}^{i}\right), \mathbf{e}\left(t_{j}\right)\right)\right)}
$$

$t_{k}^{i}$表示用户i的第k次点击；$t_{j}$表示candidate news；$\mathcal{H}$表示DNN网络



#### 实验结果

![对比其他模型](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/24/221727-85238.png)



![消融实验](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/24/221758-419990.png)

---

