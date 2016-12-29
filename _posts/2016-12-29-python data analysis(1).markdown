---
layout:     post
title:      "Python数据分析(1) IPython环境"
subtitle:   "Do Things That Won't Scale"
date:       2016-12-29 09:48:00
author:     "Hang"
header-img: "img//2016-06-03/head-bg.jpg"
catalog: true
tags:
    - 技术
---

## 为什么使用Python

其实想用Python有一段时间了。也算是掌握了一些基础。回想一下，非系统性的使用过Python来：完成Coursera课程的文本处理作业、AC了几道LeetCode的题、学习了一下机器学习。再次拾起呢，是应为我的研究课题里面有一个开源项目使用的Python，我很想利用起来。目标就是能够使用开源项目，完成我的论文；进一步要改进源代码，混一混开源社区。顺便把没学完的机器学习也学完。人生苦短，我用Python。哦，我也用C/C++,Shell,Matlab,JavaScript。

## 环境和社区

我使用的是Anaconda3，各种工具都集成好了，非常方便。然后列一下Python相关社区，算是提醒下自己：

pydata: 一个google group邮件列表，问题是关于python数据分析和pandas方面的。

pystatsmodels: 针对与statsmodels和pandas相关的问题。

numpy-discussion: 针对与NumPy相关的问题。

scipy-user: 针对与SciPy和Python科学计算相关的问题。

## IPython

2001年，Fernando Perez为了得到一个更为高效的交互式Python解释器而启动了一个业余项目，于是IPython项目诞生了。

#### IPython基础

通过命令行启动IPython的方法与启动标准Python类似。许多Python对象都被格式化为排版更好的形式。下面是在标准Python解释器(3.5.1)上和IPython(4.0.0)上执行相同代码的显示结果：

```
>>> from numpy.random import randn
>>> data={i:randn() for i in range(7)}
>>> data
{0: 1.8230318057018993, 1: 0.7039919684178829, 2: 1.032168710605224, 3: -0
21946304076, 4: -0.24785058178520072, 5: -2.83012041586793, 6: 0.655515075
}
```

```
In [1]: from numpy.random import randn

In [2]: data={i:randn() for i in range(7)}

In [3]: data
Out[3]:
{0: 0.539776562751879,
 1: 2.1688673163165673,
 2: 0.1427020793150374,
 3: 0.5402213808397194,
 4: -0.3480578983327642,
 5: 0.17633671837934767,
 6: -0.5129201556582613}
 ```

##### Tab键自动完成

Tab键自动完成是对标准Python Shell的主要改进之一，大部分的交互式数据分析环境都有这个功能。在Shell中输入表达式时，只要按下Tab键，当前命名空间中任何与已输入的字符串相匹配的变量就会被找出来。Tab键自动完成功能不止可以用于搜索命名空间和自动完成对象或模块属性。当输入文件路径时，Tab键也可以用于文件系统的匹配。

#### 内省

在变量的前面或后面加上一个问号(?)就可以将有关该对象的一些通用信息显示出来：

```
In [5]: ?data
Type:        dict
String form: {0: 0.539776562751879, 1: 2.1688673163165673, 2: 0.1427020793150374
, 3: 0.5402213808397194, 4: -0.3480578983327642, 5: 0.17633671837934767, 6: -0.5
129201556582613}
Length:      7
Docstring:
dict() -> new empty dictionary
dict(mapping) -> new dictionary initialized from a mapping object's
    (key, value) pairs
dict(iterable) -> new dictionary initialized as if via:
    d = {}
    for k, v in iterable:
        d[k] = v
dict(**kwargs) -> new dictionary initialized with the name=value pairs
    in the keyword argument list.  For example:  dict(one=1, two=2)
```

这就叫做对象内省(object introspection)。

对函数使用??还可能查看其源代码。

?还有一个用法是搜索IPython命名空间，如：

```
In [10]: import numpy as np

In [11]: np.*load*?
np.__loader__
np.load
np.loads
np.loadtxt
np.pkgload
```

##### %run命令

在IPython会话环境中，所有的文件都可以通过```%run```命令来当做Python程序运行。

脚本是在一个空的命名空间中运行的(没有任何import，也没有定义任何其他的变量)，所以其行为应该跟在标准命令行环境中执行时一样。此后该文件中定义的全部变量(以及import、函数和全局变量)就可以在当前的IPython Shell中访问了。

注意如果希望脚本能够访问在交互式IPython命名空间中定义的变量，那就应该使用```%run -i```而不是```%run```

###### 中断正在执行的代码

任何代码在执行时

##### 执行剪贴板中的代码

###### IPython跟编辑器和IDE之间的交互

键盘快捷键

异常和跟踪

魔术命令

基于Qt的富GUI控制台

matplotlib集成与pylab模式

#### 使用命令历史

搜索并重用命令历史

输入和输出变量

记录输入和输出

#### 与操作系统交互

shell命令和别名

目录书签系统

#### 软件开发工具

交互式调试器

调试器的其他使用场景

测试代码的执行时间:%time和%timeit

基本性能分析:%prun和%run -p

逐行分析函数性能

#### IPython HTML Notebook

#### 利用IPython提高代码开发效率的几点提示

重新加载模块依赖项

代码设计提示

保留有意义的对象和数据

扁平结构要比嵌套结构好

无惧大文件

#### 高级IPyhon功能

让你的类对IPyhon更加友好

个性化和配置