---
title: Deep Learning Hardwares And Drivers
comments: true
date: 2017-01-14 13:58:04
updated: 2017-01-14 13:58:56
categories: AI Deep Learning
tags:
- Deep Learning
---

**说明：**Deep Learning 服务器、显卡等硬件，以及CUDA，CUDNN介绍等。
<!-- more -->


参考文章：

----
## 服务器与工作站
### Amax服务器
* 苏州超集Amax公司。
* 2017年深度学习服务器更新DGX系列，DGC-1V要80万，据说相当于250台服务器，支持8卡最新的P100显卡。
* 工作站DevMax-400,401都支持4卡水冷，不到10万单价，满卡401需要99K。

### Dell服务器
* Dell PowerEdge系列：T630 塔式服务器，R720、R730 2U机架式服务器，但是720xd和730xd不支持GPU。

---
## GPU 显卡
### 显卡分类与基本交火
* 显卡类型：集成显卡、核心显卡、独立显卡。总体上，独显优于核显优于集显，这三者功耗依次降低，但是性能对应下降。
* 混合交火和SLI
	* CrossFire(CF)混合交火，是AMD公司旗下ATI公司（ATI Technologies Inc冶天科技）技术，后来免费授权，一般主板都支持CF。（A卡）
	* SLI可升级连接接口（Scalable Link Interface），是Nvidia公司技术。SLI需要收费授权，主板不一定支持SLI。增加一个显卡，性能提升不足单卡的80%，有的甚至不升反降，性价比比不上CrossFire。（N卡）
	* 二者实际差不多，普通人都称为交火。交火最好是相同的显卡，或者同一厂家的同一系列产品。

### Nvidia 英伟达显卡

