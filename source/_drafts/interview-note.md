---
title: interview-note
tags:
---

### 防止过拟合的方法 

>   特征数多，样本数少

1.  early stop（设置验证集）：
    -   K折交叉验证：将数据集分为k份，每次在(k-1)上训练，在剩余的1上进行验证；之后对K次试验取平均
    -   设置标准验证集
2.  Dropout：在整个训练过程的每一次迭代中，dropout包括在计算下一层之前将当前层中的一些节点置零
3.  L2正则化：算法偏向于在大量特征上均匀分布权重的模型；L1更多的用于特征选择
4.  收集更多的训练数据



### 召回层的作用及实现

1.  基于多路召回策略
2.  基于embedding实现，局部哈希敏感



### CTR模型的种类



#### 机器学习模型

##### 矩阵分解

##### LR

>    只使用了一阶特征间的交互

<img src="https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/image-20210419171900869.png" style="zoom:50%;" />

-   前提条件是：特征之间没有相互关联，无法进行特征交叉
-   最终函数为：sigmoid函数，预测的是用户的点击率

##### FM、FFM

> 使用了二阶特征间的交互

##### GBDT+LR

>   利用GBDT进行特征的混合与筛选



#### 深度学习模型

##### AutoRec

>   首次在推荐系统中引入深度学习

$$
\min _{\theta} \sum_{r \in S}\|r-h(r ; \theta)\|_{2}^{2}
$$

主要是利用协同过滤中的数据，输入自编码器的数据是所有用户对同一物品的评分向量；目标是学到h函数，能够补全评分向量中未出现的信息。

推荐过程：将物品的评分向量输入h函数时，第i维对应的值，即为第i个用户的评分

特点：模型太简单，表达能力不足



##### Deep Crossing

>   首次实现特征的自动交叉和提取

<img src="https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/image-20210419200759748.png" style="zoom:50%;" />

其中残差网络（跳连）的基本结构为：

<img src="https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/image-20210419201212194.png" alt="image-20210419201212194" style="zoom: 33%;" />

残差网络的作用是拟合目标值与预测值之间的残差，由于残差网络的结构，梯度可以越过两层网络向后传播

可以解决：

1.  防止过拟合
2.  防止梯度消失，加快收敛速度



##### NeuralCF

>   实现CF与深度学习的结合，**利用多层神经网络代替协同过滤中的点积操作**

<img src="https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/image-20210419172045924.png" style="zoom:67%;" />

-   使用两组embedding，一个是MF的embeding，一个是随机初始化的embedding
-   MF的embedding做element-wise乘法，得到新的向量
-   MLP的embedding经过拼接后通过多层MLP网络



##### PNN

>   相比于deep crossing模型，将stacking layer替换为product layer；
>
>   相比于NeuralCF模型，将考虑除了用户embedding和商品embedding之外的更多的embedding

<img src="https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/image-20210419210839639.png" style="zoom:50%;" />



##### wide&deep

<img src="https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/image-20210419172137810.png" style="zoom:50%;" />

-   一部分特征通过wide层，用于增强记忆能力
-   一部分特征通过deep层，用于增强泛化能力



> FM与深度学习





> 注意力机制与深度学习

##### DIN

-   将待推荐物品作为注意力机制中得query，计算点击历史的权重



##### DIEN

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/image-20210419210256405.png)

-   兴趣抽取层：

    通过一层GRU实现

-   兴趣演化层：

    通过带attention的GRU实现，其中attention的查询向量为广告向量，使用的是双线性attention



### 图神经网络

-   GCN：基于频域、基于空间域
-   GAT：基于空间域



### BERT中的词向量计算方式



### LSTM和attention的区别

1.  计算方面：LSTM是串行的，attention是完全并行的
2.  学习内容：LSTM必须有时序关系，attention或者self attention就完全没有，所以transformer需要加入position embedding



### attention的种类

1.  软性注意力机制：权重为各个向量和查询向量Q的相关程度

2.  自注意力机制：self-attention

    <img src="https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/image-20210419135352879.png" style="zoom:50%;" />

3.  硬性注意力机制：只选取概率高的作为输出



### 激活函数

1.  relu：可以解决梯度消失
2.  pRelu：
3.  sigmoid：
4.  tanh：



### 检测指标

1.  准确率：模型预测的所有样例中，预测正确的
2.  精确率：模型认为的正样例中，真正的正样例所占的比例
3.  召回率：在所有的正样例中，模型预测对了多少（找到了多少）
4.  F1：召回率和精确率的调和平均数



### AUC的计算方法

适用于分类问题，关于阈值设定的问题，计算正样本排在负样本之前的程度

