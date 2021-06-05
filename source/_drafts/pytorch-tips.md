---
title: pytorch-tips
tags:
---



## detach()的作用

返回一个新的`tensor`，从当前计算图中分离下来的，但是仍指向原变量的存放位置,不同之处只是requires_grad为false，得到的这个`tensor`永远不需要计算其梯度，不具有grad

```python
a = torch.tensor([1,2,3.], requires_grad = True)
out = a.sigmoid()
c = out.detach()
c.zero_()  
# tensor([ 0.,  0.,  0.])
out
# tensor([ 0.,  0.,  0.]),可以通过c可以修改out, 因此c和out指向同一块内存
```

如果是用detach()出来的tensor参与梯度计算，会报错如下

```reStructuredText
RuntimeError: element 0 of tensors does not require grad and does not have a grad_fn
```



## pytorch的乘法

### \* 乘法

#### tensor与标量相乘

```python
>>> a = torch.ones(3,4)
>>> a
tensor([[1., 1., 1., 1.],
        [1., 1., 1., 1.],
        [1., 1., 1., 1.]])
>>> a * 2
tensor([[2., 2., 2., 2.],
        [2., 2., 2., 2.],
        [2., 2., 2., 2.]])
```

#### tensor与一维向量相乘

**要求tensor的列数与一维向量的列数相同**，相当于把一维向量复制成与tensor行数相同的矩阵，再做点乘

```python
>>> a = torch.ones(3,4)
>>> a
tensor([[1., 1., 1., 1.],
        [1., 1., 1., 1.],
        [1., 1., 1., 1.]])
>>> b = torch.Tensor([1,2,3,4])
>>> b
tensor([1., 2., 3., 4.])
>>> a * b
tensor([[1., 2., 3., 4.],
        [1., 2., 3., 4.],
        [1., 2., 3., 4.]])
```

#### tensor与tensor相乘

**要求两个矩阵的维度完全相同**， 实质就是点乘

```
>>> a = torch.tensor([[1, 2], [2, 3]])
>>> a * a
tensor([[1, 4],
        [4, 9]])
```

#### 点积中的broadcast

a是二维Tensor，b是三维Tensor，但是a的维度与b的后两位相同，那么a和b仍然可以做点积，点积结果是一个和b维度一样的三维Tensor，运算规则是：若`c = a * b`, 则`c[i,*,*] = a * b[i, *, *]`

```python
>>> a = torch.tensor([[1, 2], [2, 3]])
>>> b = torch.tensor([[[1,2],[2,3]],[[-1,-2],[-2,-3]]])
>>> a * b
tensor([[[ 1,  4],
         [ 4,  9]],
        [[-1, -4],
         [-4, -9]]])
```



### torch.mul

>   用法与\*相同，同样包含broadcast



### torch.mm

数学中对于维度有要求的矩阵乘法，第一个矩阵的列数与第二个矩阵的行数相同



### torch.matmul

torch.mm的broadcast版本，可以进行高维矩阵的乘法，举例如下：

>   [batch_size, n_memory, dim, dim]*[batch_size, n_memory, dim, 1] = [batch_size, n_memory, dim, 1]



## 损失函数

-   torch.nn.BCELoss()：二分类交叉熵损失函数，需要提前计算sigmoid
-   torch.nn.CrossEntropyLoss()：多分类交叉熵的计算



## tensor维度裁剪

-   torch.squeeze()的用法：将输入张量形状中的1去除并返回。 如果输入是形如(A×1×B×1×C×1×D)，那么输出形状就为： (A×B×C×D)

    >   注意：如果不特殊指明维度，会消除所有值为1的维度，谨慎使用！！！否则会出错

-   torch.unsqueeze(tensor, dim)用于在指定的dim处扩充一个维度，维数是1



## RNN 变长序列

1.  pad_sequence：将一个batch中的数据填充成相同的长度
2.  pack_padded_sequence：消除pad，对序列化数据进行压缩
3.  pad_packed_sequence：将RNN返回的packedSequence转化为RNN的标准输出





## DGL使用

>   Tip1: DGL初始化为一张空图时，不能添加节点和边，可能是bug





## GPU

1.  model移动到gpu之上的代码：

    ```python
    device = torch.device("cuda:0" if torch.cuda.is_available() else "cpu")
    model.to(device)
    ```

    

2.  不同于model，tensor移动到gpu需要进行赋值操作

    ```
    mytensor = my_tensor.to(device)
    ```

    






## reference

[1] [Pytorch的to(device)用法](https://blog.csdn.net/weixin_36670529/article/details/103995898)

