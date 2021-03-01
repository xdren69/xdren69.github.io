---
title: vscode+jupyter 配置问题记录
tags:
- 环境配置
---



环境条件：

>   系统：windows
>
>   conda版本：4.8.3



### CommandNotFoundError: Your shell has not been properly configured to use 'conda activate'.

```
CommandNotFoundError: Your shell has not been properly configured to use 'conda activate'.
To initialize your shell, run

    $ conda init 

Currently supported shells are:
  - bash
  - fish
  - tcsh
  - xonsh
  - zsh
  - powershell

See 'conda init --help' for more information and options.

IMPORTANT: You may need to close and restart your shell after running 'conda init'.
```

问题描述：终端不断提示需要运行```conda init ```，但是按照提示操作依旧没效果

解决方法：使用cmd作为终端来替换powershell，即在setting添加个人设置：

```json
"terminal.integrated.shell.windows": "C:\\Windows\\System32\\cmd.exe"
```



### VS code 配置python环境问题：'dosk' is not recognized as an internal or external command

> 问题描述：使用VS code的终端出现提示如下
>
> ![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202007/26/092006-346281.png)

解决方法：


在环境变量中Path名下添加：**%SystemRoot%\system32**，注意不能直接添加**C:\Windows\System32**，原因尚不明确

不过添加**%SystemRoot%\system32**后，系统会自动解析成

![](D:\AppData\Roaming\marktext\images\2020-06-29-18-57-55-image.png)

此后问题便可以解决了



### 报错Error: Activating Python 3.6.5 64-bit ('base': conda) to run Jupyter failed with Error: spawn cmd ENOENT.


> 此处的解决方法为：在setting中添加用户设置

```json
"terminal.integrated.env.windows": {
    "PATH": ""
},
```

原因尚不明确，具体博客见[这里](https://www.gitmemory.com/issue/microsoft/vscode-python/9230/567731790)



### 在vscode中创建.jupyter文件

直接将文件格式改成jupyter后，如下：

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202007/26/102724-18666.png)

有些情况下会无法添加cell；参考[知乎大佬的意见](https://zhuanlan.zhihu.com/p/85445777)采用如下方法：

1.  使用快捷键**Ctrl+Shift+P** 调出 Command Palette

2.  输入**"Python: Create New Blank Jupyter Notebook"** 

    ![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202007/26/103121-323552.png)

3.  即可创建可以添加cell的jupyter文件

    ![](https://picb.zhimg.com/80/v2-de4d3b1f27329e3b3142588659c98bfb_720w.jpg)