参考链接：
* [Nvidia developer官网](https://developer.nvidia.com/)
* [cuda文档API](http://docs.nvidia.com/cuda/index.html)
* [cuda-gpus 列表](https://developer.nvidia.com/cuda-gpus)
* [cuda wikipedia](https://en.wikipedia.org/wiki/CUDA#Limitations)

目前常用的NVIDIA独立显卡有Geforce，Quadro，Tesla 等系列的产品，都支持 NVIDIA CUDA并行计算平台。 NVIDIA GeForce（精视）和 NVIDIA Quadro 分别是为消费级图形处理和专业可视搜索化而设计的，Tesla 产品系列是完全针对并行计算而设计的，可提供独有的计算特性。由于Tesla系列产品的专业性，常用在地震处理, 信号与图像处理, 视频分析等对图形运算要求比较高的行业。
* **Geforce系列** 考虑到性价比和性能需求，常用Geforce系列，Geforce包括GeForce GTX系列和NVIDIA TITAN系列。
	* **GeForce GTX系列**：比如GTX 970，GTX 980，GTX 1080，GTX1080 Ti等。早期产品基于Maxwell核心架构，新一代的核心架构是Pascal，当然Pascal架构的比Maxwell的高一代。
	* **NVIDIA TITAN系列**：
		* 常见产品：早期的GeForce GTX TITAN（白泰坦）、GeForce GTX TITAN Black（黑泰坦）、GeForce GTX TITAN Z、GeForce GTX TITAN X。之后命名系列去掉GeForce GTX ，改为NVIDIA TITAN X、NVIDIA TITAN Xp等。Compute Capability参数指的是version，一般version越高，并行计算能力越强。
		* 两个型号的Titan X？早期的 `NVIDIA GeForce GTX Titan X` 和后期的 `NVIDIA Titan X` 名称上都包含Titan X。但是早期的NVIDIA GeForce GTX Titan X基于上一代的Maxwell架构，NVIDIA Titan X基于新一代的Pascal架构。NVIDIA Titan X领先GTX 1080（基于Pascal） 30％，GTX 1080领先GTX Titan X 25％（来自百度知道）。
		* GTX Titan Z和GTX Titan X：GTX Titan Z 是内置的2路SLI，所以是单卡双核。gtx titan x是单卡单核。titan z早于 gtx titan x，titan z的功耗更大，性价比不是很高。多路SLI需要支持多路SLI的PCI-E Xx的主板或者服务器AMAX等，常见两路、三路、四路SLI，如一个电脑加四个gtx titan x构成四路SLI（四卡SLI），两个titan z构成两路SLI（两卡SLI），都能构成四核芯GPU。一般Nvidia最大支持4核芯，4卡SLI性能并不高，常用双卡SLI或者三卡SLI性能最高。
* **Tesla系列**
	* 适用于专业领域，如Tesla K20/K40/K80等适合双精度要求高的场合，Tesla M40等适合单精度计算快，适合只训练深度学习网络。2017年有Tesla V100，比之前的M40、P100更强大。

### 显卡参数
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

---
## 显卡配置(N卡为例)
* 参考文章[Ubuntu 14.04 上安装 CUDA 7.5/8.0 超详细教程](http://blog.csdn.net/masa_fish/article/details/51882183)
* 配置深度学习环境的基本流程大致有以下几步，详细的安装建议查看
	* 安装NVIDIA显卡硬件，比如Nvidia Titan X
	* 安装CUDA，全称CUDA(Compute Unified Device Architecture)，这是Nvidia公司为N卡开发的并行计算架构，这个一定要的。安装时主要参考[官网的CUDA安装文档](http://docs.nvidia.com/cuda/)，网上一大堆的教程说不定不行。
	* 安装CUDNN，用于支持NN加速的工具包，不一定需要安装，网上也有一大堆教程，还是以[官网的CUDNN说明](https://developer.nvidia.com/cudnn)为主。
	* 安装tf和pytorch等框架。
* 配置使用
	* 运行`nvidia-smi -a` 查看N卡资源和运行状态
	* 指定程序使用的显卡，几种方法：
		* 在环境变量（系统或者pycharm等IDE）中添加CUDA_VISIBLE_DEVICES=1或者CUDA_VISIBLE_DEVICES=0,2,3
		* 程序中设置`import os os.environ["CUDA_DEVICE_ORDER"]="PCI_BUS_ID" os.environ["CUDA_VISIBLE_DEVICES"]="0"`等

### 安装cuda
1. 打开[cuda安装官网](https://developer.nvidia.com/cuda-downloads?target_os=Linux&target_arch=x86_64&target_distro=Ubuntu&target_version=1604&target_type=deblocal)，选择自己系统对应的版本，下载安装文件，如1.2G的deb文件。
2. 在服务器上按照官网的命令进行安装，基本流程就是添加本地文件源，然后安装，命令如下。安装过程会默认在/usr/local中创建cuda链接，链接到cuda-8.0具体版本。

```
sudo dpkg -i cuda-repo-ubuntu1604-9-0-local_9.0.176-1_amd64.deb
sudo apt-key add /var/cuda-repo-<version>/7fa2af80.pub
sudo apt-get update
sudo apt-get install cuda
```
3. 测试安装状态，运行nvidia-smi查看显卡状态，如果有错误提示Failed to initialize NVML: Driver/library version mismatch，可以尝试重启解决。

### 安装cudnn
安装好cuda，可以进行nvidia-smi查看显卡状态，但是tf还需要使用cudnn加速，theano等可能就不一定需要。所以一般还需要再安装cudnn。
1. 打开[cudnn下载官网](https://developer.nvidia.com/rdp/cudnn-download)，需要注册邮箱账号，登录选择cudnn相应的版本，下载即可。
2. 进行安装，比如deb包，使用`sudo dpkg -i xx.deb`即可。需要将安装后产生的libcudnn.so.6/7/8文件放到/usr/local/cuda/lib64下，如果找不到，可以全盘搜索文件`find / -name libcudnn*`，我的是被安装到`/usr/lib/x86_64-linux-gnu`，拷贝到/usr/local/cuda/lib64/下面并修改可执行权限，或者添加链接`sudo ln -s libcudnn.so.5.1 libcudnn.so`
3. 运行命令`nvcc`，需要确定libcudnn已经在path中，默认是不会添加到path的，不影响tf使用。如果要添加，可以修改.bashrc文件，添加

```
export CUDNN_HOME=/usr/local/cuda
export LD_LIBRARY_PATH=${CUDNN_HOME}/lib64:$LD_LIBRARY_PATH
export CPLUS_INCLUDE_PATH=${CUDNN_HOME}/include:$CPLUS_INCLUDE_PATH
```
* 注意cuda、cudnn、tf、python之间的版本关系，三者需要相互支持。比如合适的搭配：cuda8、libcudnn6_cuda8、tf1.3.0、python3.6。