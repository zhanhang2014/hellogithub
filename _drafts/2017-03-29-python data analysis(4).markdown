---
layout:     post
title:      "Python数据分析(4) 使用HDF5读写数据"
subtitle:   "Do Things That Won't Scale"
date:       2017-03-07 04:06:00
author:     "Hang"
header-img: "img//2016-06-03/head-bg.jpg"
catalog: true
tags:
    - 技术
---

课题中收集的公共数据集是基于HDF5格式的，所以这里着重介绍。有关CSV、JSON、XML的读写以后如果用到再来此补充。

## HDF5简介

HDF5是一个高效读写磁盘二进制格式文件的科学工具，它使用C实现，带有许多语言接口，如Java、Python和MATLAB等。HDF指的是层次类型数据格式(hierarchical data format)。

