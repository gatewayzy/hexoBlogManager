---
title: Deep Learning Tools
comments: true
date: 2017-01-14 13:58:04
updated: 2017-01-14 13:58:56
categories: AI Deep Learning
tags:
- Deep Learning
---

**说明：**Deep Learning 编程框架。
<!-- more -->


参考文章：


## 深度学习开源框架
---
### 参考文章
* [十个值得一试的开源深度学习框架](http://www.cnblogs.com/bluestorm/p/5328585.html)
* [深度学习框架的评估与比较](http://www.infoq.com/cn/news/2016/01/evaluation-comparison-deep-learn?utm_campaign=infoq_content&)


|学习框架|开发语言|接口语言|特点|支持模型|部署环境|
|:---:|:---:|:---:|:---:|
|Caffe	|C++/cuda	|C++/python	|稳定	|CNN	|win/linux |
|TensorFlow	|C++/cuda/python	|C++/python	|	|CNN/RNN/…	|linux	|
|MXNet	|C++/cuda	|python/R/Julia	|可以自己折腾	|CNN/RNN	|win/linux	|
|CNTK	|C++	|C++	|	|	|win/linux	|
|Marvin	|C++	|	|	|	|	|
|Theano	|Python	|Python	|派生出Blocks和Keras等框架	|	|win/linux	|
|Torch	|Lua	|Lua	|	|	|linux	|
|Deeplearning4j	|Java	|	|可与Hadoop和Spark集成，即插即用	|	|	|

在单GPU的场景下，多数工具集都调用了CuDNN。CuDNN(CUDA Deep Neural Network library)是Nvidia显卡专门针对Deep Learning框架设计的一套GPU计算加速方案，目前支持的DL库包括Caffe, TensorFlow, Theano, Torch, CNTK等。

## 框架对比
---
1. 封装层次
	* TensorFlow、theano属于底层data flow，属于陈述式的（declarative），所以学习成本高，可修改性高。
	* Caffe、Torch、MXNet、Keras等属于高层，属于命令式的，所以直接调用使用简单，但是不易修改调参。
2. 流行性
	1. Theano是Bengio和GoodFellow写的，出现早，在学界广泛。keras等框架是基于Theano等封装来的。
	* TensorFlow出现晚，有谷歌支持，使用更广泛，GoodFellow也去了TF。职业开发推荐用TF。一种说法是Theano和TF属于数据流架构，算不上真正的深度学习框架。
	* Caffe原本是进行图像卷积的，但是后来也支持更多网络。优点是python交互界面漂亮，但是安装麻烦，缺乏商业支持。
	* Torch基于Lua（Facebook等在用），MXnet基于R（亚马逊等在用），他们也在提供更多语言的接口。Pytorch也非常流行。
3. 其他内容
	1. SciKit-learn是python的一个机器学习库，又叫sklearn，实现了一些回归、聚类等算法，类似于java的weka。


## TensorFlow
---
### 安装
---
* 安装多个python，系统环境变量默认选一个，不用改。每次运行时指定到对应python/bin目录下使用./pip或者./python进行操作。
* 安装anaconda3，在其python位置运行./pip install 各种工具如scipy、tf.whl等。
* 安装tensorflow：使用./pip安装tf.whl，注意tf.whl支持的python版本如cp35/cp34表示py3.5/py3.4，是cpu还是gpu，是linux还是windows，是32位还是x86_64位。安装过程中，少什么就装什么。

### 使用
---
* [TensorFlow 官方文档中文版](http://docs.pythontab.com/tensorflow/)
* [TensorFlow 官方文档中文版-wiki](http://wiki.jikexueyuan.com/project/tensorflow-zh/)
* tensorboard --logdir save/ 查看模型的tensorboard

## Keras
---
### 安装


### 使用
---
* [Keras中文文档](https://keras-cn.readthedocs.io/en/latest/)





