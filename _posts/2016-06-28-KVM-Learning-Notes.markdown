---
layout:     post
title:      "KVM学习笔记"
subtitle:   "短短的假期里持续更新"
date:       2016-06-28 00:00:00
author:     "Hang"
header-img: "img//2016-06-03/head-bg.jpg"
catalog: true
tags:
    - 技术
---


## 前言

从杭州回到了重庆，很快又要去上海。

事先声明：这篇博客可能不适合阅读。我想利用短短假期里的一些时间尽量有效地学习一下KVM，基本是从零开始，用笔记的形式督促一下自己，也算加深印象。至于这种完全顺着自己思路来的东西是否对别人有帮助...留作以后考量吧。

## 学习资料

[QEMU+KVM学习笔记](https://www.evernote.com/shard/s325/sh/bebe0aaa-9d30-4624-9772-a0af9dfffead/a08905a6b6733c5cd945bba560d4d927)

[The Definitive KVM (Kernel-based Virtual Machine) API Documentation](https://www.kernel.org/doc/Documentation/virtual/kvm/api.txt)

[KVM虚拟化原理与实践](http://smilejay.com/kvm_theory_practice/)

[Native Linux KVM Tool](http://lwn.net/Articles/436781/)

[Intel® 64 and IA-32 Architectures Software Developer Manuals](http://www.intel.com/content/www/us/en/processors/architectures-software-developer-manuals.html/)

[KVM-韦任的维基百科](http://people.cs.nctu.edu.tw/~chenwj/dokuwiki/doku.php?id=kvm)

## 6月29日

一般认为，虚拟机监控的实现有两类：监控模型(Hypervisor)和宿主机模型(Host-based)。由于监控模型需要进行处理器调度，还需要实现各种驱动程序，以支持运行其上的虚拟机，因此实现难度要大于宿主机模型。KVM的实现采用后者。

#### KVM 工作原理

KVM的基本工作原理：用户模式的Qemu利用接口libkvm通过ioctl系统调用进入内核模式。KVM Driver 为虚拟机创建虚拟内存和虚拟CPU后执行 VMLAUCH 指令进入客户模式。装载 Guest OS 执行。如果 Guest OS 发生外部中断或者影子页表缺页之类的事件，暂停 Guest OS 的执行，退出客户模式进行一些必要的处理。然后重新进入客户模式，执行客户代码。如果发生I/O事件或者信号队列中有信号到达，就会进入用户模式处理。

#### KVM 的内存管理

KVM使用影子页表实现客户物理地址到主机物理地址的转换。

#### 小结

这一天主要了解了一些 KVM 的基本概念。包括工作原理，功能组成，代码结构等。后面准备自己搭建一个 KVM。

## 6月30日

#### KVM 创建和运行虚拟机的流程

KVM 虚拟机创建和运行分为用户态和内核态两个部分。用户态主要提供应用接口，为虚拟机创建上下文环境，在 libkvm 中提供访问内核字符设备```/dev/kvm```的接口；内核态为添加到内核中的字符设备```/dev/kvm```，模块加载进内核后即可进行接口用户空间调用创建虚拟机。在创建虚拟机的过程中，KVM 字符设备主要为客户机创建 KVM 数据结构。


