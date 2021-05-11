---
title: build-whole-Recsys-1
tags:
---

## 推荐系统的整体架构



### 数据部分

推荐系统的“数据部分”主要负责的是“用户”“物品”“场景”信息的收集与处理。根据处理数据量和处理实时性的不同，我们会用到三种不同的数据处理方式，按照实时性的强弱排序的话，它们依次是：

1.  客户端与服务器端实时数据处理
2.  流处理平台准实时数据处理（Flink）
3.  大数据平台离线数据处理（Spark）



### 模型部分

推荐系统的“模型部分”是推荐系统的主体。模型的结构-般由“召回层”、“排序层” 以及“补充策略与算法层”组成。

1.  召回层：一般由高效的召回规则、算法或简单的模型组成，这让推荐系统能快速从海量的候选集中召回用户可能感兴趣的物品（**深度学习中 Embedding 技术在召回层的应用**）
2.  排序层：利用排序模型对初筛的候选集进行精排序（此处使用**不同结构的深度学习模型**）
3.  补充策略与算法层：在返回给用户推荐列表之前，为兼顾结果的“多样性”“流行度”“新鲜度”等指标，结合一些补充的策略和算法对推荐列表进行一定的调整，最终形成用户可见的推荐列表（增强学习？）
