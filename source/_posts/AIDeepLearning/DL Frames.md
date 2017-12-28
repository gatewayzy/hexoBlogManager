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
|Torch	|Lua	|Lua/Python	|	|	|linux	|
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
### tf安装过程
* 一般是先安装显卡、安装显卡驱动，安装cuda，安装cudnn(tf要求必须安装)，安装anaconda(python)，安装tensorflow。
* 注意cuda、cudnn、tf、python之间的版本关系，版本需要相互支持。比如合适的搭配：cuda8、libcudnn6_cuda8、python3.6、tf1.3.0。比较新的比如tf1.4可能就对py3.6支持上不是特别好，所以版本一般不要选最新的。

### 安装tf
---
* 安装多个python，系统环境变量默认选一个，不用改。每次运行时指定到对应python/bin目录下使用./pip或者./python进行操作。
* 安装anaconda3，在其python位置运行./pip install 各种工具如scipy、tf.whl等。
* 安装tensorflow：使用./pip安装tf.whl，注意tf.whl支持的python版本如cp35/cp34表示py3.5/py3.4，是cpu还是gpu，是linux还是windows，是32位还是x86_64位。安装过程中，少什么就装什么。

* tf中文官网：<https://www.tensorflow.org/install/install_linux>

#### Windows使用pip简单
`pip3 search tensorflow` 查看到的`tensorflow`是cpu版本，`tensorflow-gpu`是gpu版本，`tf-nightly`和`tf-nightly-gpu`是nightly版本。但是windows下只能装64位且py3.5+的tf。


#### Linux下载whl包
* 中文官网上有各种介绍，下载也比较快。下载whl包网站：https://pypi.python.org/pypi/tensorflow-gpu
* 确定python版本，确定cpu/gpu，确定tf版本，访问下面网站即可。

* CPU support for linux cpu py3.6.0 tf1.4.0
    https://storage.googleapis.com/tensorflow/linux/cpu/tensorflow-1.4.0-cp36-cp36m-linux_x86_64.whl

* GPU support for linux gpu py3.6.0 tf1.4.0
    https://storage.googleapis.com/tensorflow/linux/gpu/tensorflow_gpu-1.4.0-cp36-cp36m-linux_x86_64.whl

