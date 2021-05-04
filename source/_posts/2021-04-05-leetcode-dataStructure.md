---
title: leetcode刷题笔记-常用数据结构
tags:
  - Data Structure
toc: true
categories:
  - leetcode
excerpt: 本文记录了leetcode中的一些常用数据结构，以及相应的Python调用方式
date: 2021-04-05 21:55:22
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



## 树

### 利用栈实现中序遍历

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



## 队列

### 先入先出队列

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

注意：

队列的判空不能用：

```python
while que:
  pass
```



### 优先级队列

```python
# 优先级队列
que = queue.PriorityQueue()

# 优先级队列put的是一个元组，(优先级,数据)，优先级数越小，级别越高
que.put((3,'ddddd'))
order, string = que.get()
```



>   Tips：优先级队列 VS 堆
>
>   1.  原理：PriorityQueue使用的就是heapq来实现的，所以可以认为两者算法本质上是一样的。当然PriorityQueue考虑到了线程安全的问题。
>   2.  使用：通过heapq可以访问小顶堆的堆顶元素，而不弹出该元素；`queue.PriorityQueue()`一旦访问队首元素就必须弹出



## 字符串

注：Python的字符串不能通过下标修改，即进行如下操作时：

```python
string = 'abcdafg'
string[4] = 'e'
```

会出现报错：

```python
TypeError: 'str' object does not support item assignment
```



## 数组

### 滑动窗口（双指针）

>   滑动窗口的原理：如果我们依次递增地枚举子串的起始位置，那么子串的结束位置也是递增的（或者递减的）



## 链表

### 快慢指针

>   存在以下两种使用情况：
>
>   1.  快指针每次走两步，慢指针每次走一步   
>   2.  快指针比慢指针先走k步                                                                                                                                                                                                                                                                               