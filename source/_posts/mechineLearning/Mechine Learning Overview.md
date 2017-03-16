---
title: Mechine Learning Overview
comments: true
date: 2017-01-14 13:58:13
updated: 2017-01-14 13:58:56
categories: Mechine Learning
tags:
- Mechine Learning
---

**说明：**Mechine Learning算法简介。
<!-- more -->

---
参考文章：
* 推荐书籍：《统计学习方法》李航

## 机器学习综述
---
* 参考文章
	* [机器学习 综述](http://www.toutiao.com/i6349363835060617729/)
	* [机器学习10大经典算法](http://blog.csdn.net/xxinliu/article/details/7408742)
	* [机器学习与数据挖掘中的十大经典算法](http://www.360doc.com/content/13/0716/16/478627_300397623.shtml)
	* [机器学习常见算法分类汇总](http://www.ctocio.com/hotnews/15919.html)

### 简介
--- 
* 大多数机器学习的算法思想是通过一些样本数据来训练计算机的计算规则，再执行具体的任务。 机器学习在复杂问题中的应用越来越广泛。例如：
	1. 信息安全：识别网络攻击模式；
	2. 图像分析：面部识别、指纹识别；
	3. 大数据和深度学习：市场营销；
	4. 物体识别和对象预测：多传感器数据融合、自动驾驶；
	5. 模式识别：分析代码漏洞……

* 常见的算法有：CART、 C4.5、 SVM、 K-Means、 Apriori、 EM、PageRank、 AdaBoost、kNN、 NaiveBayes等等。

* 机器学习策略主要包括有监督学习（Supervised Learning）和无监督学习（Unsupervised Learning）。
	* 有监督学习中，训练集包含数据和期望输出，根据期望输出学习相关参数。
	* 无监督学习中，训练集只有数据，而没有标签，训练得到稳定状态结束。

### 有监督学习
---
* 有监督学习包括分类、回归等等。
* 分类算法（Classification Algorithms）包括逻辑回归（Logic Regression）、分类树（Classification Tree）、支持向量机（Suport Vector Machines，SVMs）、随机森林（Random Forests）、人工神经网络（Artificial Neural Network，ANNs）等。

* 回归算法（Regression Algorithms）包括线性回归（Linear Regression）、决策树（Decision Trees）、贝叶斯网络（Bayesian Networks）、模糊分类（Fuzzy Classification），以及ANNs。

### 无监督学习
---
* 无监督学习包括聚类、降维等等。
* 聚类算法（Clustering Algorithms），将数据按相关性的远近分为多个簇，最常见的如K-means 聚类，还有分层聚类（Hierarchical Clustering）、高斯混合模型（Gaussian Mixture Models）、遗传算法（Genetic Algorithms）、ANNs。

* 降维算法（Dimensionality Reduction Algorithms）是将原始多维数据映射为低维数据，用更少的数据量来描述原始数据的主要特征。降维算法如主成分分析（Principal Component Analysis，PCA）、张量还原（Tensor Reduction）、多维统计（Multidimensional Statistics）、随机投射（Random Projection）、ANNs。

###  算法分类
---
机器学习主要的算法分类如下。

|- machine learning	|	|	|	|
|:---|:---	|---:	|---:	|
|-- supervised learning	|	|-- unsupervised learning 	|	|
|--- classification	|--- regression	|--- clustering	|--- dimension reduction	|
|logic regression	|linear regression	|k-means clustering	|principal component analysis	|
|classification trees	|decision trees	|hierarchical clustering	|tensor decomposition	|
|support vector machines	|bayesian networks	|gaussian mixture models	|multidimensional statistics	|
|random forests	|fuzzy classification	|genetic algorithms	|random projection	|
|artificital neural networks	|artificial neural networks	|artifical neural networks	|artifical neural networks|


|- 机器学习	|	|	|	|
|:---|:---	|---:	|---:	|
|-- 监督学习	|	|-- 无监督学习 	|	|
|--- 分类算法	|--- 回归算法	|--- 聚类算法	|--- 降维算法	|
|逻辑回归	|线性回归	|k-means聚类	|PCA主成分分析	|
|分类树	|决策树	|层次聚类	|张量还原	|
|SVM支持向量机	|贝叶斯网络	|高斯混合模型	|多维统计	|
|随机森林	|模糊分类	|遗传算法	|随机投射	|
|神经网络	|神经网络	|神经网络	|神经网络|
|……	|……	|……	|……	|


## 模型评估
---
* 评判模型需要测试模型以下数据是否合理：偏差 bias，方差 variance，正确率 precision，查全率 recall
* 参考文章
	* [你的机器学习模型为什么会出错？奉上四大原因解析及五条改进措施](http://www.toutiao.com/i6369826318221050369/)

###  bias variance precision recall
---

* 机器学习模型的设计目标是低偏差、低方差，高准确率、高查全率
	* 在偏差和方差中找到平衡，既能尽量准确，又要能够通用。
	* 在正确率和查全率中找到平衡，既能尽量保证预测都是对的，又能尽可能多做出预测。
	
* 高偏差 high bias：
	* 概念：高偏差指的是模型对某些样本点的输出与实际值相差较大。
	* 原因：模型没训练好，连样本数据都无法准确预测。表现如欠拟合。
	* 改进：增加训练数据。
	
* 高方差 high variance：
	* 概念：高方差表示虽然模型对样本点输出符合预期，但是一旦应用到新的数据就会产生错误。
	* 原因：模型有隐藏的问题，只能对样本数据有效，但是无法正确预测新数据。表现如过拟合。
	* 改进：减少训练数据、增加测试数据。
	
* 低正确率 low precision：
	* 正确率是指模型的所有正向判断（确定为‘是xx’）中，判断正确的比例。正确率反应了一个模型的预测准确度。
	* 计算方法是针对一个正向判断： `模型判断正确的次数 / 模型所有判断次数` ，即 `True Positives / Predicted Positives` = `TP / (TP+FP)`
	* 改进：提高概率阈值（判断更为严苛）。

* 低查全率 low recall：
	* 查全率是指正向判断中正确的次数，占实际正向结果的比例。查全率反应了一个模型的实际应用效果。
	* 计算方法是针对一个正向判断： `模型判断正确的次数 / 实际正向数量` ，即 `True Positives / Actual Positives` = `TP / (TP+FN)`
	* 改进：降低概率阈值（判断更为宽松）。
	
* 正确率与查全率关系举例：
	* 判断邮件是否为垃圾邮件：实际收到了12封邮件，其中9封是正常邮件，3封是垃圾邮件。一个模型一共做了2次正向判断，其中1次是正确的，10次反向判断，其中8次是正确的。那么其正确率就是1/2=50%，而查全率就是1/3=33%。


###  Cross Validation交叉验证
---

* 检测模型是否具有高偏差或者高方差最直接的办法就是对数据进行交叉验证。
* 交叉验证总体思路是：拿出大部分的数据进行训练建模，留一小部分的数据作为测试样本，用刚刚建立的模型进行测试，并评估测试结果。持续进行交叉建模、测试，直到所有的样本数据都恰好被预测了一次为止（交叉思想）。
* 常见的交叉验证方法有很多，例如Holdout验证、k折交叉验证、留一验证等。
	* Hold-Out Method：将原始数据随机分为两组,一组做为训练集,一组做为验证集,进行训练和测试，记录准确率。优点是简单，缺点是只对样本进行随机划分，并不算严格意义的交叉验证。
	* K-fold Cross Validation(k-CV)：将原始数据分成K组(一般是均分),每次将一个子集做测试集，其余子集作为训练集，得到K个模型，用这K个模型准确率综合评估(如取平均数）可以有效避免过拟合和欠拟合。
	* Leave-One-Out Cross Validation(LOO-CV)：每次将一个样本作为验证集，其余样本作为训练集，所以LOO-CV会得到N个模型，用这N个模型的准确率进行综合评估。相比于K-CV,LOO-CV既能保证结果可以被复制而不会受随机因素影响，又能保证每一回合中几乎所有样本都用于训练从而使得模型最接近原始分布。但是LOO-CV缺点在于计算成本过高。



## Dimensionality reduction 降维
---
* high-dimensional vectors 高维数据需要进行降维，方便计算和实现去噪。
* 常见降维方法有：Random Projection、PCA、SVD、LSI等
* 参考论文：
	* Bingham, Ella, and Heikki Mannila. "Random projection in dimensionality reduction: applications to image and text data." Proceedings of the seventh ACM SIGKDD international conference on Knowledge discovery and data mining. ACM, 2001.

### Random Projection 随机映射
---
* 降维步骤：随机生成降维矩阵R，比如将3072维降为200维，则d=200，降维得到X(d,50000)=R(d,3072)X(3072,50000)
* 降维矩阵生成方法：
	* 高斯分布，或者简化为r(i,j)=sqrt(3)*(1/6概率的1，1/6概率的-1，2/3概率的0)，其中乘以sqrt(3)是为了保证每一列的模为1，维持元数据的强度。
	* 随机生成rij，但是要保证每一列是单位长度。

### PCA 主成分分析
---
* 参考文章
	* [主成分分析PCA](http://www.cnblogs.com/zhangchaoyang/articles/2222048.html) 
	* [主成分分析（Principal components analysis）-最大方差解释](http://www.cnblogs.com/jerrylead/archive/2011/04/18/2020209.html)
* 简介
	* PCA全称Principal Components Analysis(主主成分分析)，以前叫Principal factor analysis。可用于降维去噪。
* 数学目标：
	* 一个正交化线性变换，把数据变换到一个新的坐标系统中，使得这一数据的任何投影的第一大方差在第一个坐标（称为第一主成分）上，第二大方差在第二个坐标（第二主成分）上，依次类推。
* 基本方法
	* 实现PCA一般有两种方法：使用特征值分解和使用奇异值分解。原理就是用`Am∗nPm∗k=A˜m∗k`将n维特征映射到较低的k维，即：为了保持数据特征差异，`Pm*k`应该是正交坐标系，相乘后得到`Am*n`降维后的近似结果`A˜m∗k`。
	* 方法1 特征值分解
		* 将A特征中心化。即每一维的数据都减去该维的均值得到B
		* 计算B的协方差矩阵C
		* 计算协方差矩阵C的特征值和特征向量
		* 选取大的特征值对应的特征向量构成矩阵M，得到新的数据集A*M
	* 方法2 基于SVD
		* SVD求解，得到`A=U*S*V^T`
		* 选取S中比重高的k行作为降维矩阵Sk，将A进行降维结果为 `Uk*Sk`

### SVD 奇异值分解
---
* 参考文章
	* [SVD与PCA](http://blog.selfup.cn/1243.html)
* 简介
	* SVD全称singular value decomposition（奇异值分解），用于矩阵降维。
* 使用方法
	* 将高维数据进行SVD求解，得到`A=U*S*V^T`，其中U是左奇异向量，S是奇异值矩阵，V是右奇异向量，T表示矩阵转置。
* SVD在PCA中的应用
	* 降维：选取S中比重高的k行作为降维矩阵Sk，将A进行降维得到结果为 `Uk*Sk`，其含义就是去除那些不太重要的维度。
	* 按行压缩：舍去部分训练样本结果为 `Sk*Vk^T`，其含义就是去除那些十分相近的数据。



## LDA 线性判别分析
---
* 参考文章
	* [机器学习中的数学(4)-线性判别分析（LDA）, 主成分分析(PCA)](http://www.cnblogs.com/LeftNotEasy/archive/2011/01/08/lda-and-pca-machine-learning.html)

* 简介
	* LDA全称Linear Discriminant Analysis（线性判别分析），属于线性学习分类算法，属于supervised learning。
* 数学目标
	* 求解线性函数 `Y=AX+B` 的系数矩阵A，其中A是系数矩阵，X是样本数据各维度值构成的矩阵，B是常数，Y是X经过LDA投影后的数据结果。
* 求解目标
	* 对于样本分类数据经过线性函数投影后的结果，使得同一分类的结果尽可能集中&&不同类别之间结果距离尽可能大。
* 基本方法
	* 将数据打上分类标签，求解系数矩阵使得同类之间距离差小，类别之间距离差大。比如使用最小二乘法求解系数矩阵。

## kNN
---
* kNN 分类(k-Nearest Neighbor)
* 参考文章：
	* [kNN百度百科](http://baike.baidu.com/view/1485833.htm?fromtitle=Knn&fromid=3479559&type=syn)
	* [KNN算法](http://www.voidcn.com/blog/u010726042/article/p-2497507.html)
	* [用KNN算法分类CIFAR-10图片数据](http://www.mamicode.com/info-detail-1316003.html)
* 参考论文：
	* [Hastie, Trevor, and Robert Tibshirani. "Discriminant adaptive nearest neighbor classification." IEEE transactions on pattern analysis and machine intelligence 18.6 (1996): 607-616.]

### 核心思想
---
* 查找一个数据在特征空间中的k个最相似的样本，在这k个样本中，所属类别最多的一类作为该数据的分类。

### 算法
---
* 准备训练集和测试集，选定不同的k（一般k小于训练样本数的平方根，k太小会受噪声点影响，k太大会包含太多其他类的点）。
* 对每一个测试点，计算他和所有训练点的距离，保留最相近的k个训练点（可以存在优先级队列中），并设置k中所属类最多的为该测试点的类别
* 统计所有测试集的分类误差，选取误差最小的k作为kNN参数，以后就用这个k对新数据进行分类。

### 优点
---
* 适合对稀有事件进行分类：发生概率很小的分类问题（例如当流失率很低时，比如低于0.5%，构造流失预测模型）
* 简单方便：直接设置多次的k在样本集和测试集上进行分类，选取误差率较小的k即可。
* 适用于多分类或分类界限不明显的情况：由于数据分类时只和相近的样本有关，与类和类之间的分类界限无关。这方面比SVM可能更好。
* 不仅可以用于分类，还可以用于回归：通过找出一个样本的k个最近邻居，将这些邻居的属性的平均值赋给该样本，就可以得到该样本的属性。更或者，用权值与距离成反比等权值设置代替平均值。

### 缺点
---
* 分类局限：样本分类不均衡时，分类会偏移向该类，因为k中占得太多。
* 时空消耗大：因为对每一个待分类的文本都要计算它到全体已知样本的距离，才能求得它的K个最近邻点。
* 可理解性差，无法给出像决策树那样的规则。

###  改进
---
* 分类效率：处理掉影响较小的属性...
* 分类效果：合适的权值计算方式、内部可变的k、合适的距离衡量方法：高维度不宜使用欧式距离，值域越大的变量应先对变量进行标准化...

### kd-tree
---
* 参考文章
	* [KNN（三）--KD树详解及KD树最近邻算法](http://blog.csdn.net/app_12062011/article/details/51986805)
	* [KNN之KD树实现](http://m.blog.csdn.net/article/details?id=20542209)
* 基本概念
	* kd树中，kd代表k-dimension，每个节点即为一个k维的点。每个非叶节点可以想象为一个分割超平面，用垂直于坐标轴的超平面将空间分为两个部分，这样递归的从根节点不停的划分，直到没有实例为止。
* 为什么用kd-tree
	* 在knn计算中，传统线性计算方法是一个数据要和所有样本点计算距离，再进行比较，计算量和存储量都比较大
	* kd-tree的目标是将样本数据进行分层存储，所以在查找一个数据的knn时避免和不必要的样本点进行计算，降低计算量。

#### 构造kd-tree
* 经典的构造k-d tree的规则如下：随着树的深度增加，循环的选取坐标轴，作为分割超平面的法向量。对于3-d tree来说，根节点选取x轴，根节点的孩子选取y轴，根节点的孙子选取z轴，根节点的曾孙子选取x轴，这样循环下去。每次均为所有对应实例的中位数的实例作为切分点，切分点作为父节点，左右两侧为划分的作为左右两子树。建立kd-tree的时间复杂度为`O(k*n*logn)`。
* 根据方差选取split域的构造方法：
1.确定：split域=x。具体是：6个数据点在x，y维度上的数据方差分别为39，28.63，所以在x轴上方差更大，故split域值为x；
2.确定：Node-data=（7,2）。具体是：根据x维上的值将数据排序，6个数据的中值(所谓中值，即中间大小的值)为7，所以Node-data域位数据点（7,2）。这样，该节点的分割超平面就是通过（7,2）并垂直于split=x轴，即直线x=7；
3.确定：左子空间和右子空间。具体是：分割超平面x=7将整个空间分为两部分：x<=7的部分为左子空间，包含3个节点={(2,3),(5,4),(4,7)}；另一部分为右子空间，包含2个节点={(9,6)，(8,1)}；
4.递归：转1。对左子空间和右子空间内的数据重复根节点的过程就可以得到一级子节点（5,4）和（9,6），同时将空间和数据集进一步细分，如此往复直到空间中只包含一个数据点。

#### kd-tree的使用
kd-tree的搜索、插入、删除等

## k-Means
---
* 参考文章
	* [K Means Clustering Java Code](https://my.oschina.net/mazhiyuan/blog/141090)
	* [数据挖掘-聚类-K-means算法Java实现](http://www.dataonfocus.com/k-means-clustering-java-code/)

### 算法流程
---
* 初始化：选择K个点作为初始质心
	* 这一步首先要知道K的值，也就是说K是手动设置的，而不是像EM算法那样自动聚类成n个簇。
	* 其次，如何选择初始质心。最简单的方式无异于，随机选取质心了，然后多次运行，取效果最好的那个结果。这个方法，简单但不见得有效，有很大的可能是得到局部最优。另一种复杂的方式是，随机选取一个质心，然后计算离这个质心最远的样本点，对于每个后继质心都选取已经选取过的质心的最远点。使用这种方式，可以确保质心是随机的，并且是散开的。
* repeat
	* 将每个点指派到最近的质心，形成K个簇
	* 重新计算每个簇的质心
* until 质心不在变化 

### 优化
---
* 聚类使用的距离衡量
	* 聚类需要将最近的数据指派到一起，如何定义“最近”的概念，对于不同的数据，适用不同的邻近性度量。比如，对于欧式空间中的点可以使用欧式空间，对于文档可以用余弦相似性。
* 离群点的处理：
	* 离群点可能过度影响簇的发现，导致簇的最终发布会与我们的预想有较大出入，所以提前发现并剔除离群点是有必要的。比如利用方差来剔除离群点。
* 簇数优化
	* 使用较大的K，往往会使得聚类的结果看上去更加合理，但很多情况下，我们并不想增加簇的个数。这时可以交替采用簇分裂和簇合并。这种方式可以避开局部极小，并且能够得到具有期望个数簇的结果。

