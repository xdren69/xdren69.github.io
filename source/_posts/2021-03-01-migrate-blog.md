---
title: Hexo博客迁移记录
date: 2021-03-01 21:36:11
toc: true
categories:
- 博客搭建
excerpt: 本篇的目标是，将原计算机A上搭建好的blog的内容，迁移到另一台新的计算机B上，使得用户可以分别在A，B实现blog的撰写和发布，同时A，B间的内容也能同步
---

## 起因

原本blog是暑假搭建在我的笔记本上的，但是上学后更多的用的是实验室的台式机，笔记本太重了经常放在寝室，导致我上学期的后半段几乎没有跟新博客（又在给自己找理由了hhh）。趁着刚开学有空，就想将原本笔记本上的博客内容迁移到实验室的主机上来，实现笔记本和实验室主机都能写博客，同时还能相互间同步。说道免费同步，当然是利用github~

于是诞生了这篇围绕如何利用github实现多终端都可以撰写博客的博文



## 方法

### 在笔记本上的操作（原电脑）

1.  给 username.github.io博客仓库创建hexo分支，并设为默认分支

2.  随便一个目录下，命令行执行 git clone git@github.com:username/username.github.io.git 把仓库 clone 到本地（默认是hexo分支）

3.  显示所有隐藏文件和文件夹，进入刚才 clone 到本地的仓库，删掉除了 .git 文件夹以外的所有内容

4.  命令行 cd 到 clone 的仓库，执行如下命令：

    ```shell
    git add -A
    git commit -m "--"
    git push origin hexo
    ```

    把刚才删除操作引起的本地仓库变化更新到远程，此时刷新下 github 端博客hexo分支，应该已经被清空了

5.  将上述 .git 文件夹复制到本机本地博客根目录下（即含有 themes、source 等文件夹的那个目录），现在可以把上述 clone 的本地仓库删掉了，因为它已经没有用了，本机博客目录已经变成可以和 hexo 分支相连的仓库了

6.  将博客目录下 themes 文件夹下每个主题文件夹里面的 .git .gitignore 删掉

7.  cd 到博客目录，执行如下命令

    ```shell
    git add -A
    git commit -m "--"
    git push origin hexo
    ```

    将博客目录下所有文件更新到 hexo 分支。如果上一步没有删掉 .git .gitignore，主题文件夹下内容将传不上去。



### 在实验室主机上的操作（新电脑）

1.  在新电脑上操作，先把新电脑上环境安装好，node.js、git、hexo，ssh key 也创建和添加好

2.  选好博客安装的目录， 执行

    ```shell
    git clone git@github.com:username/username.github.io.git
    ```

3.   cd 到博客目录，npm install、hexo g && hexo s，安装依赖，生成和启动博客服务。正常的话，浏览器打开 localhost:4000 可以看到博客了。至此新电脑操作完毕



### 同步方法

1.  上传

    ```shell
    git add -A
    git commit –m "xx"
    git push 
    ```

2.  在已经执行过git clone的电脑上进行下载

    ```shell
    git pull
    ```

    

## 引用

[1] [使用hexo，如果换了电脑怎么更新博客？](https://www.zhihu.com/question/21193762)

