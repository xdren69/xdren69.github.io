---
title: NLG
toc: true
categories:
- CS224n
tags:
- NLP
excerpt: 这是一篇关于NLP中语言模型(NLG)的学习笔记，是CS224n-2019中lec15的学习笔记
---

### NLG
> NLG模型是指在给出前n个已知词语后，预测第n+1个词语时所采用的模型

>> RNN模型训练时为了提升效率，采用teacher forcing的方法

对于seq2seq模型，分为encoder和decoder两部分,其中decoder部分的算法分为：
- greedy decoding:softmax
- beam search:beam size
beam size过小就会趋于，greedy decoding，从而会导致很多不合理的生成；而beam size过大会导致：
in NMT, increasing beam size will decrease BLUE score
in chit-chat dialogue, ...

#### 新的decoding方法，即sampling-based decoding
- pure sampling
- top-n sampling
technic:
Softmax temperature: include a temperature hyperparameter\\( \tau \\), a method to change the probability attribution
\\( P_{t}(w)=\frac{\exp \left(s_{w} / \tau\right)}{\sum_{w^{\prime} \in V} \exp \left(s_{w^{\prime}} / \tau\right)} \\)

### Text summary
two main strategy:
- Extractive summarization(highlight):Select parts (typically sentences) of the original text to form a summary.
- Abstractive summarization(pen):Generate new text using natural language generation techniques.——using NLG techics, write from the scratch

#### the progress of pre-neural summarization:
1. Content selection
2. Information ordering
3. Sentence realization

#### summarization evaluation:ROUGE
> first of all, to explain the precision and recall
    - *type I error*:predict wrong as right
    - *type II error*:predict right as wrong
> precision:True positive/Actual results,
> recall:True positive/Predicted results,recall refers to the percentage of total relevant results correctly classified by your algorithm——from [here](https://towardsdatascience.com/precision-vs-recall-386cf9f89488)

#### neural summarization
