---
title: CNN在NLP中的应用——以Text Classification为例
toc: true
categories:
- CS224n
tags:
- NLP
excerpt: 这是一篇关于CNN在NLP中应用的学习笔记，是CS224n-2019中lec11的学习笔记
---

### 1. CNN

全称Convolutional Neural Network，即卷积神经网络。来源于卷积计算，以二维卷积在计算机视觉中的应用为例：

![](http://ufldl.stanford.edu/tutorial/images/Convolution_schematic.gif)

其中橘黄色3\*3的矩阵为正在进行卷积计算的部分，其中红色数字为卷积核（或filter）的内容，右侧矩阵为通过卷积计算所提取出的特征

#### 1.1 CNN在NLP中的应用

RNN的缺陷：

-   只在最后一个节点的隐藏状态中使用softmax函数，所提取的信息量有限；
-   对于句子中phrases的理解，仅仅通过前缀信息来理解；
-   虽然LSTM弥补了上述缺陷，但是由于需要前一个时间点的数据来计算当前时间点的数据，因此其并行计算的能力不足，训练起来很慢

而CNN恰好能补足这些缺点：

-   同时通过卷积核来捕捉一个单词及其周围的词语，借此来理解当前单词的上下文信息
-   CNN具有较好的并行性，训练速度比较快

一般在NLP中使用一维卷积（即卷积核只向着一个方向移动），应用举例如下：





#### 1.2 CNN中的概念

##### 1.2.1 padding

用于在待卷积的数据的两侧或一侧pad 0，从而使卷积的结果与待卷积的数据等长

##### 1.2.2 global pooling, local pooling, k-max pooling

在卷积完成后对有效数据的提取

##### 1.2.3 stride

每次进行卷积时的跨度大小

##### 1.2.4 *dilation*

##### 1.2.5 ResNet in CNN

##### 1.2.6 Highway in CNN

##### 1.2.7 1*1 Convolutions



### 2. text classification

判断一段文本是主观还是客观，或者是积极还是消极；即对文本进行分类

#### 2.1 A Sensitivity Analysis of (and Practitioners’ Guide to) Convolutional Neural Networks for Sentence Classification

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202007/22/090443-35935.png)

#### 2.2 Very Deep Convolutional Networks for Text Classification

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202007/22/090446-6263.png)
