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

## 汇总和计算描述统计

## 处理缺失数据

## 层次化索引

## 其他有关pandas的话题