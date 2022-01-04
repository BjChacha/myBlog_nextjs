---
title: '终极CheatSheet系列'
date: '2020-11-10'
# lastmode: '2022-01-04'
tags: ['cheatsheet']
draft: true
summary: '受够了反反复复地上查网同一个指令'
# images: ['']
authors: ['default']
layout: PostLayout
---

## Linux(Ubuntu)

### 文件操作

- mkdir: 创建目录
- touch: 创建空文件
- rm: 删除文件
  - rm -rf: 删除目录及其所有文件
- mv: 移动/重命名

## Python

### 字符串相关

- str.join(list): 用`str`连接`list`中的字符串

- str.split(s): 以's'分隔字符串，返回列表

- str.strip(): 去掉字符串中特定字符，默认是空格

- str.lstrip(): 去掉字符串左端特定字符，默认是空格

- str.rstrip(): 去掉字符串右端特定字符，默认是空格

- str.ljust(n): 字符串左对齐，总长度为 n

- str.rjust(n): 字符串右对齐，总长度为 n

- str.center(n): 字符串居中（靠左），总长度为 n

### 随机数

- random.randint(a, b): [a,b]区间的随机整数

### 其他工具

- collections.Conter(iterable): 返回一个字典，键是`iterable`的元素，值是该元素出现的次数。

- itertools.combinations(iterable, n): 返回`iterable`中个数为`n`的自由组合。

## Conda

- conda update conda

## SQL

- DENSE_RANK() OVER(PARTITION BY col1 ORDER BY col2 DESC) "rank": 按照`col1`划分，对`col2`进行排序

## Numpy

## Vim

### 光标移动

- h, j, k, l: 左、下、上、右

- CTRL-F, CTRL-B: 上一页、下一页

- CTRL-U, CTRL-D: 上移半屏、下移半屏

- 0: 跳到行首

- ^: 跳到从行首开始第一个非空白字符

- $: 跳到行尾

- gg: 跳到第一行

- G: 跳到最后一行

- nG: 跳到第 n 行，如 10G 是跳到第 10 行

- 20%: 移动到文件 20%处

- 10|: 移动到当前行的 10 列

- w: 跳到下一个单词开头（标点或空格分隔）

- W: 跳到下一个单词开头（空格分隔）

- e: 跳到下一个单词尾部（标点或空格分隔）

- E: 跳到下一个单词尾部（空格分隔）

- b: 跳到上一个单词开头（标点或空格分隔）

- B: 跳到上一个单词开头（空格分隔）

- (): 向前、后移动一个句子（句号分隔）

- {}: 向前、后移动一个段落（空行分隔）

-

## VS Code

## PyTorch

### Tensor 创建

- torch.tensor([1, 2, 3], dtype=torch.float): 创建张量

- torch.arange(1, 10, 2): 范围为[1,10)、步进为 2 的序列

- torch.linspace(1, 10, 8): 范围为[1,10]、数量为 8 的序列

- torch.zeros((3, 3)): 3x3 全 0 张量

- torch.ones((3, 3)): 3x3 全 1 张量

- torch.eye(3, 3): 3x3 单位张量

- torch.diag(torch.tensor([1, 2, 3])): 对角张量

- torch.ones_like(a): 大小与`a`相同的全 1 张量

- torch.fill\_(a, 5): 大小、数据类型与`a`相同的全 5 张量

- torch.rand((3, 3)): 服从均匀分布的 3x3 随机张量

- torch.randn(2, 3): 服从正态分布的 2x3 随机张量

- torch.normal(torch.zeros((3,3)), torch.ones((3,3))): 服从标准正态分布的 3x3 随机张量

- torch.randperm(20): 长度为 20 的随机整数排列（从零开始）

### 数据选择

- torch.where(a>0): 返回张量`a`中大于 0 的元素坐标

- torch.where(a>0, torch.tensor(1), torch.tensor(0)): 张量`a`元素若大于 0，则赋值为 1，否则赋值为 0

- torch.masked_select(a, a>0): 返回张量`a`中大于 0 的元素

### 维度变换

- a.shape: 张量`a`的大小

- a.size(): 同上

- a.view([1, 9]): 将张量`a`改成 1x9 的张量

- torch.reshape(a, [1, 9]): 同上

- torch.squeeze(a): 消除长度为 1 的维度，如[1,3,3,1] -> [3,3]

- torch.transpose(a, 0, 1): 维度交换，如[1,3,3,1] -> [3,1,3,1]

### 合并分割

- torch.cat([a, b, c], dim=0): 合并，不增加维度

- torch.stack([a, b, c], axis=0): 合并，增加维度

- torch.split(abc, split_size_or_sections=[4,1,1], dim=0): 分隔，分别分成 4、1、1 份。

### 一般训练框架

```python
import torch
from torch import nn

# Data preprocessing
dataset = torch.utils.data.TensorDataset(..)
# or
dataset = torchvision.datasets.DataFolder(..)
dataloader = torch.utils.data.DataLoader(dataset, batch_size=128, shuffle=True, num_workers=2)

# Net Building
class YourNet(nn.Module):
  # your net here

net = YourNet
loss_func = nn.BCELoss()
optimizer = torch.optim.Adam(net.parameters(), lr=1e-3)

# Training
for e in range(epochs):
  optimizer.zero_grad()

  predictions = net(features)
  loss = loss_func(predictions, labels)

  loss.backward()
  optimizer.step()
```
