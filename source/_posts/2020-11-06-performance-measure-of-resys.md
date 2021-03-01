---
title: 推荐系统的评测标准
date: 2020-11-06 10:53:16
toc: true
categories:
  - recsys
excerpt: 这是一篇介绍了推荐系统的两种测评方法：hit\@k和ndcg\@k
---

## **hit@k**

定义如下：

In a nutshell, it is the count of how many positive triples are ranked in the top-n positions against a bunch of synthetic negatives.

具体计算方法为：

In the following example, pretend the test set includes two ground truth positive only:

```txt
Jack   born_in   Italy
Jack   friend_with   Thomas
```

Let's assume such positive triples (identified by * below) are ranked against four synthetic negatives each. Now, assign a score to each of the positives and its synthetic negatives using your pre-trained embedding model. Then, sort the triples in descending order. In the example below, the first triple ranks 2nd, and the other triple ranks first (against their respective synthetic negatives):

```
s        p         o            score   rank
Jack   born_in   Ireland        0.789      1
Jack   born_in   Italy          0.753      2  *
Jack   born_in   Germany        0.695      3
Jack   born_in   China          0.456      4
Jack   born_in   Thomas         0.234      5

s        p         o            score   rank
Jack   friend_with   Thomas     0.901      1  *
Jack   friend_with   China      0.345      2
Jack   friend_with   Italy      0.293      3
Jack   friend_with   Ireland    0.201      4
Jack   friend_with   Germany    0.156      5
```

Then, count how many positives occur in the top-1 or top-3 positions, and divide by the number of triples in the test set (which in this example includes 2 triples):

```
Hits@3= 2/2 = 1.0
Hits@1= 1/2 = 0.5
```

## **ndcg@k**

全名**Normalized Discounted Cumulative Gain**，是一个测量排序质量的指标。我们将会分成三个步骤来介绍，即：

1.  Cumulative Gain(CG)
2.  Discounted Cumulative Gain(DCG)
3.  Normalized Discounted Cumulative Gain(NDCG)

### **CG**

每一个待推荐的item都有一个相关性的得分，所有相关性得分总和即为CG，对于推荐系统给出的**已排序（按照推荐算法）**序列A，其中**相关性得分（与推荐系统的推荐顺序无关）**和CG分别如下所示：

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/06/214738-406944.png)

### **DCG**

对于推荐系统给出的两个**有序**推荐序列A和B，虽然B的结果比A的要好（按相关性从大到小进行排序），但是按照CG进行评测两者表现效果相同，因此单纯依靠CG存在很大的缺陷

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/06/204813-22694.png)

因此我们提出DCG，希望能够测量推荐系统能否将待推荐的item按照相关性的降序进行排列，其公式如下：
<div>
    $$DCG=\sum_{i=1}^{n} \frac{2^{r e l e v a n c e_{i}}-1}{\log _{2}(i+1)}$$
</div>


或者
<div>
    $$DCG=\sum_{i=1}^{n} \frac{\text {relevance}_{i}}{\log _{2}(i+1)}$$
</div>


其中第一个公式对于具有较高相关性但在推荐序列中排名较后的item具有很大的惩罚，可以作为对于推荐系统排序能力进行测量的工具。对于前面举例子的集合A，B，其DCG的得分如下：

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/06/214715-601173.png)

>   如果相关性用0/1来表示，则两个公式的效果相同



### **NDCG**

由于不同推荐系统对于不同用户给出的待推荐序列的长度不同，相关度的范围不同，因此DCG无法作为一个通用的衡量标准，为了使其通用化，我们引入了NDCG，对于每一个推荐系统生成的序列，其计算方法如下：

1.  按照推荐系统给出的顺序计算DCG
2.  按照相关性排序的顺序计算DCG，带到iDCG
3.  求出两者的比率DCG/iDCG，取值范围应该在0,1之间

举例如下，对于推荐系统给出的序列：

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/06/214645-909101.png)

按照相关性排序可以得到：

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/06/214531-962115.png)

两个序列DCG分别计算如下：

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/06/214548-830768.png)

因此该序列NDCG为：

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202011/06/213029-831561.png)



## **referrence**

[1] [How is hits@k calculated and what does it mean in the context of link prediction in knowledge bases](https://stackoverflow.com/questions/58796367/how-is-hitsk-calculated-and-what-does-it-mean-in-the-context-of-link-prediction)

[2] [Evaluate your Recommendation Engine using NDCG](https://towardsdatascience.com/evaluate-your-recommendation-engine-using-ndcg-759a851452d1)

