---
title: Deep Learning Overview
comments: true
date: 2017-01-14 13:58:04
updated: 2017-01-14 13:58:56
categories: AI Deep Learning
tags:
- Deep Learning
---

**说明：**Deep Learning简介。
<!-- more -->


参考文章：

## 深度学习理论模型
---
### 参考文章

* [CNN(卷积神经网络)、RNN(循环神经网络)、DNN(深度神经网络)的内部网络结构有什么区别？](http://www.toutiao.com/i6316989504984973825/)


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
	* TensorFlow出现晚，有谷歌支持，使用更广泛，GoodFellow也去了TF。职业开发推荐用TF。一种说法是Theano和TF属于数据流架构，算不上真正的深度学习框架。
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


## GPU 显卡
---
* 显卡类型：集成显卡、核心显卡、独立显卡
* 混合交火和SLI
	* CrossFire(CF)混合交火，是AMD公司旗下ATI公司（ATI Technologies Inc冶天科技）技术，后来免费授权，一般主板都支持CF。（A卡）
	* SLI可升级连接接口（Scalable Link Interface），是Nvidia公司技术。SLI需要收费授权，主板不一定支持SLI。增加一个显卡，性能提升不足单卡的80%，有的甚至不升反降，性价比比不上CrossFire。（N卡）
	* 二者实际差不多，普通人都称为交火。交火最好是相同的显卡，或者同一厂家的同一系列产品。

### Nvidia 英伟达显卡
---
* 参考链接：
	* [Nvidia developer官网](https://developer.nvidia.com/)
	* [cuda文档API](http://docs.nvidia.com/cuda/index.html)
	* [cuda-gpus 列表](https://developer.nvidia.com/cuda-gpus)
	* [cuda wikipedia](https://en.wikipedia.org/wiki/CUDA#Limitations)
* 显卡介绍 (独显)
	* 目前高端的NVIDIA显卡有Geforce，Quadro，Tesla 等系列的产品，都支持 NVIDIA CUDA并行计算平台。 NVIDIA GeForce（精视）和 NVIDIA Quadro 分别是为消费级图形处理和专业可视搜索化而设计的，Tesla 产品系列是完全针对并行计算而设计的，可提供独有的计算特性。由于Tesla系列产品的专业性，常用在地震处理, 信号与图像处理, 视频分析等对图形运算要求比较高的行业。
	* Geforce系列
		* 考虑到性价比和性能需求，常用Geforce系列，Geforce包括GeForce GTX系列和NVIDIA TITAN系列。
		* GeForce GTX系列：比如GTX 970，GTX 980，GTX 1080，GTX1080 Ti等。早期产品基于Maxwell核心架构，新一代的核心架构是Pascal，当然Pascal架构的比Maxwell的高一代。
		* NVIDIA TITAN系列：
			* 常见产品：早期的GeForce GTX TITAN（白泰坦）、GeForce GTX TITAN Black（黑泰坦）、GeForce GTX TITAN Z、GeForce GTX TITAN X。之后命名系列去掉GeForce GTX ，改为NVIDIA TITAN X、NVIDIA TITAN Xp等。Compute Capability参数指的是version，一般version越高，并行计算能力越强。
			* 两个Titan X？早期的NVIDIA GeForce GTX Titan X和后期的NVIDIA Titan X名称上都包含Titan X。但是早期的NVIDIA GeForce GTX Titan X基于上一代的Maxwell架构，NVIDIA Titan X基于新一代的Pascal架构。NVIDIA Titan X领先GTX 1080（基于Pascal） 30％，GTX 1080领先GTX Titan X 25％（来自百度知道）。
			* gtx titan z是内置的2路SLI，所以是单卡双核。gtx titan x是单卡单核。titan z早于 gtx titan x，titan z的功耗更大，性价比不是很高。多路SLI需要支持多路SLI的PCI-E Xx的主板或者服务器AMAX等，常见两路、三路、四路SLI，如一个电脑加四个gtx titan x构成四路SLI（四卡SLI），两个titan z构成两路SLI（两卡SLI），都能构成四核芯GPU。一般Nvidia最大支持4核芯，4卡SLI性能并不高，常用双卡SLI或者三卡SLI性能最高。
	* Tesla系列
		* 适用于专业领域，如Tesla K20/K40/K80等适合双精度要求高的场合，Tesla M40等适合单精度计算快，适合只训练深度学习网络。
	* 显卡参数
		* 核心和显存。核心是决定性的，显存对性能有辅助作用。
		* 核心： 核心型号和核心参数。常见参数有：
			* 制作工艺：55nm，40纳米等，越小越先进，频率更高。
			* 核心频率：700MHz等。
			* 流处理器数量：48,800等，越多越强。不过，N卡和A卡架构不同，相同性能的两个核心，A卡的流处理器数量是N卡的4-5倍，所以两家不能比较流处理器数量。
			* 流处理器频率：1400MHZ、700MHZ等等，频率越高，性能越强。不过，A卡和N卡架构有区别，N卡的流处理器频率一般是核心频率的两倍以上，而A卡的流处理器频率则与核心频率相同。
		* 显存：决定了数据大小和吞吐量，常见参数有：
			* 显存容量：256M，1G，12G等，决定能缓存的容量大小。
			* 显存频率：DDR2、DDR3、GDDR3、GDDR5等几个类型，GDDR5的频率最高，等效频率能达到4GHZ以上。
			* 显存位宽：64bit、256bit、512bit等几种。位宽越大，制造难度就越大，成本也越高，所以很多厂商宁可选择低位宽与高频率的组合，这样在保证性能的同时还能降低成本。
			* 显存带宽=显存频率*显存位宽/8（B/s），表示GPU核心与显存之间数据传输速率。
		* 不同型号的核心，参数也不同，比如GT240，官方默认是96个流处理器、40NM工艺、550MHZ核心频率、1340MHZ流处理器频率、3400MHZ显存频率、128显存位宽。而实现产品中，按上面这些官方参数生产的显卡，称之为公版显卡，而达不到这些官方参数的显卡，我们就称之为缩水版显卡，而超出官方参数的显卡，我们就称之为超公版显卡。
		* 显卡大小：有全长半长、全高半高之分，full/half length, full/half height，半高显卡也叫刀卡。GeForce系列以全长全高为主，如Nvidia高4.376''(inch)，长10.5''(inch)，高度约合11.1cm。Tesla有半高产品，支持2U(约8.89cm)机架式服务器，如[Tesla支持服务器](http://www.nvidia.cn/object/where-to-buy-tesla-cn.html)。机架式服务器Rack server(1U,2U等)，塔式服务器Tower server，刀片式服务器(高密度组装)。

* 显卡安装
	* 参考文章[Ubuntu 14.04 上安装 CUDA 7.5/8.0 超详细教程](http://blog.csdn.net/masa_fish/article/details/51882183)
	* 安装NVIDIA显卡(支持的cudaNN版本)--安装cuda（7.5/8.0等版本）--tf/Theano等
* 配置使用
	* nvidia-smi -a 查看N卡资源和状态
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
* [TensorFlow 官方文档中文版](http://docs.pythontab.com/tensorflow/)
* [TensorFlow 官方文档中文版-wiki](http://wiki.jikexueyuan.com/project/tensorflow-zh/)
* tensorboard --logdir save/ 查看模型的tensorboard

## Keras
---
### 安装


### 使用
---
* [Keras中文文档](https://keras-cn.readthedocs.io/en/latest/)



