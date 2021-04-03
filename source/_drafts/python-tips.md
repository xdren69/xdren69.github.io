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



