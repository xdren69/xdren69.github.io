---
title: pytorch模型的保存与加载
tags:
---



## state_dict(): 模型参数的存储与查找

通过模型来解释state_dict()的作用：

```python
# Define model
class TheModelClass(nn.Module):
    def __init__(self):
        super(TheModelClass, self).__init__()
        self.conv1 = nn.Conv2d(3, 6, 5)
        self.pool = nn.MaxPool2d(2, 2)
        self.conv2 = nn.Conv2d(6, 16, 5)
        self.fc1 = nn.Linear(16 * 5 * 5, 120)
        self.fc2 = nn.Linear(120, 84)
        self.fc3 = nn.Linear(84, 10)

    def forward(self, x):
        x = self.pool(F.relu(self.conv1(x)))
        x = self.pool(F.relu(self.conv2(x)))
        x = x.view(-1, 16 * 5 * 5)
        x = F.relu(self.fc1(x))
        x = F.relu(self.fc2(x))
        x = self.fc3(x)
        return x

# Initialize model
model = TheModelClass()

# Initialize optimizer
optimizer = optim.SGD(model.parameters(), lr=0.001, momentum=0.9)

# Print model's state_dict-模型的参数
print("Model's state_dict:")
for param_tensor in model.state_dict():
    print(param_tensor, "\t", model.state_dict()[param_tensor].size())

# Print optimizer's state_dict-优化器的参数
print("Optimizer's state_dict:")
for var_name in optimizer.state_dict():
    print(var_name, "\t", optimizer.state_dict()[var_name])
```

其输出如下所示：

```python
Model's state_dict:
conv1.weight     torch.Size([6, 3, 5, 5])
conv1.bias   torch.Size([6])
conv2.weight     torch.Size([16, 6, 5, 5])
conv2.bias   torch.Size([16])
fc1.weight   torch.Size([120, 400])
fc1.bias     torch.Size([120])
fc2.weight   torch.Size([84, 120])
fc2.bias     torch.Size([84])
fc3.weight   torch.Size([10, 84])
fc3.bias     torch.Size([10])

Optimizer's state_dict:
state    {}
param_groups     [{'lr': 0.001, 'momentum': 0.9, 'dampening': 0, 'weight_decay': 0, 'nesterov': False, 'params': [4675713712, 4675713784, 4675714000, 4675714072, 4675714216, 4675714288, 4675714432, 4675714504, 4675714648, 4675714720]}]
```



## 模型的保存与加载

### 保存与加载推断模型——state_dict()

#### 保存

>   PATH中一般使用 \'.pt\' 或者是 \'.pth\' 作为模型文件扩展名

```python
torch.save(model.state_dict(), PATH)
```

#### 加载

```python
# 需要预先定义模型
model = TheModelClass(*args, **kwargs)
# 先通过torch.load(PATH)来反序列化参数字典；才能通过model.load_state_dict()来将参数加载进模型
model.load_state_dict(torch.load(PATH))
# 在运行推理之前，务必调用 model.eval() 去设置 dropout 和 batch normalization 层为评估模式。如果不这么做，可能导致模型推断结果不一致
model.eval()
```



### 保存与加载完整模型

#### 保存

```python
torch.save(model, PATH)
```

#### 加载

```python
# 不用预先定义模型，直接加载
model = torch.load(PATH)
model.eval()
```



### 保存 和 加载 Checkpoint 用于推理/继续训练

#### 保存

```

```

#### 加载

