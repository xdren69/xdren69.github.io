---
title: ubuntu安装cuda和cudnn指南
date: 2020-10-03 10:21:50
toc: true
categories:
- 环境配置
tags:
- cuda
excerpt: 这是一篇关于在16.04和18.04ubuntu系统下配置cuda10.1,conda和cudnn的记录博客
---

## cuda，cudnn之间的关系



## cuda安装

### 安装驱动的两种方式

1.  在安装cuda时使用cuda默认的驱动，这时产生的问题是cuda默认的驱动一般不是官网上最新的驱动，在使用较新的显卡时（比如我们这回使用的3090），会导致显卡与驱动不适配的情况。
2.  将驱动与cuda分开安装，先安装相应的驱动，再安装相应的cuda



### 版本检查

本文采用cuda和驱动分开安装，具体流程如下：

1.  安装前先在[NVIDIA官网](https://www.nvidia.cn/Download/Find.aspx?lang=cn)查看Nvidia显卡的型号和驱动的对应情况:

2.  然后在[cuda官网](https://docs.nvidia.com/cuda/cuda-toolkit-release-notes/index.html)查看cuda版本和驱动的对应情况：

    >   从这里可以看出cuda对于驱动的版本是向上兼容的！所以我们优先满足第1步中显卡对应的驱动即可！

    ![cuda版本与驱动的对应关系](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202103/17/181742-129705.png)

3.  然后开始正式安装



### 正式安装

1.  （第一次安装时需要）禁用ubuntu系统自带的nouveau驱动：

    ```shell
    sudo gedit /etc/modprobe.d/blacklist.conf
    ```

    在文件末尾添加：

    ```shell
    blacklist nouveau
    ```

    然后执行如下命令跟新系统驱动：

    ```shell
    sudo update-initramfs -u
    ```

    重启系统：

    ```shell
    sudo reboot now
    ```

    检查是否禁用成功

    ```shell
    lsmod | grep nouveau
    ```

    若没有输出则代表禁用成功

    

2.  关闭图形界面：

    首先按``CTRL+ALT+F1``（1~6均为命令行界面，7为图形界面）进入命令行界面

    然后执行如下命令关闭图形界面：

    ```shell
    sudo service lightdm stop
    ```

    

3.  安装驱动：

    注意要带参数``--no-opengl-files``，否则会出现，安装后重启，输入用户名和密码，无法进入桌面，会一直让你输入用户名和密码的情况，网上把这种情况称为***循环登录***。

    完整命令如下：

    ```shell
    sudo sh ./NVIDIA-Linux-x86_64-***.***.run --no-opengl-files
    ```

    检测安装成果：输入如下指令

    ```shell
    nvidia-smi
    ```

    若显示显卡则代表安装成功

    

4.  安装cuda，因为我们已经安装好了驱动，因此在安装cuda时不再勾选安装驱动

    

5.  打开图形界面：

    ```shell
    sudo service lightdm start
    ```




### 卸载NVIDIA驱动

首先执行官方推荐的卸载

```
sudo /usr/bin/nvidia-uninstall
```

然后卸载相关的包

```
sudo apt-get --purge remove nvidia*
sudo apt autoremove
```



### 卸载cuda

首先执行官方推荐的卸载

```
sudo /usr/bin/cuda-uninstall
```

然后卸载相关的包

```
sudo apt-get --purge remove "*cublas*" "cuda*"
sudo apt autoremove
```





## 重装驱动

>   有时候驱动会莫名其妙消失，这时候只需要重装驱动即可！具体步骤如下：



### 卸载原始NVIDIA驱动

首先执行官方推荐的卸载

```
sudo /usr/bin/nvidia-uninstall
```

然后卸载相关的包

```
sudo apt-get --purge remove nvidia*
sudo apt autoremove
```



### 重装驱动

1.  关闭图形界面：

    首先按``CTRL+ALT+F1``（1~6均为命令行界面，7为图形界面）进入命令行界面

    然后执行如下命令关闭图形界面：

    ```shell
    sudo service lightdm stop
    ```

    

2.  安装驱动：（同之前）

    ```shell
    sudo sh ./NVIDIA-Linux-x86_64-***.***.run --no-opengl-files
    ```

    检测安装成果：输入如下指令

    ```shell
    nvidia-smi
    ```

    若显示显卡则代表安装成功

    

3.  打开图形界面：

    ```shell
    sudo service lightdm start
    ```

