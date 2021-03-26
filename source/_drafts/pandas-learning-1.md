---
title: pandas-learning-1
tags:
---

```python
import pandas as pd

data = pd.read_csv(data_file)
print(data)
```



## 位置索引

### iloc

```python
inputs, outputs = data.iloc[:, 0:2], data.iloc[:, 2]
inputs = inputs.fillna(inputs.mean())
print(inputs)
```



## 处理缺失值

用当前列的平均值来填充当前列的NA值

```python
inputs, outputs = data.iloc[:, 0:2], data.iloc[:, 2]
inputs = inputs.fillna(inputs.mean())
print(inputs)
```

```t
   NumRooms Alley
0       3.0  Pave
1       2.0   NaN
2       4.0   NaN
3       3.0   NaN
```



## 处理离散值

将离散值转换为one-hot向量

```python
inputs = pd.get_dummies(inputs, dummy_na=True)
print(inputs)
```

```
   NumRooms  Alley_Pave  Alley_nan
0       3.0           1          0
1       2.0           0          1
2       4.0           0          1
3       3.0           0          1
```



## 转化为张量格式

```python
import torch
X, y = torch.tensor(inputs.values), torch.tensor(outputs.values)
# inputs.values为numpy.narray()
```

