---
title: leetcode刷题笔记
tags:
---



>   在刷leetcode时，我主要是根据题型来刷的，这样有助于对每一种题型加深记忆，同时这也是很多大佬推荐的刷题方法，本篇文章主要记录的是每种题型用到的主要的数据结构及相关的调用方法，每种题型的方法总结



## 栈

### python调用

不同于C++和Java，python没有实现单独的stack，而是通过list来实现

```python
stack = []
# 进栈操作
stack.append(5)
# 出栈操作
num = stack.pop() # num=5
```



### 单调栈

单调栈是栈问题中常考的题目



## 位运算

在进行位运算时，经常用到的子函数是，统计一个数转换成二进制后，其中1的个数，其简单实现为：

```python
def count1(num):
    ones = 0 #1的个数
    while num:
        num &= (num-1)
        ones += 1
    return ones
```

其图示化解释可以参考[这里](https://leetcode-cn.com/problems/number-of-1-bits/solution/wei-1de-ge-shu-by-leetcode/)



## 堆

### python调用

```python
# 堆中的主要算法都在heapq这个包中
import heapq

heap = [5,3,6,4]
# 将数组原地转化成堆，线性时间内完成
heapq.heapify(x)

# 5入堆
heapq.heappush(heap, 5)

# 取最小的元素，让它弹出
num = heapq.heappop(heap)

# 访问最小的元素但不弹出
heap[0]
```

>    Tips: python默认实现的是最小堆，若要改成最大堆，需要通过以下几种方式：
>
>   1.  每次新元素入堆时，对新元素取负
>   2.  将新元素包裹在新的数据结构中，同时实现新的数据结构中得\_\_cmp\_\_( )函数

比较一个元组时，默认先比较第一个元素，若第一个元素相等，则比较第二个元素



## 树

### python 代码

1.  利用栈实现中序遍历

```python
while stack or root:
    while root:
        stack.append(root)
        root = root.left
        root = stack.pop()
        # 这里是需要执行的主要代码
        if root.val <= preNum:
            return False
        #-------------------#
        preNum = root.val
        root = root.right
```



## 队列

### python代码

#### 先入先出队列

```python
import queue

# 先入先出队列
que = queue.Queue()

# 数据入队
que.put(12)

# 数据出队
num = que.get()

# 队列判空
que.empty()
```



#### 优先级队列

```python
# 优先级队列
que = queue.PriorityQueue()

# 优先级队列put的是一个元组，(优先级,数据)，优先级数越小，级别越高
que.put((3,'ddddd'))
```





## 数组

### 滑动窗口

>   通过左右两个下标来维护一个滑动的窗口

相关题型：



## 链表

### 快慢指针

>   类似于滑动窗口                                                                                                                                                                                                                                                                                   