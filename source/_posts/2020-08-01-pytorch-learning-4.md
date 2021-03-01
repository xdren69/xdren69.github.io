---
title: 卷积神经网络——pytorch-learning（四）
date: 2020-08-01 00:38:09
tags:
  - pytorch
toc: true
categories:
  - pytorch-learning
excerpt: 这是一篇关于pytorch的系列学习笔记的第四篇，主要学习了如何用pytorch来搭建一个卷积神经网络
---

## 图像形成简述

一般图像分为两类：

-   栅格图像：图像由像素点组成，一般可以由人工捕获，在相机摄像时，每个像素点都通过一个光学元件来采集——放大后变成马赛克

-   矢量图像：一般无法人工捕获，由程序生成，相当于一段描述：圆心在哪，半径多少....，一般由程序画出来，而不是现成的——放大不变形



## 卷积神经网络简述

使用DNN（全连接神经网络）来进行图像分类时，是将二维图像完全展开成一维图像，只保留了像素间横向的联系，却忽略了纵向的联系。

因此我们引入了卷积神经网络，提取其二维特征，其工作过程是通过黄色的卷积核来提取绿色的图像中的二维特征，随后将特征输出到粉色矩阵中，以便进行下一步的处理。**单通道卷积**的运算过程如下所示：

![](http://ufldl.stanford.edu/tutorial/images/Convolution_schematic.gif)

对于卷积核作用的直观理解：检测图像的局部是否满足某一特征，如果满足，则返回一个较大的值；否则返回一个较小的值

>   注意：对于多通道的图像，每次卷积是针对所有的通道来进行的，卷积核应该是三维的，第三个维度的大小应该和通道数相同，因此在讨论**卷积核的大小**时，只考虑二维的大小。如下图所示：

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202008/03/105832-244617.png)

展开后具体如下：

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202008/03/142132-415389.png)

压缩后如下所示（卷积时不加padding）：

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202008/03/142324-256961.png)

泛化图像的尺寸，以及投影到多个卷积核之后可以得到：

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202008/03/143138-516563.png)

>   注意：卷积核的数目与输出结果的通道数相同

### 卷积核的相关参数

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202008/03/143552-726443.png)

其中：

-   m是卷积核的个数
-   n是输入图像的通道数



### padding

在图像外层填充0，借此来保证经过卷积计算后的图形与原图形大小保持一致。一般而言，外围填充的0的圈数为

<div>$$\frac{kernel_size}{2}$$</div>

如下图所示：

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202008/04/043500-76666.png)



### stride

在进行卷积运算时，每一次移动卷积核的距离

```python
import torch

in_channels, out_channels= 5, 10
width, height = 100, 100
kernel_size = 3
batch_size = 1

input = torch.randn(batch_size, in_channels, width, height)
conv_layer = torch.nn.Conv2d(in_channels, out_channels, kernel_size=kernel_size)
# padding参数
# conv_layer = torch.nn.Conv2d(in_channels, out_channels, kernel_size=kernel_size, padding=1, bias=False)
# stride参数
# conv_layer = torch.nn.Conv2d(1, 1, kernel_size=3, stride=2, bias=False)
# 可以选择自己初始化卷积的权重
# kernel = torch.Tensor([1,2,3,4,5,6,7,8,9]).view(1, 1, 3, 3)
# conv_layer.weight.data = kernel.data
output = conv_layer(input)

print(input.shape)
print(output.shape)
print(conv_layer.weight.shape)
```

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202008/04/043212-614482.png)



### Pooling

可以理解成下采样，用来筛选特征，压缩图像大小。但是特征筛选只在同一个通道内进行；经过pooling之后图像的通道数不改变；在pooling层中：

`stride = kernal_size`

-   Max Pooling：每次取局部内的最大值

```python
import torch
input = [3,4,6,5,
2,4,6,8,
1,6,7,8,
9,7,4,6,]
input = torch.Tensor(input).view(1, 1, 4, 4)
maxpooling_layer = torch.nn.MaxPool2d(kernel_size=2)
output = maxpooling_layer(input)
print(output)
```

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202008/04/045128-924096.png)



## 代码示例

在此处我们使用MNIST数据集作为演示，具体网络架构如下：

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202008/03/103321-54993.png)

>   注意：卷积层和池化层不在意图像的大小，但是线性层在意，需要提前计算

```python
class Net(torch.nn.Module):
    def __init__(self):
        super(Net, self).__init__()
        self.conv1 = torch.nn.Conv2d(1, 10, kernel_size=5)
        self.conv2 = torch.nn.Conv2d(10, 20, kernel_size=5)
        self.pooling = torch.nn.MaxPool2d(2)
        self.fc = torch.nn.Linear(320, 10)
        
    def forward(self, x):
        # Flatten data from (n, 1, 28, 28) to (n, 784)
        batch_size = x.size(0)
        x = F.relu(self.pooling(self.conv1(x)))
        x = F.relu(self.pooling(self.conv2(x)))
        x = x.view(batch_size, -1) # flatten
        x = self.fc(x)
        return x

model = Net()
```

对于GPU的使用：

```python
# 选择设备
device = torch.device("cuda:0" if torch.cuda.is_available() else "cpu")
# 迁移模型
model.to(device)
# 迁移数据
def train(epoch):
    running_loss = 0.0
        for batch_idx, data in enumerate(train_loader, 0):
        inputs, target = data
        # 这里
        inputs, target = inputs.to(device), target.to(device)
        optimizer.zero_grad()
        # forward + backward + update
        outputs = model(inputs)
        loss = criterion(outputs, target)
        loss.backward()
        optimizer.step()
        running_loss += loss.item()
        if batch_idx % 300 == 299:
        print('[%d, %5d] loss: %.3f' % (epoch + 1, batch_idx + 1, running_loss / 2000))
        running_loss = 0.0
```



## 卷积神经网络进阶

>   以复杂网络GoogLeNet为例子
>
>   ![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202008/11/154655-910069.png)

### 对Inception模块进行解释

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202008/11/155211-784046.png)

### 1*1的卷积核

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202008/11/151215-789362.png)

作用：

-   将所有输入通道同一像素点的数值进行加权

-   改变通道的数目，相比于其他大小的卷积，1*1的卷积在改变通道数目时，计算量较小。对比如下：

    ![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202008/11/153045-547791.png)

### 代码实现

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202008/11/155326-574495.png)

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202008/11/155617-685512.png)

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202008/11/155649-760652.png)

>    注意：张量的维度表示方式为（B,C,W,H），因此*torch.cat*操作时dim=1



## 卷积的深度及其效果

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202008/11/161503-83004.png)

>   结论：并不是卷积越深，效果越好
>
>   解决方法：使用残差网络（Residual net）



## 残差网络（Residual net）

![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202008/11/162534-820915.png)

 ## 搭建网络的建议

1.  逐层搭建，逐层测试，主要测试网络的输入与输出的矩阵的大小



![](https://gitblog-1302688916.cos.ap-beijing.myqcloud.com/cs224n/202008/11/164124-300897.png)



## 引用

[1] [河北工业大学刘洪普老师的视频教程](https://www.bilibili.com/video/BV1Y7411d7Ys)

[2] [He K, Zhang X, Ren S, et al. Identity Mappings in Deep Residual Networks]()

[3] [Huang G, Liu Z, Laurens V D M, et al. Densely Connected Convolutional Networks]()

