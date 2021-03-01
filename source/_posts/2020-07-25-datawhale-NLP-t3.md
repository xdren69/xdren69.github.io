---
title: datawhale-零基础入门NLP-Task3
tags:
  - datawhale
  - NLP
toc: true
categories:
  - Datawhale学习手记
excerpt: 这是一篇关于参与datawhale的数据比赛的组织活动的第三篇博客记录，主要记录的是Task3-基于机器学习的文本分类部分的完成情况
date: 2020-07-25 23:19:18
---

# 学习目标

此次任务的目标是：在不同的文档表示方法下，使用传统机器学习算法来完成文本分类任务

# 文本表示方法(part1)

>   由于机器学习模型的输入只能是数字，而不是文本；所以在实现文本分类任务时，我们首先应该考虑的是如何将一段文本向量化



## Bag of Words (AKA Count Vectors)

用双城记来举例子展示我们是如何计算Bag of Words的

>   *It was the best of times,*
>   *it was the worst of times,*
>   *it was the age of wisdom,*
>   *it was the age of foolishness,*

假设这四句话每一句都是一段文本，这四个文本共同组成了一个语料库

1.  设计词典：

    通过对以上的文本进行分析，我们可以得出语料库中一共出现了如下10个词语

    ```tex
    it、was、the、best、of、times、worst、age、wisdom、foolishness
    ```

2.  计算出这段文本的向量表示，即将向量的长度设计为10，每一维度对应某个文本中一个单词出现的次数，即可以得到

    ```tex
    "It was the best of times" = [1, 1, 1, 1, 1, 1, 0, 0, 0, 0]
    "it was the worst of times" = [1, 1, 1, 0, 1, 1, 1, 0, 0, 0]
    "it was the age of wisdom" = [1, 1, 1, 0, 1, 0, 0, 1, 1, 0]
    "it was the age of foolishness" = [1, 1, 1, 0, 1, 0, 0, 1, 0, 1]
    ```

>   对这一算法的直观理解是：词汇分布越相似的文本，其含义越相近



## TF-IDF

根据这一算法的名字，我们将分成TF、DF、IDF三个小部分来介绍

>   我们的基本思想是：寻找一些词语，用这些词语来代表一个文本；这一算法多用在搜索引擎等技术中，用于搜索和待检查词语匹配的文章
>
>   根据日常经验，我们的基本思路是：寻找在一个文本中出现频率最高的几个词来代表这段文本，但是要排除停顿词，如英语中的“a, the, is, was”等



### TF

全名是**Term Frequency**，即词频。因为词频最能直观反映一篇文章的keywords，比如一篇写猫的文章，其中“猫”这个字的出现的次数一定很多。

由于有些文本是长文本，有些是短文本；就算同是写猫的文本，长文本中“猫”出现的次数一定多于短文本中“猫”出现的次数，但是却不能表明长文本中与“猫”的相关度比短文本强。因此我们在这里需要进行正则化，即：

```tex
tf(t,d) = count of t in d / number of words in d
```



### DF

全名是**Document Frequency**，即文档频率。这个词可不是它的字面意思哈！当我们计算一个单词A的DF时，实际上我们是在计算语料库中包含单词A的文档的数量。其公式如下：

```tex
df(t) = occurrence of t in documents
```

我们之所以要计算DF，是为了用到它的倒数IDF，DF表明了一个单词的普遍程度，DF的值越大，表明当前单词在大部分的文章都出现过，十分普遍，不具备代表意义



### IDF

全名是**Inverse Document Frequency**，即逆文档频率，它就是DF的倒数。其含义已在DF中说过了，这里就说一下计算公式吧：

$$\operatorname{idf}(\mathrm{t})=\log (\mathrm{N} /(\mathrm{df}+1))$$

之所以+1，是因为防止除数为0（在搜索系统中，待查询的词可能没在文本中出现过）；之所以取log，是因为防止数据爆炸（value explode）



>   综上，
>
>   -   一个词语的TF和IDF与这个词语在一段文本中是否具有代表性是正相关的，因此**TF*IDF**与这个词语也是正相关的；在实际文本中一般选择**TF*IDF**中最大的头几个词来代表文本
>
>   -   在本题中会将文本转化成一个长度固定为vocab_size的向量，每一维度对应着相应下标的单词的**TF*IDF**值



## Trick: N-gram

通过对以上两种算法的学习，我们发现这两种算法的需要构建词典来进行词频统计，在构建词典时，我们可以使用N-gram算法来扩充我们的词典，让词典中的一些词更有意义。N-gram是将连续的N个词组成一个新的词语来看待，基本思想*将文本内容按照字节顺序进行大小为N的滑动窗口操作，最终形成长度为N的字节片段序列*

举例而言，对于“It was the best of times”这句话使用2-gram（*bigram*）来分析，会在词典中增加这些单词：

```tex
“it was”、“was the”、“the best”、“best of”、“of times”
```

以后在统计词频时，会将以上算作是一个单词来看待。



# 机器学习模型

