---
title: rl_intro
tags:
---

## terms

-   environment: 
-   agent: 学习让reward可以被max的action
-   state (agent's observation): 整个environment中所有的state，可能包含agent看不见的
-   reward: environment基于agent的action给予agent的
-   episode: 从游戏开始到结束的整个阶段



## difficult

-   reward很稀疏，导致需要很多training examples——用两个agent相互训练
-   reward delay, 在射击游戏中只有fire才能得到reward，move无法得到reward



## special

supervised learning VS Reinforcement learning（以围棋为例）

-   supervised learning: 告诉计算机每一步该怎么走
-   Reinforcement learning: 不告诉每一步怎么走，而是告诉最终结果



## Markov Decision Process





>   目前流行A3C，deep Q network已经不值一提

## A3C

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202012/06/133458-722585.png)

value-based先流行，policy-based最近才发展

### policy-based

>   learning a actor/policy, 即一个函数pi使得Action = pi (Observation)

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202012/06/134307-778615.png)





## 后续资料

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202012/06/133707-147952.png)