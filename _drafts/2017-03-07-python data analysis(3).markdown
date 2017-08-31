---
layout:     post
title:      "Python数据分析(3) pandas入门"
subtitle:   "Do Things That Won't Scale"
date:       2017-03-07 04:06:00
author:     "Hang"
header-img: "img//2016-06-03/head-bg.jpg"
catalog: true
tags:
    - 技术
---

## pandas的数据结构介绍

要使用pandas，首先要熟悉它的两个主要数据结构；series和dataframe。

#### Series

Series是一种类似于一维数组的对象，它由一组数据(各种NumPy数据类型)以及一组与之相关的数据标签(索引)组成。
Series的字符串表现形式为：索引在左，值在右。在没有指定索引时会自动创建一个0到n-1的整数型索引。

可以通过索引的方式选取Series中的单个或一组值。NumPy数组运算都会保留索引和值之间的链接。

Series最重要的一个功能是：它在算术运算中会自动对齐不同索引的数据。

#### DataFrame

DataFrame是一个表格型的数据结构，它含有一组有序的列，每列可以是不同的值类型(数值、字符串、布尔值等)。

最常用的构建DataFrame的方法是直接传入一个由等长列表或NumPy数组组成的字典。

## 基本功能

#### 重新索引

reindex的作用是创建一个适应新索引的新对象。例如：

```
obj = pd.Series([5,8,0,-1],index=['d','b','a','c'])
obj2 = obj.reindex(['a','b','c','d','e'])
```

#### 丢弃指定轴上的项

丢弃某条轴上的一个或多个选项，使用drop并指定索引数组即可：

```
new_obj = obj.drop('c')
```

#### 索引、选取和过滤

对DataFrame进行索引其实就是获取一个或多个列：

```
data['two']
data[['three','one']]
```

#### 算术运算和数据对齐

在对象相加时，如果存在不同的索引对，结果的索引是索引对的并集。在不重叠的索引处会引入NA值。也可以使用add方法并传入fill_value值来指定填充值。在使用reindex重新索引时也可以指定一个填充值。

DataFrame和Series之间的运算会进行广播(broadcasting)。默认情况下DataFrame和Series之间的算术运算会将Series的索引匹配到DataFrame的列，然后沿行一直向下广播。

Numpy的ufuncs也可以用于操作pandas对象，或者使用apply方法：

```
f = lambda x:x.max() - x.min()
frame.apply(f)
```
可以使用sort_index方法按索引排序，可以使用order方法对Series排序。排序时的任何缺损值会被排到末尾。

## 练习代码

```
import pandas as pd

obj = pd.Series([4,7,-5,3])
obj.values
obj.index

obj2 = pd.Series([5,8,0,-1],index=['d','b','a','c'])
obj2['a']
obj2[['a','c','d']]
'b' in obj2

sdata = {'Ohio':35000,'Texas':71000,'Oregon':16000,'Utah':5000}
obj3 = pd.Series(sdata)

states = ['California','Ohio','Oregon','Texas']
Obj4 = Series(sdata,index=states)
pd.isnull(Obj4)
pd.notnull(Obj4)
obj4.isnull()
obj3+obj4
obj4.name = 'population'
obj4.index.name = 'state'

data = {'state':['Ohio','Ohio','Ohio','Nevada','Nevada'],'year':[2000,2001,2002,2001,2002],'pop':[1.5,1.7,3.6,2.4,2.9]}
frame = pd.DataFrame(data)
pd.DataFrame(data,columns=['year','state','pop'])
frame['state']
frame.year
frame.ix[1]
frame['eastern']=frame.state=='Ohio'
del frame['eastern']
frame.T

obj = Series(range(5),index=['a','a','b','b','c'])
obj.is_unique
```