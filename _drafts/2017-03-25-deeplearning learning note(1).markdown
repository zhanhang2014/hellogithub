---
layout:     post
title:      "深度学习 学习笔记(1) TensorFlow环境搭建"
subtitle:   "Keep keeping"
date:       2017-03-25 10:54:00
author:     "Hang"
header-img: "img//2016-06-03/head-bg.jpg"
catalog: true
tags:
    - 技术
---


## TensorFlow简介

TensorFlow是由Google开发的开源机器学习库。TensorFlow的功能很直白的写在它的名字上：使用数据流图(data flow graphs)技术进行数值计算。Tensor(张量)代表多维数组，Flow(流)代表基于数据流图的计算，TensorFlow即为张量在图中的流动。关于这个概念的理解，最好是在环境搭建和实践的过程再逐渐深入理解。

## TensorFlow搭建

本文使用一种快捷的方式搭建TensorFlow环境：Docker容器。这样做的好处是不用为依赖环境或是版本之类的事情烦心。

#### 安装Docker

以Window环境为例，首先安装[Docker Toolbox](https://www.docker.com/products/docker-toolbox)。由于国内网络的原因，建议使用阿里云的[镜像](http://mirrors.aliyun.com/docker-toolbox/?spm=5176.100239.blogcont60601.20.rcckTf)。安装时注意git和virtual box也是需要的，如果没有装过记得勾选上。安装注意务必使用管理员权限安装。

#### 配置镜像

安装成功后，用管理员模式启动Docker Quikstart Terminal。注意如果你的git不在默认目录下是需要手动查找git bash.exe路径的(在git的bin目录下)。第一次启动时，会自动下载boot2docker.ios镜像，这个可以理解为是docker通过virtal box预先启动的管理容器的虚拟机。同样这个也非常的慢，建议查看好terminal上显示的路径和版本,然后去github自己下载对应镜像到路径下。这步完成以后使用命令：

```
docker-machine ls
```

可以看到：

![](/img/2017-03-25/machine.jpg)

然后便可以部署TensorFlow的容器了。使用docker pull命令下载镜像(同样推荐在[阿里云开发者平台](https://dev.aliyun.com/search.html)上查找)。然后使用docker run命令启动容器。不过更简便的方法是用docker-compose以配置文件的方式来部署。推荐一个学习用的教程[https://github.com/aymericdamien/TensorFlow-Examples](https://github.com/aymericdamien/TensorFlow-Examples?spm=5176.100239.blogcont60601.21.rcckTf)上面有Jupyter环境和官方例程的代码。使用：

```
git clone https://github.com/denverdino/TensorFlow-Examples
cd TensorFlow-Examples
```

下载该例程，该目录下有一个配置好的docker-compose.yml模板。执行以下命令，便能以模板中的配置自动创建Tensorflow环境了:

```
docker-compose up -d
```

创建完成后，在终端查看启动的Docker容器:

![](/img/2017-03-25/containers.jpg)

#### 端口转发

容器启动之后，我们如何访问呢？可以使用:

```
docker exec it jupyter bash
```

以终端的方式访问容器，其中的jupyter是所访问的容器名或者ID。这样还是不够方便。我们使用的学习例程容器已经配置好了Jupyter和Tensorboard服务，可以让我们从主机上以HTTP的方式访问容器。在docker-compose.yml中已经设置好了访问端口，我们需要做的是在Virtual box中设置端口转发：

以管理员方式打开Vitual box->选中正在运行的虚拟机->设置->网络->高级->端口转发->按配置文件设置端口和名称。

![](/img/2017-03-25/port.jpg)

注意要关闭网络代理，在主机的浏览器上打开[http://localhost:8888/](http://localhost:8888/)和[http://localhost:6006/](http://localhost:6006/)就可以访问容器了。

## 简单实践

环境配置好之后，我们就用一个简单的线性回归的例子来实践一下。这个例子的代码在jupyter中：

![](/img/2017-03-25/example.jpg)

我把完整代码和自己的注释也贴在这里：

```
//引入tensorflow等相关库
import tensorflow as tf
import numpy
import matplotlib.pyplot as plt
rng = numpy.random

//设置参数，学习步长、周期等
learning_rate = 0.01
training_epochs = 1000
display_step = 50

//训练数据和标记
train_X = numpy.asarray([3.3,4.4,5.5,6.71,6.93,4.168,9.779,6.182,7.59,2.167,
                         7.042,10.791,5.313,7.997,5.654,9.27,3.1])
train_Y = numpy.asarray([1.7,2.76,2.09,3.19,1.694,1.573,3.366,2.596,2.53,1.221,
                         2.827,3.465,1.65,2.904,2.42,2.94,1.3])
n_samples = train_X.shape[0]

//注意这里使用的placeholder是tensorflow中方便表示数据的占位符。
//数据的特点是替换多于改变。
X = tf.placeholder("float")
Y = tf.placeholder("float")

//这里使用的variable则更多用来表示权重、偏置等参数。
//参数的特点是迭代中不断修改。
W = tf.Variable(rng.randn(), name="weight")
b = tf.Variable(rng.randn(), name="bias")

//这句将以上tensor的关系表达出来，并未进行实际计算。
//tensorflow使用python描述数据流图，再使用高效的方式统一计算。
pred = tf.add(tf.multiply(X, W), b)

//最小二乘的代价函数：
cost = tf.reduce_sum(tf.pow(pred-Y, 2))/(2*n_samples)
//使用梯度下降优化：
optimizer = tf.train.GradientDescentOptimizer(learning_rate).minimize(cost)

//在开始tensorflow的计算之前都需要做一步初始化：
init = tf.global_variables_initializer()

//tensorflow以会话的方式来执行计算
with tf.Session() as sess:
    sess.run(init)

    for epoch in range(training_epochs):
        for (x, y) in zip(train_X, train_Y):
        	//算法核心
            sess.run(optimizer, feed_dict={X: x, Y: y})

        //显示迭代过程
        if (epoch+1) % display_step == 0:
            c = sess.run(cost, feed_dict={X: train_X, Y:train_Y})
            print "Epoch:", '%04d' % (epoch+1), "cost=", "{:.9f}".format(c), \
                "W=", sess.run(W), "b=", sess.run(b)

    print "Optimization Finished!"
    training_cost = sess.run(cost, feed_dict={X: train_X, Y: train_Y})
    print "Training cost=", training_cost, "W=", sess.run(W), "b=", sess.run(b), '\n'

    //绘图
    plt.plot(train_X, train_Y, 'ro', label='Original data')
    plt.plot(train_X, sess.run(W) * train_X + sess.run(b), label='Fitted line')
    plt.legend()
    plt.show()

```

![](/img/2017-03-25/regression.jpg)

## 感想&后续目标

看了一下TensorFlow一些公开数据集的结果排行(主要是图像识别方面的)，感受到这个社区的确是非常活跃。“我想做有意义的工作”，可能最实际的就是把深度学习恰当的运用到更广的领域吧。个人认为，深度学习最新颖的地方在于"计算机运行能力的突破带来处理问题的新方式"。当然，尽管上世纪50年代约翰麦肯锡大叔那不被人理解的心愿似乎已经成为一种主流了，它的基础还是优化、线性代数、概率论。此外抛开课题不谈，我对网络和建模非常感兴趣(前者这里多指社会学和经济学里面的网络)。计算机方面的话相对来说已经算是优势了，"多学一些python吧"。有空梳理一下自己的知识结构，薄弱的基础要补，在这里悄悄感谢一下导师能放我做这个方面的东西，嘻嘻。