### 使用
---
* [TensorFlow 官方文档中文版](http://docs.pythontab.com/tensorflow/)
* [TensorFlow 官方文档中文版-wiki](http://wiki.jikexueyuan.com/project/tensorflow-zh/)
* tensorboard --logdir save/ 查看模型的tensorboard

## Keras
---
* [Keras中文文档](https://keras-cn.readthedocs.io/en/latest/)

---
## 数据集
### train/dev/test
* 训练集 train ：主要是用来训练模型的。
* 开发集 develop：主要用于模型参数调优。
* 验证集 validate：模型的最终优化和最终确定，比如选取效果最好的模型。
* 测试集 test：用于测试模型的泛化能力。


### batch、iteration、epoch、minibatch
* batch、iteration、epoch、minibatch
    * 不同的人叫法不同：每次SGD称为一个iteration(迭代，更新参数)，但是对于full training data和每次SGD使用的data不同的人称为：epoch/batch、batch/minibatch、epoch/minibatch。反正就是epoch指最大的，如果minibatch指最小的，batch视情况而定。
    * 拿epoch/batch举例：
        * epoch：训练集中的所有样本都训练完成一次称为一个epoch。
        * batch：在一次SGD中，使用训练集中的batchsize个样本，称为batch。
        * 举例：训练集有1000个样本，选取batchsize=10，那么训练完一次整个样本需要100次iteration，1epoch。
---
## layer
###  conv2d
* 二维卷积操作，输入的两个维度和channel，输出的两个维度和channel。卷积优点在于：参数共享、窗口稀疏(filter之外的稀疏为0)等。
* 卷积的核称为kernel、filter、patch.
* conv2d的参数解释：tf.nn.conv2d(input, filter, strides, padding, use_cudnn_on_gpu=None, name=None)
    * 第一个参数input：指需要做卷积的输入图像，它要求是一个Tensor，具有`[batch, in_height, in_width, in_channels]`这样的shape，具体含义是[训练时一个batch的图片数量, 图片高度, 图片宽度, 图像通道数]，注意这是一个4维的Tensor，要求类型为float32和float64其中之一
    * 第二个参数filter：相当于CNN中的卷积核，它要求是一个Tensor，具有`[filter_height, filter_width, in_channels, out_channels]`这样的shape，具体含义是[卷积核的高度，卷积核的宽度，图像通道数，卷积核个数]，要求类型与参数input相同,filter的通道数要求与input的`in_channels`一致，有一个地方需要注意，第三维`in_channels`，就是参数input的第四维
    * 第三个参数strides：卷积时在图像每一维的步长，这是一个一维的向量，长度4，具有`[1, strides[1], strides[2], 1]`，strides[0]=strides[3]=1
    * 第四个参数padding：string类型的量，只能是"SAME","VALID"其中之一，这个值决定了不同的卷积方式。
    * 第五个参数：`use_cudnn_on_gpu`:bool类型，是否使用cudnn加速，默认为true
    * conv2d结果返回一个Tensor，这个输出，就是我们常说的feature map，尺寸为`[batch, out_height, out_width, out_channels]` 比长宽in，长宽in out，1s长s宽1，比长宽out
        * SAME padding有：`out_height = ceil(float(in_height) / float(strides[1]))` 长/stride长 `out_width = ceil(float(in_width) / float(strides[2]))` 宽/stride宽
        * VALID padding有：`out_height = ceil(float(in_height - filter_height + 1) / float(strides[1]))`(长-长+1)/stride长 `out_width = ceil(float(in_width - filter_width + 1) / float(strides[2]))`(宽-宽+1)/stride宽
* Pooling：将多个维度的数值进行合并，降维降噪，防止过拟合。常见`max_pooing`、`average_pooling`、`min_pooling`。
    * tf.nn.max_pool(input, ksize, strides, padding )
        * input是输入，形状为`[batch_size, height, width, channels]`，
        * ksize是kernel_size，形状为`[1, k_height, k_width, 1]`
        * strides是步长，形状为`[1, stride[1], stride[2], 1]`
        * padding是补全方式，有VALID和SAME，
        * 输出是tensor，形状为`[batch_size, feature_height, fearure_width, channels]`。使用VALID-padding时，输出的大小与conv2d计算类似，`out_height = ceil(float(in_height - filter_height + 1) / float(strides[1]))`
* Dropout：随机断开目标网络的连接，随机断开的机率是1-keep_prob。训练时进行dropout以防止训练过拟合，测试和预测时关闭dropout，因为模型已经定型了。



## loss
* `cross_entropy_loss` 交叉熵 ：适用于已知真实分布的情况下，评估预测的效果。比如已知图片训练集的分类，使用交叉熵计算预测的结果的熵。
* `nce_loss` 噪声对比估计损失函数(noise-contrastive estimation, NCE)
* `sampled_softmax_loss`

## Gradient Descent GD梯度下降

* [batch-GD,Mini-batch-GD,SGD,Online-GD 大数据背景下的梯度训练算法](http://www.cnblogs.com/richqian/p/4549590.html)
* 常见optimizer优化器：
    * Adam 快速，常用
    * Batch GD：计算loss函数在整个训练集上的梯度方向，沿着该方向进行迭代。缺点：大数据集上复杂度过高。
    * Mini-batch GD：每次选取训练集的子集进行训练。较为常用。
    * Stochastic GD：SGD随机梯度下降，每次只选一个数据进行训练。优点：速度比较快。缺点：收敛性不好，可能引起目标函数剧烈震荡，可能hit不到最优点。
    * Online GD：所有训练数据只使用1次，然后丢弃。优点：利用实时数据，可以得到模型的变化趋势。


|梯度下降算法|batch gd|mini-batch gd|stochastic gd|online gd|
|:---:   |:---:   |:---:   |:---:   |:---:   |
|训练集   |固定   |固定   |固定   |实时更新   |
|单次迭代样本数   |整个训练集   |训练集的子集   |单个样本   |根据具体算法定   |
|算法复杂度   |高   |一般   |低   |低   |
|时效性   |低   |一般(delta模型)   |一般(delta模型)   |高   |
|收敛性   |稳定   |稳定   |不稳定   |不稳定   |


* SGD 随机梯度下降，大数据集用不错。SGD改进，使用动量。
L-BFGS 在小数据集上不错，改进的Large-batch L-BFGS不错。
Conjugate Gradients 也在小数据集也不错。
Mini-batch GD 一般选20-1000大。

## activation

### 常见激活函数
* ReLU (Rectified Linear Unit) ：用于隐层神经元输出 `relu(x)=max(0,x)` 优点：计算简单；单侧抑制；兴奋边界更宽；稀疏激活性。
	* 优点：
	* 缺点：没有进行数据压缩，数据范围可能很大。很容易改变数据的分布。
    * 改进：
	    * Leaky ReLU：在小于0的部分不直接设置为0，而是斜率较小的曲线，如y=-0.01x。
	    * pReLU、random ReLU等，ReLU后面加上Batch Normalization优化数据分布。
* Sigmoid：用于隐层神经元输出 `sigmoid(x)=1/(1+e^(-x))` 缺点：饱和区导数趋近0，容易梯度消失；
* Softmax：用于多分类神经网络输出 `softmax(x)=e^x/sum_i(e^(x_i)))` 计算各个类别的可能性，进行概率归一化，概率高的为判断依据。只用于多于一个输出的神经元.
* tanh：`tanh(x)=(1-e^(-x))/(1+e^(-x))` 计算复杂。
* SeLU：用于隐层神经元输出 论文指出能保证分布为`均值为0，方差为1`，所以梯度更不易消失，支持更深网络，效果更好。

### 激活函数的 Normalization
* 1502.03167 Batch normalization论文指出，尽可能保证每一层的输入有相同的分布。因此在activation之后经常进行batch normalization(selu有相似功能，见snn论文)

* Batch normalization
* Layer normalization
* Weight normalization


## 问题与解决
### libcudnn找不到
* 解决：编辑~/.bashrc，添加`export LD_LIBRARY_PATH=/usr/local/cuda/lib64/`，主要就是由于cuda虽然安装，但是没有添加到环境变量。一般cuda是链接到cuda-8.0文件夹这种形式，用于实现cuda通用版本链接。

### tf版本导致的一些问题
* 由于tf1.0之后的版本中，对很多方法进行了重新梳理和调整，代码上需要一些修改，比如：

```
    * tf.concat()参数顺序与老版本相反
    * tf.mul()变成tf.multiply()
    * tf.sub()变成tf.subtract()
    * tf.initialize_all_variables() 改为：tf.global_variables_initializer()
    * tf.all_variables() 改成 tf.global_variables()
    * summary接口也改变很多：
    * tf.histogram_summary 改为：tf.summary.histogram
    * tf.scalar_summary 改为：tf.summary.scalar
    * tf.train.SummaryWriter 改为：tf.summary.FileWriter
    * tf.merge_all_summaries() 改为：summary_op = tf.summary.merge_all()
    * tf.merge_summaries() 改为：summary_op = tf.summary.merge()
```

### tf的summaryWriter提示check failed size(2 VS 1)
* 我是因为batch_size太大，导致GPU溢出，从100改成64就可以了。