本次调用的是**Ridge Classifier**分类模型，是由岭回归（Ridge Regression）变化而来的，我们接下来先介绍岭回归。



## 线性回归

用如下所示的线性模型，去拟合数据

$$\hat{y}=w[0] \times x[0]+w[1] \times x[1]+\ldots+w[n] \times x[n]+b$$

为了检测拟合结果，引入了代价函数（cost function），训练时代价越高，模型效果越差

<div>
$$\sum_{i=1}^{M}\left(y_{i}-\hat{y}_{i}\right)^{2}=\sum_{i=1}^{M}\left(y_{i}-\sum_{j=0}^{p} w_{j} \times x_{i j}\right)^{2}$$
</div>

可能出现的结果：

| 训练集cost | 测试集cost |        模型效果         |
| :--------: | :--------: | :---------------------: |
|     大     |     大     | 欠拟合（under fitting） |
|     小     |     小     |         效果好          |
|     小     |     大     | 过拟合（over fitting）  |



## 岭回归（Ridge Regression）

>   本质是在线性回归的基础上加入了L2正则化

进行拟合的函数是不变的，变化的是代价函数（cost function）的计算方法

<div>
$$\sum_{i=1}^{M}\left(y_{i}-\hat{y}_{i}\right)^{2}=\sum_{i=1}^{M}\left(y_{i}-\sum_{j=0}^{p} w_{j} \times x_{i j}\right)^{2}+\lambda \sum_{j=0}^{p} w_{j}^{2}$$
</div>

相当于在减小cost function的同时需要满足如下条件：

$$\text { For some } c>0, \sum_{j=0}^{p} w_{j}^{2}<c$$

即对模型中的参数的大小都进行了一定程度的抑制，通过$\lambda$的值来控制抑制程度；$\lambda$越接近0，就越接近普通的线性回归

>   注意：只能用来解决过拟合问题，不能用来筛选特征



## Lasso回归

>   本质是在线性回归的基础上加入了L1正则化

<div>
$$\sum_{i=1}^{M}\left(y_{i}-\hat{y}_{i}\right)^{2}=\sum_{i=1}^{M}\left(y_{i}-\sum_{j=0}^{p} w_{j} \times x_{i j}\right)^{2}+\lambda \sum_{j=0}^{p}\left|w_{j}\right|$$
</div>

相当于在减小cost function的同时需要满足如下条件：

$$\text { For some } t>0, \sum_{j=0}^{p}\left|w_{j}\right|<t$$

>   可以解决的问题：
>
>   -   筛选特征，会将无用的特征前的权重赋值为0
>   -   防止过拟合

==对于欠拟合模型，适当减少$\lambda$ ，并增加迭代次数，有助于减小cost的值==



## Ridge Classifier

>   本质是将Ridge Regression转化为一个多输出的回归，预测类对应回归的最高值输出



# 代码实现

具体代码如下所示：

-   Count Vectors  +  RidgeClassifier

    ```python
    import pandas as pd
    
    from sklearn.feature_extraction.text import CountVectorizer
    from sklearn.linear_model import RidgeClassifier
    from sklearn.metrics import f1_score
    
    train_df = pd.read_csv('../data/train_set.csv', sep='\t', nrows=15000)
    
    vectorizer = CountVectorizer(max_features=3000)
    train_test = vectorizer.fit_transform(train_df['text'])
    
    clf = RidgeClassifier()
    clf.fit(train_test[:10000], train_df['label'].values[:10000])
    
    val_pred = clf.predict(train_test[10000:])
    print(f1_score(train_df['label'].values[10000:], val_pred, average='macro'))
    ```

    

-   TF-IDF  +  RidgeClassifier

    ```python
    import pandas as pd
    
    from sklearn.feature_extraction.text import TfidfVectorizer
    from sklearn.linear_model import RidgeClassifier
    from sklearn.metrics import f1_score
    
    train_df = pd.read_csv('../data/train_set.csv', sep='\t', nrows=15000)
    
    tfidf = TfidfVectorizer(ngram_range=(1,3), max_features=3000)
    train_test = tfidf.fit_transform(train_df['text'])
    
    clf = RidgeClassifier()
    clf.fit(train_test[:10000], train_df['label'].values[:10000])
    
    val_pred = clf.predict(train_test[10000:])
    print(f1_score(train_df['label'].values[10000:], val_pred, average='macro'))
    ```

    

# 引用

[1] [Datawhale零基础入门NLP赛事 - Task3官方文档](https://tianchi.aliyun.com/notebook-ai/detail?postId=118254)

[2] [A Gentle Introduction to the Bag-of-Words Model](https://machinelearningmastery.com/gentle-introduction-bag-words-model/)

[3] [TF-IDF from scratch in python on real world dataset.](https://towardsdatascience.com/tf-idf-for-document-ranking-from-scratch-in-python-on-real-world-dataset-796d339a4089)

[4] [Ridge and Lasso Regression: L1 and L2 Regularization](https://towardsdatascience.com/ridge-and-lasso-regression-a-complete-guide-with-python-scikit-learn-e20e34bcbf0b)

