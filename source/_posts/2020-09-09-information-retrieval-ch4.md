---
title: chap 4-Index construction
date: 2020-09-09 09:36:43
toc: true
categories:
  - information retrieval
excerpt: 这是一篇关于《信息检索导论》第四章的读书笔记，主要学习了如何在不同的硬件环境和文本集大小的条件下构建索引表
---

## 基于块的排序索引方法（BSBI）

>   此处主要使用的算法是外部排序算法和归并算法

主要步骤为：

1.  将文档集分割成几个大小相等的部分（所谓的块）
2.  将每个部分的词项 ID—文档 ID 对排序（将每个词项映射为ID）
3.  将中间产生的临时排序结果存放到磁盘中
4.  将所有的中间文件合并成最终的索引

特点：

1.  需要构建词项-词项ID的映射表
2.  所有的索引结果都需要经过排序



## single-pass in-memory indexing（SPIMI）

相比于BSBI，其优点为：

1.  不需要构建词项-词项ID的映射表，节约内存
2.  索引结果不需要排序，节约时间

算法描述：

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202009/09/102342-615432.png)

最后的内存块的合并过程此处省略，与BSBI相同。

特点：将词典直接存入每一个内存块中



## 分布式索引构建方法 

采用MapReduce框架，其图示如下所示：

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202009/09/131230-92186.png)

-   Map 阶段：将输入的数据片映射成键—值对。这个映射过程对应于 BSBI 和 SPIMI 算法中的分析任务，因此也将执行 Map 过程的机器称为分析器（parser）
-   Reduce阶段：将同一键（词项ID）的所有值（文档ID）集中存储，以便快速读取和处理。实现时，将所有的键按照词项区间划分成j个段，并将属于每个段的键—值对写入各自分区文件即可



## 动态索引构建方法

-   周期性重建索引：周期性地对文档集从头开始进行索引重构。如果随时间的推移 文档更新的次数不是很多，并且***能够接受对新文档检索的一定延迟***，再加上如果有足够的资源 能够支持在建立新索引的同时让旧索引继续工作，那么周期性索引重构不失为一种较好的选择
-   添加辅助索引：***要求能够及时检索到新文档***，一个是大的主 索引，另一个是小的用于存储新文档信息的辅助索引，后者保存在内存中。检 索时可以同时遍历两个索引并将结果合并。文档的删除记录在一个无效位向量中，在返回结果之前可以利用它过滤掉已删除文档。某篇文档的更新通过先删除后重新 插入来实现。



## 其他检索类型

-   排序式检索系统（ranked retrieval）：相比于布尔检索系统，不按照文档ID排序，仅仅按照权重或者影响程度排序
-   安全性检索系统：添加用户访问权限，构建ACL（access control list，访问控制表）
