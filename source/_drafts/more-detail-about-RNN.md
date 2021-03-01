---
title: more-detail-about-RNN
toc: true
categories:
- CS224n
tags:
- NLP
excerpt: 这是一篇关于NLP中语言模型(NLG)的学习笔记，是CS224n-2019中lec15的学习笔记
---

#### problem: gradient vanish

Gradient signal from faraway is lost because it’s much smaller than gradient signal from close-by. So model weights are only updated only with respect to near effects, not long-term effects.

> 梯度消失的原因：
>
> 1. There’s no dependency between step t and t+n in the data
>
> 2. We have wrong parameters to capture the true dependency between t and t+n——it’s too difficult for the RNN to learn to preserve information over many timesteps.

**solution:**

1. LSTM:
   
   - On step t, there is a hidden state h(t) and a cell state c(t)——n vector
   
   - We have a sequence of inputs , and we will compute a sequence of hidden states h(t) and cell states c(t).
   
   - The selection of which information is erased/written/read is controlled by
     three corresponding gates——n vector
   
   **the inner work**
   
   ![](D:\AppData\Roaming\marktext\images\2020-07-01-09-46-01-image.png)
   
   h(t) is the content that should be output

2. Gated Recurrent Units (GRU)
   
   ![](D:\AppData\Roaming\marktext\images\2020-07-01-10-17-29-image.png)
   
   when set **u**=0, we can keep information for a long time

3. diff between above two:
   
   - LSTM is a good default choice (especially if your data has particularly long dependencies, or you have lots of training data)
   
   - Rule of thumb: start with LSTM, but switch to GRU if you want something more efficient——faster

#### problem: gradient exploding

**solution:**

1. Gradient clipping: if the norm of the gradient is greater than some threshold, scale it down before applying SGD update

#### solution in DNN and RNN:

1. Residual connections aka “ResNet”——skip-connections

2. Dense connections aka “DenseNet”, Directly connect everything to everything

3. Highway connections aka “HighwayNet”, Similar to residual connections, but the identity connection vs the transformation layer is controlled by a dynamic gate

#### More advanced type of RNN

1. Bidirectional RNNs——only applicable if you have access to the entire input sequence.
   
   ![](D:\AppData\Roaming\marktext\images\2020-07-01-10-53-43-image.png)

2. Multi-layer RNNs, aka ''stack RNNs''
   
   - for Neural Machine Translation, 2 to 4 layers is best for the encoder RNN, and 4 layers is best for the decoder RNN
   
   - skip-connections/dense-connections are needed to train deeper RNNs (e.g. 8 layers)
   
   - Pytorch 的处理顺序: it will do all of RNN in layer one, than layer two...