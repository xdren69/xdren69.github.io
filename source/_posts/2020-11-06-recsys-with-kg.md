---
title: 知识图谱与推荐系统
date: 2020-12-07 22:03:20
toc: true
tags:
  - item recommendation
categories:
  - paper reading
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
