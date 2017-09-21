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

## 第二步：生成地震标记和图例

有了基本框架以后，我们的下一步工作是将发生地震的位置标记到地图上。利用[USGS](https://earthquake.usgs.gov/)提供的地震信息，用unfoldingmap的Marker类将信息可视化的显示到地图上。使用了现成的parser，将最近一周发生的地震记录在一个List中。核心部分代码如下：

```java
// The List you will populate with new SimplePointMarkers
List<Marker> markers = new ArrayList<Marker>();

//Use provided parser to collect properties for each earthquake
//PointFeatures have a getLocation method
List<PointFeature> earthquakes = ParseFeed.parseEarthquake(this, earthquakesURL);
	    
// to create a new SimplePointMarker for each PointFeature in 
// earthquakes.  Then add each new SimplePointMarker to the 
// List markers (so that it will be added to the map in the line below)
for (PointFeature earthquake : earthquakes) {
	markers.add(this.createMarker(earthquake));
}
	    
// Add the markers to the map so that they are displayed
map.addMarkers(markers);
```

### 功能完善

我们希望标记在地图上的地震点按强度显示不同的大小和颜色。查看unfoldingmap的文档，可以用setColor()和setRadius()实现这些功能。只需要在上文代码中对私有函数createMarker()进行修改即可实现。修改后的函数代码如下。

```java
private SimplePointMarker createMarker(PointFeature feature)
{  
	// To print all of the features in a PointFeature (so you can see what they are)
	// uncomment the line below.  Note this will only print if you call createMarker 
	// from setup
	//System.out.println(feature.getProperties());
		
	// Create a new SimplePointMarker at the location given by the PointFeature
	SimplePointMarker marker = new SimplePointMarker(feature.getLocation());
		
	Object magObj = feature.getProperty("magnitude");
	float mag = Float.parseFloat(magObj.toString());
		
	// Here is an example of how to use Processing's color method to generate 
	// an int that represents the color yellow.  
	int yellow = color(255, 255, 0);
	int red = color(255, 0, 0);
	int blue = color(0, 0, 255);

	if (mag >= THRESHOLD_MODERATE) {
		marker.setColor(red);
		marker.setRadius((float)(1.6 * mag + 3));
	} else if (mag >= THRESHOLD_LIGHT) {
		marker.setColor(yellow);
		marker.setRadius((float)(mag * 1.6 + 2));
	} else {
		marker.setColor(blue);
		marker.setRadius((float)(mag * 1.6 + 1));
	}
	// Finally return the marker
	return marker;
}
```

在这之后，我们希望在窗口左侧添加简单的图例说明，直接利用processing库中的简单接口就可以了。第二步完成的效果图如下：

![](/img/2017-09-03/markerandkey.png)
