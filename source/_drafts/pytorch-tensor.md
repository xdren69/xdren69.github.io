---
title: pytorch中tensor的属性
tags:
---

## 张量初始化

1.  全0：
2.  全1：
3.  从正态分布随机采样
4.  从python的List中获得
5.  range()



## 获取张量形状





## 张量运算

### 按元素运算

>   对于任意具有相同形状的张量，常见的标准算术运算符（`+`、`-`、`*`、`/` 和 `**`）都可以被升级为按元素运算

```python
x = torch.tensor([1.0, 2, 4, 8])
y = torch.tensor([2, 2, 2, 2])
x + y, x - y, x * y, x / y, x**y  # **运算符是求幂运算
```

还有相应的指数运算

```python
torch.exp(x)
```





## 张量拼接

### cat

>   只会沿着原有维度进行拼接，不会产生新的维度

```python
X = torch.arange(12, dtype=torch.float32).reshape((3, 4))
Y = torch.tensor([[2.0, 1, 4, 3], [1, 2, 3, 4], [4, 3, 2, 1]])
torch.cat((X, Y), dim=0) # (6,4)
torch.cat((X, Y), dim=1) # (3,8)
```



## 与numpy的转化

>   不会共享内存

tensor转化为numpy

```python
X = torch.arange(12, dtype=torch.float32).reshape((3, 4))
A = X.numpy()
B = torch.tensor(A)
type(A), type(B)
```

numpy转化为tensor

```python
np_data = np.arange(6).reshape((2, 3))
torch_data = torch.from_numpy(np_data)
```



## 沿某个轴汇总

### 累计和

>   从指定轴的第0个元素累加到当前元素得到的总和

```python
A = torch.arange(20, dtype=torch.float32).reshape(5, 4)
print(A)
B = A.cumsum(axis=0)
print(B)
```

```python
tensor([[ 0.,  1.,  2.,  3.],
        [ 4.,  5.,  6.,  7.],
        [ 8.,  9., 10., 11.],
        [12., 13., 14., 15.],
        [16., 17., 18., 19.]])
tensor([[ 0.,  1.,  2.,  3.],
        [ 4.,  6.,  8., 10.],
        [12., 15., 18., 21.],
        [24., 28., 32., 36.],
        [40., 45., 50., 55.]])
```

