---
layout:     post
title:      "Python数据分析(2) NumPy基础"
subtitle:   "Do Things That Won't Scale"
date:       2017-03-04 03:48:00
author:     "Hang"
header-img: "img//2016-06-03/head-bg.jpg"
catalog: true
tags:
    - 技术
---

## NumPy的ndarray:一种多维数组对象

ndarry是一个通用的同构数据多维容器，其中的所有元素必需是相同类型的。创建ndarray最简单的方法是使用array函数，它接收一切序列型的对象，然后产生一个新的含有传入数据的NumPy数组。
dtype是一个特殊的对象，它含有ndarray将一块内存解释为特定数据类型所需的信息。也可以通过ndarray的astype方式显式地转换其dtype。如果将浮点数转换成整数，则小数部分将会被截断。
如果字符串数组表示的全是数字，也可以用astype将其全部转换为数值形式。

数组使你不用编写循环即可对数据执行批量运算。这通常就叫做矢量化(vectorization)。NumPy中大小相等的数组之间的任何算术运算都会将运算应用到元素级。不同大小的数组之间的运算叫做广播
(broadcasting)。

Numpy数组选取数据子集的或单个元素的方式很多。将一个标量赋值给一个切片时，该值会自动传播到整个选区。跟列表最重要的区别在于，数组切片是原始数组的视图。数据是不会被复制的，视图
上的任何修改都会直接反应到源数据上。如果想得到ndarray的一份副本而非视图，就需要显示地进行复制操作。而通过布尔型索引选取数组中的数据，将总是创建数据的副本。

## 利用数组进行数据处理

利用NumPy可以将条件逻辑表述为数组运算，也可以将sum/any/all用于布尔型数组。NumPy也可以用sort方法排序。

NumPy提供了一些针对一维数组的基本集合运算。最常用的是unique，它用于找出数组中的唯一值并返回已排序的结果。unique可以等价为纯Python代码中的sorted(set())。

## 用于数组的文件输入输出

np.save和np.load是读写磁盘数组的两个主要函数。默认情况下，数组是以未压缩的原始二进制格式保存在扩展名为.npy的文件中。

## 线性代数

NumPy提供了一个用于矩阵乘法的dot函数。

numpy.linalg中有一组标准的矩阵分解运算以及诸如求逆inv和行列式det之类的功能。

## 随机数生成

NumPy.random模块对Python的内置randam进行了补充，增加了一些用于高效生成多种概率分布的样本值的函数。例如可以用normal来得到一个标准正态分布的4*4样本数组：

'''
np.random.normal(siez=(4,4))
'''

## 范例：随机漫步

看一个简单的随机漫步的例子：从0开始，步长1和-1出现的概率相等。首先通过内置的random模块一纯Python的方式实现1000步的随机漫步：

'''
import matplotlib.pyplot as plt
import random
position = 0
walk = [position]
steps = 1000
for i in xrange(steps):
    step = 1 if random.randint(0,1) else -1
    position += step
    walk.append(position)
plt.plot(xrange(steps+1),walk)
'''

![](/img/2017-03-06/random-walk.png)

不难看出，这其实就是随机漫步中各补的累计和，可以用一个数组运算来实现。我们用np.random模块来一次性随机生产1000个掷硬币结果。将其分别设置为1或-1，然后统计累计和：

'''
import numpy as np
nsteps = 1000
draws = np.random.randint(0,2,size=nsteps)
steps = np.where(draw>0,1,-1)
walk = steps.cumsum()
'''

如果希望模拟多个随机漫步过程(比如5000个)，只需要对以上代码做些许修改：给numpy.random的函数传入一个二元元组就可以产生一个二维数组：

'''
nwalks = 5000
nsteps = 1000
draws = np.random.randint(0,2,size=(nwalks,nsteps))
steps = np.where(draws>0,1,-1)
walks = steps.cumsum(1)
'''

