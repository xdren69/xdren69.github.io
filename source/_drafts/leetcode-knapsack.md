---
title: leetcode刷题笔记--背包问题
tags:

---

背包问题属于**动态规划**问题，主要是要确定题目中的状态转移方程以及状态存储矩阵



## 0-1背包问题

>   假设N件物品，每件物品重量为w[i]，每件物品的价值为v[i]，背包重量为C，选择物品装入背包使其在不超过负重的前提下价值最大，每种物品只能用一次。

状态存储矩阵：假设`dp[i][j]`表示在前i件物品，背包重量为j的前提下的最大价值。

状态转移方程：`dp[i][j]=max( dp[i-1][j]，dp[i-1][j-w[i]]+v[i] ) `

leetcode 题目集：[416.分割等和子集](https://leetcode-cn.com/problems/partition-equal-subset-sum/)



## 完全背包问题

>   与0-1背包不同的一点在于对于每个物品，不是取(1)或者不取(0)两种状态，而是可以取0,1，2…任意次

状态存储矩阵：假设`dp[i][j]`表示在前i件物品，背包重量为j的前提下的最大价值。

状态转移方程：`dp[i] [j]=max(dp[i-1] [j-k×w[i]]+k×v[i])，其中 0 ≤ k×w[i] ≤ j `

leetcode 题目集：[322.零钱兑换](https://leetcode-cn.com/problems/coin-change/)



## 参考

[1] [0-1背包和完全背包问题](https://blog.csdn.net/HangHug_L/article/details/108491601)