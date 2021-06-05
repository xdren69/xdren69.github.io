---
title: leetcode--Python常用语法
tags:
---

## 使用with打开多个文件

```python
with open(filename1, 'rb') as fp1, open(filename2, 'rb') as fp2, open(filename3, 'rb') as fp3:
    for i in fp1:
        pass
```



## yield 函数（生成器）





## 列表生成器



### 双重循环

```python
nested_list = [[0, 1], [2, 3], [4, 5], [6, 7], [8, 9]]
result = [x for sublist in nested_list for x in sublist if x % 3 == 0]
```

### 搭配lambda

```python
tmp = [1, 2, 3]
a = [(lambda x: x*5)(i) for i in tmp]
print(a)
```

