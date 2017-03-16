---
title: Deep Learning简介
comments: true
date: 2017-01-14 13:58:04
updated: 2017-01-14 13:58:56
categories: Deep Learning
tags:
- Deep Learning
---

**说明：**Deep Learning简介。
<!-- more -->


参考文章：

## 深度学习理论模型
---
### 参考文章

* [CNN(卷积神经网络)、RNN(循环神经网络)、DNN(深度神经网络)的内部网络结构有什么区别？](http://www.toutiao.com/i6316989504984973825/?tt_from=mobile_qq&utm_campaign=client_share&app=news_article&utm_source=mobile_qq&iid=6168746227&utm_medium=toutiao_ios)


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
	1. TensorFlow、theano属于底层data flow，属于陈述式的（declarative），所以学习成本高，可修改性高。
	* Caffe、Torch、MXNet、Keras等属于高层，属于命令式的，所以直接调用使用简单，但是不易修改调参。
2. 流行性
	1. Theano是Bengio和GoodFellow写的，出现早，在学界广泛。keras等框架是基于Theano等封装来的。
	* TensorFlow出现晚，有微软支持，使用更广泛，GoodFellow也去了TF。职业开发推荐用TF。一种说法是Theano和TF属于数据流架构，算不上真正的深度学习框架。
	* Caffe原本是进行图像卷积的，但是后来也支持更多网络。优点是python交互界面漂亮，但是安装麻烦，缺乏商业支持。
	* Torch基于Lua（Facebook等在用），MXnet基于R（亚马逊等在用），他们也在提供更多语言的接口。
3. 其他内容
	1. SciKit-learn是python的一个机器学习库，又叫sklearn，实现了一些回归、聚类等算法，类似于java的weka。

## Neuroph
---
###  参考文章
* [neuroph.sourceforge](http://neuroph.sourceforge.net/)
* [Neuroph Studio手册](http://neuroph.sourceforge.net/Getting%20Started%20with%20Neuroph%202.7.pdf)

### 简介
java下的一款轻量级神经网络工具。支持GUI操作的Neuroph Studio，或者使用java编写程序。

## 自然语言处理与神经网络
---
### 参考文章
* [最新自然语言处理(NLP)四步流程：Embed->Encode->Attend->Predict](http://www.toutiao.com/i6353561267763954178/?tt_from=mobile_qq&utm_campaign=client_share&app=news_article&utm_source=mobile_qq&iid=6168746227&utm_medium=toutiao_ios)
* [人工神经网络](http://www.toutiao.com/i6353823576730108418/?tt_from=mobile_qq&utm_campaign=client_share&app=news_article&utm_source=mobile_qq&iid=6168746227&utm_medium=toutiao_ios)