理解ROC请看[这里](https://zhuanlan.zhihu.com/p/25212301)

理解AUC请看[这里](https://blog.csdn.net/qq_22238533/article/details/78666436)，即随机从模型中抽取一个正样例，一个负样例，放入模型中，正样例的概率大于负样例的概率的概率

![image-20210413172147541](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/image-20210413172147541.png)



### MIND数据集

用户数据：100万

新闻数据：16万

点击信息：2400万



### 普通的新闻推荐系统为什么使用LSTM、GRU对用户序列进行建模：

1.  用户的点击序列是一个时间序列
2.  新闻本身相比较于商品推荐具有更高的时效性



### 我们的论文

1. TransE（relation的embedding只在这里被用到）、图卷积的方法RGC--head+tail，生成注意力权重
2. 先学到新闻表示，再通过新闻表示得到用户表示；这种方法中每一个新闻表示对于不同的用户都是相同的，没法做到个性化推荐；表示新闻的粒度太粗了
3. 之前的网络对于每一个实体（item）向外扩散时虽然使用GAT，但是考虑的是尾部和头部的关联程度，没有考虑到用户的兴趣



### 冷启动

1.  物品冷启动：
2.  用户冷启动：非个性化推荐、利用用户注册时的人口统计信息
3.  系统冷启动



### bias

1.  位置bais：
    -   训练时加入一个模块，学习位置bais带来的概率偏差；预测时，不加该模块
    -   将位置信息作为新的特征加入物品embedding，线上无法获取



### 数据类别不均衡的处理方法及优缺点

1.  过采样：把小众类数据复制多份；

    >    容易过拟合，噪声点被复制了多次；在每次生成新的数据时，加入随机扰动

2.  欠采样：从大众类数据中剔除一些样本，丢失了一部分样本；

    >   进行多次独立地下采样，训练多个分类器，将最终的结果相互融合

3.  增大小样本的权值

4.  生成数据（未尝试）



### 特征选择的方法和好处

-   Filter：过滤法，按照发散性或者相关性对各个特征进行评分，设定阈值或者待选择阈值的个数，选择特征。

-   Wrapper：包装法，根据目标函数（通常是预测效果评分），每次选择若干特征，或者排除若干特征。

-   Embedded：嵌入法，先使用某些机器学习的算法和模型进行训练，得到各个特征的权值系数，根据系数从大到小选择特征。类似于Filter方法，但是是通过训练来确定特征的优劣。
    -   L1惩罚法



### 机器学习里的方差，偏差

1.  方差：是指有所有采样得到的大小为m的训练数据集，训练出的所有模型的输出的方差
    -   描述模型对于给定值的输出稳定性。分类器对样本分布过于敏感，到指在训练样本较少的时候，出现过拟合
2.  偏差：偏差是指由有所采样得到的大小为m的训练数据集，训练出的所有模型的输出的平均值和真实模型输出之间的偏差。
    -   通常是由对学习算法做了错误的假设导致的
    -   描述模型输出结果的期望与样本真实结果的差距。分类器表达能力有限导致的系统性错误，表现在训练误差不收敛



### 词向量模型

#### word2vec

[图解word2vec](https://zhuanlan.zhihu.com/p/74720197)

<img src="https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/image-20210419221057213.png" style="zoom:33%;" />

>   为每个单词生成两个embedding，其中embedding作为query向量，context作为key向量

中心思想：

1.  skip-gram：

    原本思想：通过一个单词，预测窗口范围它周围的单词

    实际计算中：两个单词是否相邻

2.  负采样：随机采集负样本



#### glove

[GloVe详解](http://www.fanyeong.com/2018/02/19/glove-in-detail/)

训练步骤如下：

1.  生成共现矩阵：

    ![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/image-20210419223122509.png)

2.  构建词向量：

    ![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/image-20210419223210508.png)

3.  构造损失函数：

    ![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/image-20210419223252069.png)



#### glove vs word2vec

-   LSA（Latent Semantic Analysis）是一种比较早的count-based的词向量表征工具，它也是基于co-occurance matrix的，只不过采用了基于奇异值分解（SVD）的矩阵分解技术对大矩阵进行降维，而我们知道SVD的复杂度是很高的，所以它的计算代价比较大。还有一点是它对所有单词的统计权重都是一致的。而这些缺点在GloVe中被一一克服了。

-   而word2vec最大的缺点则是没有充分利用所有的语料，所以GloVe其实是把两者的优点结合了起来。



>   从这篇论文给出的实验结果来看，GloVe的性能是远超LSA和word2vec的，但网上也有人说GloVe和word2vec实际表现其实差不多。