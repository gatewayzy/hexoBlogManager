---
title: Deep Learning Networks
comments: true
date: 2017-01-14 13:58:04
updated: 2017-01-14 13:58:56
categories: AI Deep Learning
tags:
- Deep Learning
---

**说明：**Deep Learning 基本知识。
<!-- more -->


参考文章：

## 神经网络 
* 必看文章:
	* [standfor.wiki-神经网络](http://deeplearning.stanford.edu/wiki/index.php/%E7%A5%9E%E7%BB%8F%E7%BD%91%E7%BB%9C)
	* [standfor.wiki-反向传播算法](http://deeplearning.stanford.edu/wiki/index.php/%E5%8F%8D%E5%90%91%E4%BC%A0%E5%AF%BC%E7%AE%97%E6%B3%95)
* 个人理解：神经网络一般包括Feedforward信号正向传递、BackPropagation误差反向传播的过程。只有一个输入层、隐藏层和输出层的是最简单的网络，每一个结点都是 wx+b + 非线性激活 的网络结构。 多层的话一般又叫Feedforward NN /FNN/前馈网络，又叫BP网络/BackPropagation网络。前馈神经网络一般就是指这种没有闭环或者回路的神经网络。

### 前向反馈与反向传播
* 神经网络分为两个过程：工作信号正向传递、误差信号反向传递。
* **前向传播**：信号正向传播，见[standfor.wiki-神经网络](http://deeplearning.stanford.edu/wiki/index.php/%E7%A5%9E%E7%BB%8F%E7%BD%91%E7%BB%9C)
	* 设定Wij和bi，每一个结点的输出都是上一层相连的所有结点的输出和并进行非线性激活，

```
\begin{align}
a_1^{(2)} &= f(W_{11}^{(1)}x_1 + W_{12}^{(1)} x_2 + W_{13}^{(1)} x_3 + b_1^{(1)})  
a_2^{(2)} &= f(W_{21}^{(1)}x_1 + W_{22}^{(1)} x_2 + W_{23}^{(1)} x_3 + b_2^{(1)})  
a_3^{(2)} &= f(W_{31}^{(1)}x_1 + W_{32}^{(1)} x_2 + W_{33}^{(1)} x_3 + b_3^{(1)})  
h_{W,b}(x) &= a_1^{(3)} =  f(W_{11}^{(2)}a_1^{(2)} + W_{12}^{(2)} a_2^{(2)} + W_{13}^{(2)} a_3^{(2)} + b_1^{(2)})
\end{align}
```

* **反向传播**：误差反向传播，见[standfor.wiki-反向传播算法](http://deeplearning.stanford.edu/wiki/index.php/%E5%8F%8D%E5%90%91%E4%BC%A0%E5%AF%BC%E7%AE%97%E6%B3%95)
	* 一次批量梯度下降法的过程：
		* 每层的w'(w的偏导累计)和b'(b的偏导累计)设置为0
		* 对于1到m个样本：
			* 用BP算法求解loss对各层w和b的偏导值。
			* 计算w的偏导累计w'=w'+ 此时loss对w偏导
			* 计算b的偏导累计b'=b'+ 此时loss对b偏导
		* 更新各层的权重参数：w=w-alpha*[(w'/m) + lamda*w] b=b-alpha*(b'/m).
	* 批量梯度下降法应该是说：对一批训练样本，先设置所有的w和b都是随机初始化以实现对称失效。然后对这批的每个样本，求出它产生的loss对所有w和b的偏导，并用一个w'和b'累计这批数据的偏导。然后将这批的偏导之和更新到所有的w和b：w-表示梯度下降，w'/m是累计误差求均值，`lamda*w`可用于动量因子以加速下降；b的更新类似，只是更简单。训练多批数据之后就可以让模型的loss尽可能小。
	* BP算法求解Loss对各层w和b的偏导，前向传导算法：
		* 对每批数据求解输出的loss函数，由m个训练数据的方差+正则化项构成，强烈建议看原文，公式不好写。Loss函数如下，强烈建议查看原文的后续推导过程[standfor.wiki-反向传播算法](http://deeplearning.stanford.edu/wiki/index.php/%E5%8F%8D%E5%90%91%E4%BC%A0%E5%AF%BC%E7%AE%97%E6%B3%95)。

![](http://deeplearning.stanford.edu/wiki/images/math/4/5/3/4539f5f00edca977011089b902670513.png)

### 总结
* 如果一个BP网络只有单一的输入层，隐含层和输出层，一般可以选取隐含层节点数为m=sqrt(in+out)+(0~10) 或者 m=sqrt(in*out) 或者 m=log_2^out。
* 1989年Robert证明了对于任何闭区间内的一个连续函数都可以用一个隐含层的BP网络来逼近，这就是万能逼近定理。
* 单层网络无法解决XOR异或这种非线性可分问题，为此使用多层网络，并对每一层使用非线性激活函数。非线性激活函数的目的在于：引入非线性，如果每层都是线性的，如W1W2X=WX仍是线性，引入非线性才能逼近各种线性和非线性的函数。
* BP神经网络的特点：
	* 存在局部极小值：多层BP网络的误差曲面可能有多个局部极小值，导致梯度下降陷入局部极小值。缓解方法：梯度下降增加动量因子，构成冲量项；多次使用不同的初始权值w和阈值b。
	* 权值过多：层数越多，权值w和b就越多，可能导致训练很慢，产生过拟合。缓解方法：使用动量项加速梯度下降。但是隐藏层的选择没有科学的依据。
* BP神经网络属于简单的网络，拓展的网络有卷积神经网络CNN，深度神经网络，脉冲神经网络(Spiking Neural Networks，SNNs)等。尤其是脉冲神经网络被称为第三代神经网络。

