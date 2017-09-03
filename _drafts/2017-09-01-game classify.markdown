---
layout:     post
title:      "Java并查集：原理，实现和应用"
subtitle:   "Do it right the frist time, maybe a slower way"
date:       2017-09-01 03:00:00
author:     "Hang"
header-img: "img//2016-06-03/head-bg.jpg"
catalog: true
tags:
    - 思考
---

## 并查集简介

如何将数据划分成多个集合？如何知道某个数据属于哪个集合？一个解决这类问题的方式就是使用并查集数据结构和联合查找算法（union-find algorithm）。顾名思义，并查集包含了两个基本的操作：查找find以及合并union。

## 并查集的实现和优化

并查集可以用来描述很多实际问题，例如刻画网络中的计算机、社交网络中的朋友关系等等。在编程中我们可以使用0-N-1的整数来描述集合中的各节点。使用Java描述并查集的基本接口：

```java	

public class UF {
	public UF(int N) // 构造并查集

	public boolean connected(int p, int q) // 查询两个节点是否相连（在一个子集中）

	public void union(int p, int q) // 连接两个节点
}

```

#### 直觉方法

首先使用最简单的方法来实现以上接口： 使用一个整型数组id[]，数组的下标代表不同节点，数组的值如果相同，则对应的节点相连。这样我们可以完善UF类：

```java

public class UF {

	private int[] id;

	public UF(int N) {
		id = new int[N];
		for (int i = 0; i < N; i++) {
			id[i] = i;
		}
	}

	public boolean connected(int p, int q) {
		return id[p] == id[q];
	}

	public void union(int p, int q) {
		int pid = id[p];
		int qid = id[q];
		for (int i = 0; i < id.length; i++) {
			if (id[i] == pid) id[i] = qid;
		}

	}
}

```

简单分析这个算法：

|算法|初始化|union|connected|
|------|------|------|------|
|Union-find|N|N|1|

#### 树型结构

每次union都要遍历整个数组，代价相对较高，可以使用树型结构改进：每个子集构成一棵树，将id[i]看做是第i个节点的父节点，这样回溯找到id[i]与i相等的节点看做是一个子集的根节点。改进后的代码：

```java

public class QuickUF {

	private int[] id;

	public QuickUF(int N) {
		id = new int[N];
		for (int i = 0; i < N; i++) {
			id[i] = i;
		}
	}

	private int root(int i) { // 回溯找到根节点
		while (i != id[i]) i = id[i];
		return i;
	}

	public boolean connected(int p, int q) {
		return root(p) == root(q);
	}

	public void union(int p, int q) {
		int proot = root(p);
		int qroot = root(q);
		id[proot] = qroot; // 将p的根节点连接到q, P所在的整棵树连接到q 

	}
}

```

对于这个算法：

|算法|初始化|union|connected|
|------|------|------|------|
|Quick-union-find|N|Ω(N)|Ω(N)|

#### 平衡与路径压缩

这样我们放弃了常数时间的connected查询，减少了每次union的复杂度。不过我们并又有用任何方式限制树的生长方式，在最坏的情况下union仍需要N次操作才能将连个子集合并。这种情况下树非常不平衡，极端的朝着某个单一方向生长。可以考虑引入权重，在合并时将较小的树连接到较大的树的根节点上。
使用一个整型数组sz[]记录一个根节点所在树的节点数，只需要修改quickUF中的union函数：

```java
public void union(int p, int q) {
	int proot = root(p);
	int qroot = root(q);
	if (proot == qroot) return;
	if (sz[proot] < sz[qroot]) {
		id[proot] = qroot; 
		sz[qroot] += sz[proot];
	}
	else {
		id[qroot] = proot;
		sz[proot] += sz[qroot];
	}
}
```

对于这个算法：

|算法|初始化|union|connected|
|------|------|------|------|
|Weighted-quick-union-find|N|lg(N)|lg(N)|

另外一个技巧是路径压缩，在root()函数中，我们一步步回溯寻找根节点，若将代码改成：

```java

private int root(int i) { // 回溯找到根节点
	while (i != id[i]) {
		i = id[id[i]];
	}
	return i;
}

```

这里一次回溯两层，路径减半，如果树的规模非常大，还可以考虑多次嵌套继续压缩路径。


## 蒙特卡洛实验

以上就是关于并查集的简单算理和Java实现，我们可以利用并查集来进行蒙特卡洛实验。通常蒙特卡洛方法通过构造匹配一定规则的随机数来解决一个特定的问题。例如用蒙特卡洛方法计算圆周率。在这里我们想做的则是判断一个NXN的正方形是否上下连通：可以将这个问题看做是NXN的区域随机产生一些空隙和障碍，看水流能否从上到下流通，或者是电流能否从一些随机分布的导体和绝缘体之间流通。

![](/img/2017-08-31/montecarlo.png)

那么这个问题里，已经连通的部分可以看做是一个连通子集，可以使用union来实现。如何判断是否上下连通呢？我们引入两个虚拟节点，分别连通最上层和最下层，那么问题就转换成了这两个虚拟节点是否属于同一个子集。

![](/img/2017-08-31/virtualsite.png)

有关蒙特卡洛实验的代码部分会在后面的博客中再涉及到更多的细节。


