---
title: datawhale-零基础入门NLP-Task5(未完待续)
date: 2020-07-31 20:17:51
tags:
  - datawhale
  - NLP
toc: true
categories:
  - Datawhale学习手记
excerpt: 这是一篇关于参与datawhale的数据比赛的组织活动的第五篇篇博客记录，主要记录的是Task5-基于深度学习的其他模型的文本分类部分的完成情况
---

## Introduction

使用fastText之外的深度学习模型，例如word2vec、TextCNN、TextRNN，来完成文本分类任务



## 模型学习

首先我们先学习一下我们即将使用的三种模型

### word2vec





### TextCNN

>   TextCNN利用CNN（卷积神经网络）进行文本特征抽取，不同大小的卷积核分别抽取n-gram特征，卷积计算出的特征图经过MaxPooling保留最大的特征值，然后将拼接成一个向量作为文本的表示。



### TextRNN

>   TextRNN利用RNN（循环神经网络）进行文本特征抽取，由于文本本身是一种序列，而LSTM天然适合建模序列数据。TextRNN将句子中每个词的词向量依次输入到双向双层LSTM，分别将两个方向最后一个有效位置的隐藏层拼接成一个向量作为文本的表示。



## Reference

[1] [Datawhale零基础入门NLP赛事 - Task5-word2vec官方文档](https://tianchi.aliyun.com/notebook-ai/detail?spm=5176.12586969.1002.3.64063dadTKJRsp&postId=118268)

[2] [Datawhale零基础入门NLP赛事 - Task5-TextCNN官方文档](https://tianchi.aliyun.com/notebook-ai/detail?spm=5176.12586969.1002.9.64063dadTKJRsp&postId=118258)

[3] [Datawhale零基础入门NLP赛事 - Task5-TextRNN官方文档](https://tianchi.aliyun.com/notebook-ai/detail?spm=5176.12586969.1002.12.64063dadTKJRsp&postId=118259)