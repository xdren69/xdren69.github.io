---
title: python-tips
tags:
---

## 使用with打开多个文件

```python
with open(filename1, 'rb') as fp1, open(filename2, 'rb') as fp2, open(filename3, 'rb') as fp3:
    for i in fp1:
        pass
```



## python常用内置函数

### reduce

函数将一个数据集合（链表，元组等）中的所有数据进行下列操作：用传给 reduce 中的函数 function（有两个参数）先对集合中的第 1、2 个元素进行操作，得到的结果再与第三个数据用 function 函数运算，最后得到一个结果。

举例如下：对list中的元素求和

```python
sum = reduce(lambda x, y: x+y, [1,2,3,4,5])
```



## python嵌套函数

-   作用：能够保证内部函数的隐私，内部的函数只能被外部函数所调用和访问，不会暴露在全局作用域
-   内层函数访问外层函数中局部变量的方法：
    -   不需要修改外层函数中的局部变量：直接访问即可
    -   需要修改外层函数中的局部变量：通过关键字`nonlocal`，注意不能使用`global`，global会到外层函数之外的存储区寻找相应变量

代码示例：

```python
### 全局变量 ###
MIN = 1
MAX = 8
###
print(f"MIN={MIN},MAX={MAX}")
def f1():
    global MIN # 使用global关键字 修改全局变量
    MIN += 1
    a = 5  # 局部变量
    b = 1
    print(f"MIN={MIN},a={a},b={b}")
    def f2():
        MAX = 9 # 局部变量与全局变量同名，局部变量会覆盖全局变量
        global MIN # 访问并修改全局变量
        MIN += 1
        nonlocal a # 使用nonlocal关键字 修改外部函数的变量
        a += 1
        b = 2 # 内部函数变量与外部函数变量同名，覆盖
        print(f"MIN={MIN},MAX={MAX},a={a},b={b}")
    f2()  # 内部函数被外部函数调用

f1()
print(f"MIN={MIN},MAX={MAX}")
```



## collections模块

### Counter

>   是一个简单的计数器，例如，可以用来统计字符出现的个数

```python
from collections import Counter

tasks = ["A","A","A","B","B","B"]
freq = Counter(tasks)
print(freq)
print(freq.values())
print(freq.keys())

# Counter({'A': 3, 'B': 3})
# dict_values([3, 3])
# dict_keys(['A', 'B'])
```

### defaultdict

>   使用dict时，如果引用的Key不存在，就会抛出KeyError。如果希望key不存在时，返回一个默认值，就可以用defaultdict
>
>   注意默认值是调用函数返回的，而函数在创建defaultdict对象时传入。

```python
from collections import defaultdict

a = defaultdict(list)
# 直接使用即可，默认a[1]已经存在一个空的list
a[1].append('a') 
```







## 常用数学计算

### 开方

```python
import math
a = math.sqrt(16)
```

### 乘方

```python
# 方法一
res = pow(4,3)
# 方法二
res = 4**3
```

### 最大数与最小数

```python
import sys
max_num = sys.maxsize
min_num = -(sys.maxsize-1)
```



## 常见STL



## 循环变量

-   python的循环变量在循环之外仍旧可以使用，例如

    ```python
    
    ```



## 字符串

-   对字符串进行重新排序

    ```python
    a = "dbca"
    b = "".join(sorted(a))
    print(b)
    # "abcd"
    ```

-   字符与ASCII的转化

    ```python
    ord('a')
    ```

    

## List

Python中list可以通过“==”来判断相等，但是里面相同元素的排列顺序必须相同

```python
a = [1,2,3]
b = [1,2,3]
c = [2,1,3]
a == b # True
a == c # False
```





## 参考

[1] [一文搞懂Python函数（匿名函数、嵌套函数、闭包、装饰器）！](https://bbs.huaweicloud.com/blogs/183686)

[2] []()



