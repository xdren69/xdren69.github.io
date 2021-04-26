---
title: leetcode刷题笔记-Python常用语法
date: 2021-03-31 13:19:43
tags:
  - Python
toc: true
categories:
  - leetcode
excerpt: 本文介绍了leetcode中常用的一些语法和坑
---

## python常用内置函数

### reduce

>   **reduce()** 将一个数据集合（链表，元组等）中的所有数据进行下列操作：用传给 reduce 中的函数 function（有两个参数）先对集合中的第 1、2 个元素进行操作，得到的结果再与第三个数据用 function 函数运算，最后得到一个结果。

举例如下：对list中的元素求和

```python
sum = reduce(lambda x, y: x+y, [1,2,3,4,5])
```

### zip

>   **zip()** 函数用于将可迭代的对象作为参数，将对象中对应的元素打包成一个个元组，然后返回由这些元组组成的列表。

1.  如果各个迭代器的元素个数不一致，则返回列表长度与最短的对象相同。
2.  利用 * 号操作符，可以将元组解压为列表。

```python
>>> a = [1,2,3]
>>> b = [4,5,6]
>>> c = [4,5,6,7,8]
>>> zipped = zip(a,b)     # 打包为元组的列表
[(1, 4), (2, 5), (3, 6)]
>>> zip(a,c)              # 元素个数与最短的列表一致
[(1, 4), (2, 5), (3, 6)]
>>> zip(*zipped)          # 与 zip 相反，*zipped 可理解为解压，返回二维矩阵式
[(1, 2, 3), (4, 5, 6)]
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

**Tips**：

>   nonlocal只能在函数内的函数中使用，如果直接在全局作用域下定义的函数中使用该语句，会报错！



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



## 循环

-   python的循环变量在循环之外仍旧可以使用，例如

    ```python
    for i in range(5):
        continue
    print(i) # 4
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

-   字符串化为十进制

    ```python
    int('123')
    ```



## List

### list之间的比较

Python中list可以通过“==”来判断相等，但是里面相同元素的排列顺序必须相同

```python
a = [1,2,3]
b = [1,2,3]
c = [2,1,3]
a == b # True
a == c # False
```

### range() 和list之间的转换

```python
a = range(100)
# 方式一：
list_1 = [*a]

# 方式二：
list_2 = list(a)
```

### 排序 sort vs. sorted

>   1.  *sort 是应用在 list 上的方法，sorted 可以对所有可迭代的对象进行排序操作*
>
>   2.  *list 的 sort 方法返回的是对已经存在的列表进行操作，而内建函数 sorted 方法返回的是一个新的 list，而不是在原来的基础上进行的操作。*

```python
>>>a=[5,2,3,1,4]
>>> a.sort()
>>> a
[1,2,3,4,5]
>>>sorted([5, 2, 3, 1, 4])
[1, 2, 3, 4, 5]   
```



## 进制与位运算

### 进制表示

-   二进制：

    ```python
    >>> bin(5) 
    '0b101' # 字符串类型
    >>> abin = 0b1000
    >>> abin
    8
    ```

-   八进制：

    ```python
    >>> oct(11)
    '013'
    >>> aoct = 0o123 （数字0，字母o）
    >>> aoct
    83
    ```

-   十六进制：

    ```python
    >>> hex(1033)
    '0x409'
    >>> ahex = 0xAB
    >>> ahex
    171
    ```

    

### 位运算

-   &（按位与运算符）：参与运算的两个值,如果两个相应位都为1,则该位的结果为1,否则为0 (a & b) 输出结果 12 ，二进制解释： 0000 1100
-   |（按位或运算符）：只要对应的二个二进位有一个为1时，结果位就为1。 (a | b) 输出结果 61 ，二进制解释： 0011 1101
-   ^（按位异或运算符）：当两对应的二进位相异时，结果为1 (a ^ b) 输出结果 49 ，二进制解释： 0011 0001
-   ~（按位取反运算符）：对数据的每个二进制位取反,即把1变为0,把0变为1 。~x 类似于 -x-1 (~a ) 输出结果 -61 ，二进制解释： 1100 0011，在一个有符号二进制数的补码形式。
-   <<（左移动运算符）：运算数的各二进位全部左移若干位，由 << 右边的数字指定了移动的位数，高位丢弃，低位补0。 a << 2 输出结果 240 ，二进制解释： 1111 0000
-   \>>（右移动运算符）：把">>"左边的运算数的各二进位全部右移若干位，>> 右边的数字指定了移动的位数 a >> 2 输出结果 15 ，二进制解释： 0000 1111



## 自定义类

-   在编程中经常会遇到对自定义对象的排序，堆维护，优先级队列维护的问题，这就需要数据结构中的元素可比较大小，由于heapq或者list.sort()都只使用小于<比较，所以只需要定义类的\_\_lt\_\_()方法即可

举例如下：

```python
class Node:
    def __init__(self, x):
        self.val = x
        # 0.left;1.right
        self.flag  = -1
        self.father = None

    def __lt__(self, other):
        return self.val < other.val
```



## 格式化输出

### `print()`输出之后不换行

```python
a = 5
print(a, end='')
```

### %用法

#### 1.浮点数输出

%f ——保留**小数点后面六位有效数字**

> eg：%.3f，保留3位小数位

%e ——保留**小数点后面六位有效数字**，科学计数法输出
> eg：%.3e，保留3位小数位，使用科学计数法

%g ——在保证**六位有效数字**的前提下，如果小数点前的数字个数小于六个，使用小数方式；否则使用科学计数法

>   eg：%.3g，保留3位有效数字，使用小数或科学计数法

```python
>>> print('%f' % 1.11)  # 默认保留6位小数
1.110000
>>> print('%.1f' % 1.11)  # 取1位小数
1.1
>>> print('%e' % 1.11)  # 默认6位小数，用科学计数法
1.110000e+00
>>> print('%.3e' % 1.11)  # 取3位小数，用科学计数法
1.110e+00
>>> print('%g' % 1111.1111)  # 默认6位有效数字
1111.11
>>> print('%.7g' % 1111.1111)  # 取7位有效数字
1111.111
>>> print('%.2g' % 1111.1111)  # 取2位有效数字，自动转换为科学计数法
1.1e+03
```



#### 2.字符串输出

%s
%10s——右对齐，占位符10位
%-10s——左对齐，占位符10位
%.2s——截取2位字符串
%10.2s——10位占位符，截取两位字符串

```python
>>> print('%s' % 'hello world')  # 字符串输出
hello world
>>> print('%20s' % 'hello world')  # 右对齐，取20位，不够则补位
         hello world
>>> print('%-20s' % 'hello world')  # 左对齐，取20位，不够则补位
hello world
>>> print('%.2s' % 'hello world')  # 取2位
he
>>> print('%10.2s' % 'hello world')  # 右对齐，取2位
        he
>>> print('%-10.2s' % 'hello world')  # 左对齐，取2位
he
```



### format用法

暂无



## 参考

[1] [一文搞懂Python函数（匿名函数、嵌套函数、闭包、装饰器）！](https://bbs.huaweicloud.com/blogs/183686)

[2] [python随笔系列--global和nonlocal关键字](https://blog.51cto.com/kaifly/2358266)

[3] [python基础_格式化输出（%用法和format用法）](https://www.cnblogs.com/fat39/p/7159881.html)