---
layout:     post
title:      "从零开始的Java项目实践手记"
subtitle:   "还携手看着天空黑与光"
date:       2017-09-03 09:25:00
author:     "Hang"
header-img: "img//2016-06-03/head-bg.jpg"
catalog: true
tags:
    - 编程
---

这篇博客记录一个Java项目：地震信息动态演示程序的实现过程。一边练习，一边学习面向对象的思想和编写程序的规范。这个项目里面会涉及到关于内存模型、作用域、GUI、继承、事件、排序等内容。这些内容会在这篇博客里分步骤得到体现。另外项目中用到的库主要有两个：[processing](https://www.processing.org/reference/)，用来生成GUI和一些图形；[unfoldingmaps](http://unfoldingmaps.org/javadoc/)，用来调用地图接口。

## 第一步：基本框架

我使用的编程环境是jdk144+eclipse，首先要做的是将程序的基本框架搭建起来，从processing库的PApplet类继承来实现窗口的绘制，然后创建unfoldingmap类的实例在窗口上绘制地图。完成的效果是这样的：

![](/img/2017-09-03/unfoldingmapexample.png)

### 注意事项：

Unfoldingmap需要指定地图提供方，在我的程序里使用的是：

```java

// Select a map provider
AbstractMapProvider provider = new Google.GoogleTerrainProvider();


```

然而谷歌地图无法直接在国内的网络环境下打开,这会导致无法在程序中打开地图。

#### 解决办法

1. 修改地图提供方

翻阅[unfoldingmap的文档](http://unfoldingmaps.org/javadoc/)，我们可以将代码修改为：

```java

// Select a map provider
AbstractMapProvider provider = new Microsoft.MicrosoftProvider()

```

使用微软公司的必应地图作为代替。

2. 设置代理

如果有VPN资源也可以自行设置代理，方法有好几种，我这里使用的是修改Java的default VM arguments的方式。

![](/img/2017-09-03/vmargument.png)

