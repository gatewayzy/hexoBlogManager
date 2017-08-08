---
title: Deep Learning Hardwares
comments: true
date: 2017-01-14 13:58:04
updated: 2017-01-14 13:58:56
categories: AI Deep Learning
tags:
- Deep Learning
---

**说明：**Deep Learning 服务器、显卡等相关硬件。
<!-- more -->


参考文章：

## 服务器与工作站
### Amax服务器
* 苏州超集Amax公司。
* 2017年深度学习服务器更新DGX系列，DGC-1V要80万，据说相当于250台服务器，支持8卡最新的P100显卡。
* 工作站DevMax-400,401都支持4卡水冷，不到10万单价，满卡401需要99K。

### Dell服务器
* Dell PowerEdge系列：T630 塔式服务器，R720、R730 2U机架式服务器。

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
		* 2017年看到V100，比之前的M40、P100更新。
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
