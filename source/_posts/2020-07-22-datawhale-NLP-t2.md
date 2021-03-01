---
title: datawhale-零基础入门NLP-Task2
tags:
  - datawhale
  - NLP
toc: true
categories:
  - Datawhale学习手记
excerpt: 这是一篇关于参与datawhale的数据比赛的组织活动的第二篇博客记录，主要记录的是Task2-数据读取与数据分析部分的完成情况
date: 2020-07-22 21:53:42
---


### 学习目标

对需要训练的数据进行分析，同时通过可视化了解待训练数据的特点

>    前半部分的代码是官方文档提供的；作业部分属于自己完成的



### 数据分析

#### 读入并观察数据的格式

```python
import pandas as pd

train_df = pd.read_csv('../data/train_set.csv', sep='\t')
train_df.head()
```

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202007/22/213030-379943.png)



#### 分析每段文本的长度

```python
%pylab inline

train_df['text_len'] = train_df['text'].apply(lambda x: len(x.split(' ')))
print(train_df.head())
print(train_df['text_len'].describe())
```

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202007/22/213247-384972.png)

>   由此我们可以看到，对于所有文本，平均长度为907，最短为2，最长为57921



#### 文本长度可视化分析

```python
_ = plt.hist(train_df['text_len'], bins=200)
plt.xlabel('Text char count')
plt.title("Histogram of char count")
```

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202007/22/220003-209612.png)

>   由此我们可以看出文本长度的分布是不均匀的



#### 文本类别可视化分析

```python
train_df['label'].value_counts().plot(kind='bar')
plt.title('News class count')
plt.xlabel("category")
```

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202007/22/213758-980152.png)

>   由此我们可以看出，不同类别的文本数也是不同的，可能对模型的训练精度产生影响



#### 单词频次分析

**先展示错误代码**

```python
from collections import Counter
all_lines = ' '.join(list(train_df['text']))
word_count = Counter(all_lines.split(" "))
word_count = sorted(word_count.items(), key=lambda d:d[1], reverse = True)

print(len(word_count))
print(word_count[0])
print(word_count[-1])
```

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202007/22/214338-964834.png)

>   错误原因：在执行到第三行时会因内存占用太多而导致程序奔溃；
>
>   解决办法：将训练数据分批读入，而不是一次性全部读入

**再展示正确代码**

```python
from collections import Counter

word_count = Counter()
chunk_iterator = pd.read_csv('../data/train_set.csv',sep='\t', chunksize=10000) 
for chunk in chunk_iterator:
    all_lines = ' '.join(list(chunk['text']))
    word_count.update(all_lines.split(" "))
word_count = sorted(word_count.items(), key=lambda d:d[1], reverse = True)

print(len(word_count))
print(word_count[0])
print(word_count[-1])
```

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202007/22/214656-643831.png)

>   表明一共出现了6869个汉字，其中3750号汉字出现的次数最多，为7482224次；3133号汉字出现的次数最少，为1次

统计覆盖率最高的前三个单词

```python
from collections import Counter
# 统计每个句子中出现的不同的单词，为每个句子构造一个单词集
train_df['text_unique'] = train_df['text'].apply(lambda x: ' '.join(list(set(x.split(' ')))))
# 将所有句子的单词集连接成文本
all_lines = ' '.join(list(train_df['text_unique']))
# 统计每个单词在单词集中出现的次数
word_count = Counter(all_lines.split(" "))
# 对统计结果进行排序
word_count = sorted(word_count.items(), key=lambda d:int(d[1]), reverse = True)
# 展示统计结果前三名，含义是每个单词在多少个句子中出现过

print(word_count[0])
print(word_count[1])
print(word_count[2])
```

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202007/22/220020-402600.png)

>   表明：编号'3750'的单词出现在了197997个句子中；编号'900'的单词出现在了197653个句子中；编号'648'的单词出现在了191975个句子中



### 本章作业

1.  假设字符3750，字符900和字符648是句子的标点符号，请分析赛题每篇新闻平均由多少个句子构成？

    ```python
    # 可以直接套用前一段代码，统计这三个标点在所有文本中出现的总次数即可
    from collections import Counter
    
    word_count = Counter()
    chunk_iterator = pd.read_csv('../data/train_set.csv',sep='\t', chunksize=10000) 
    for chunk in chunk_iterator:
        all_lines = ' '.join(list(chunk['text']))
        word_count.update(all_lines.split(" "))
    # 计算出总次数
    sum = word_count['3750']+word_count['900']+word_count['648']
    print(sum)
    # 计算出平均次数
    print(sum/200000.0)
    ```

    ![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202007/22/215036-35507.png)

2.  统计每类新闻中出现次数对多的字符

    ```python
    chunk_iterator = pd.read_csv('../data/train_set.csv',sep='\t', chunksize=10000) 
    for label in range(14):
        word_count = Counter()
        all_lines = ''
        for chunk in chunk_iterator:
            for index, l in enumerate(list(train_df['label'])):
                if l==label:
                    all_lines = all_lines+' '+ train_df['text'][index]
            word_count.update(all_lines.split(" "))
        word_count = sorted(word_count.items(), key=lambda d:int(d[1]), reverse = True)
        print(label,word_count[0])
    ```



### 引用

[1] [提高python处理数据的效率方法](https://www.cnblogs.com/triple-y/p/11580307.html)

[2] [Datawhale零基础入门NLP赛事 - Task2 数据读取与数据分析](https://tianchi.aliyun.com/notebook-ai/detail?spm=5176.12586969.1002.9.64063dadKkxAzT&postId=118253)

