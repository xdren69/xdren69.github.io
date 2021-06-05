---
title: git-tips-1
tags:
---

## 设置忽略的文件和文件夹

在主文件夹下编写`.gitignore`文件

```shell
/.idea/
*/__pycache__
```

>   `.gitignore` 文件只能作用于 Untracked Files，也就是那些从来没有被 Git 记录过的文件（自添加以后，从未 add 及 commit 过的文件）
>   如果文件曾经被 Git 记录过，那么`.gitignore` 就对它们完全无效，即一开始将Files push过了，后来又不想push了

此时的解决办法是：

>   在本地工作区中，将不需要的文件先删除并push到远程仓库，等下次执行文件后，需要git add . 时,发现`__pycache__`文件已经被忽略push了



## git add, git commit添加错文件的撤销操作

[git add ， git commit 添加错文件 撤销](https://blog.csdn.net/kongbaidepao/article/details/52253774)



## git用本地文件强制覆盖远程分支

```shell
git push origin 分支名 --force
```

一般是

```shell
git push origin mian --force
```

现在的默认分支名不再是master

