---
title: Deep Learning Overview
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

## cuda & gpu
---
* 介绍
	* 目前高端的NVIDIA显卡有Geforce，Quadro，Tesla 三个系列的产品，并且他们都支持 NVIDIA CUDA并行计算平台。 然而 NVIDIA GeForce 和 NVIDIA Quadro 分别是为消费级图形处理和专业可视搜索化而设计的，只有 Tesla 产品系列是完全针对并行计算而设计的，可提供独有的计算特性。由于Tesla系列产品的专业性，所以就注定了它肯定要用在相关的领域，比如：地震处理, 信号与图像处理, 视频分析等对图形运算要求比较高的行业。（来自百度知道）
	* NVIDIA共有两款Titan X，一款叫NVIDIA GeForce GTX Titan X，基于上一代的Maxwell架构，另一款叫NVIDIA Titan X，基于和1080一样的Pascal架构。Pascal的GTX1080吊打NVIDIA GeForce GTX Titan X，领先25％，但比NVIDIA Titan X弱了30％（来自百度知道）

* 显卡安装
	* 参考文章[Ubuntu 14.04 上安装 CUDA 7.5/8.0 超详细教程](http://blog.csdn.net/masa_fish/article/details/51882183)
	* 安装NVIDIA显卡(支持的cudaNN版本)--安装cuda（7.5/8.0等版本）--tf/Theano等
* 配置使用
	* nvidia-smi 查看状态
	* 指定显卡：
		* 在环境变量（系统或者ide）中添加CUDA_VISIBLE_DEVICES=1或者CUDA_VISIBLE_DEVICES=0,2,3
		* 程序中设置`import os os.environ["CUDA_DEVICE_ORDER"]="PCI_BUS_ID" os.environ["CUDA_VISIBLE_DEVICES"]="0"`等


## TensorFlow
---
### 安装
---
* 安装多个python，系统环境变量默认选一个，不用改。每次运行时指定到对应python/bin目录下使用./pip或者./python进行操作。
* 安装anaconda3，在其python位置运行./pip install 各种工具如scipy、tf.whl等。
* 安装tensorflow：使用./pip安装tf.whl，注意tf.whl支持的python版本如cp35/cp34表示py3.5/py3.4，是cpu还是gpu，是linux还是windows，是32位还是x86_64位。安装过程中，少什么就装什么。

### 使用
---

* tensorboard --logdir save/ 查看模型的tensorboard





