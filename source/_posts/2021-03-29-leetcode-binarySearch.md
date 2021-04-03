---
title: leetcode刷题笔记--二分查找
date: 2021-03-29 09:38:56
tags:
  - binary search
toc: true
categories:
  - leetcode
excerpt: 本文介绍了二分查找的原理和各种变体
---

>   本篇文章主要记录二分法的使用及其变体



## 二分查找

二分法主要应用于在**有序数组**（其中可以包含相同的元素）中，在log(n)的时间复杂度内查找等于目标值的元素的位置

二分法也有很多变体，主要为：

-   给定一个有序的数组，查找 value 是否在数组中，不存在返回 -1
-   给定一个有序的含有重复元素的数组，查找第一个等于 value 的下标，找不到返回 -1
-   给定一个有序的含有重复元素的数组，查找最后一个等于 value 的下标，找不到返回 -1
-   给定一个有序的数组，查找第一个大于等于 value 的下标，都比 value 小则返回 -1
-   给定一个有序的数组，查找最后一个小于等于 value 的下标，都比 value 小则返回 -1

>   对于后四个问题，整体抽象结果如下：
>
>   当 nums[mid]==value 时，如果下一个搜索的目标段为\[mid+1,right\]，返回right；如果下一个搜索的目标段为\[left, mid-1\]，返回left



## 代码示例

具体代码如下：

### 查找 value 是否在数组中

>   返回的是middle的值

```c++
/* 注意：题目保证数组不为空，且 n 大于等于 1 ，以下问题默认相同 */
int BinarySearch(int array[], int n, int value)
{
    int left = 0;
    int right = n - 1;
    // 如果这里是 int right = n 的话，那么下面有两处地方需要修改，以保证一一对应：
    // 1、下面循环的条件则是 while(left < right)
    // 2、循环内当 array[middle] > value 的时候，right = middle

    while (left <= right)  // 循环条件，适时而变
    {
        int middle = left + ((right - left) >> 1);  // 防止溢出，移位也更高效。同时，每次循环都需要更新。
        if (array[middle] > value)
            right = middle - 1;
        else if (array[middle] < value)
            left = middle + 1;
        else
            return middle;
        // 可能会有读者认为刚开始时就要判断相等，但毕竟数组中不相等的情况更多
        // 如果每次循环都判断一下是否相等，将耗费时间
    }
  
    return -1;
}
```



### 查找第一个等于 value 的下标

>   返回left下标对应的值

```c++
int BinarySearch(int array[], int n, int value)
{
    int left = 0;
    int right = n - 1;

    while (left <= right)
    {
        int middle = left + ((right - left) >> 1);

        if (array[middle] >= value)  // 因为是找到最小的等值下标，所以等号放在这里
            right = middle - 1;
        else
            left = middle + 1;
    }

    if (left < n && array[left] == value)
        return left;

    return -1;
}
```



### 查找最后一个等于 value 的下标

对上一种方法做出如下两个改动：

1.  修改中间判断语句

    ```C++
    if (array[middle] > value) //去掉等号
    ```

    

2.  修改最后的返回语句，返回right的值

    ```C++
    if (right >= 0 && array[right] == value) //第二个判断条件是为了判断
        return right;
    ```

    



### 查找第一个大于等于 value 的下标

```c++
int BinarySearch(int array[], int n, int value)
{
    int left = 0;
    int right = n - 1;

    while (left <= right)  
    {
        int middle = left + ((right - left) >> 1);
      
        if (array[middle] >= value)
            right = middle - 1;
        else
            left = middle + 1;
    }
    
    return (left < n) ? left : -1;
}
```



### 查找最后一个小于等于 value 的下标

对上一种方法做出如下两个改动：

1.  修改判断语句

    ```c++
    if (array[middle] > value) //去掉等号
    ```

    

2.  修改返回值

    ```c++
    return (left < n) ? left : -1 
    //改为 
    return (right >= 0) ? right : -1
    ```

    

## 引用

[1] [二分查找](https://ethsonliu.com/2018/04/binary-search.html)

