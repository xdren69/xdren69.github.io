---
title: news-recommend-summary
tags:
---

## 新闻推荐的特点

1.  不同于普通的item推荐，新闻推荐的时效性很强，因此传统的ID-based协同过滤等方法在此处不适用。同时时效性会导致协同过滤矩阵的稀疏
2.  News language is usually highly condensed and comprised of a large amount of knowledge entities and common sense
3.  



## 整体结构





## embedding方法

1.  word2vec
2.  word-graph



## news encoder 常用方法

1.  CNN
2.  KCNN（与知识图谱相结合）





## user encoder常用方法

1.  GRU（long-，short-term）



## news encoder与user encoder相结合的预测概率

1.  点积

2.  DNN神经网络

    ![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202010/11/191334-539886.png)

3.  