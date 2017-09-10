---
title: Statistical Learning Method
comments: true
date: 2017-01-14 13:58:19
updated: 2017-01-14 13:58:19
categories: AI Machine Learning
tags:
- 统计学习方法
---

**说明：**统计学习方法，包括统计学习方法概论、感知机、kNN、SVM等等。
<!-- more -->

---
* 参考文章：
	* 《统计学习方法》--主讲分类

----
## 统计学习方法
* 周志华的《统计学习方法》主要从统计学、分类模型的角度介绍了一些机器学习算法。

---
### 概论

* 统计学习
	* 监督学习、非监督学习、半监督学习、强化学习
* 输入空间、特征空间、输出空间
* 主要的预测问题
	* 回归问题：输入与输出均为连续变量
	* 分类问题：输出为离散
	* 标注问题：输入与输出都是变量序列
* 统计学习三要素
	* 方法=模型+策略+算法
	* 模型：概率模型（条件概率）、非概率模型（如决策函数）
	* 策略：
		* 损失函数（代价函数），模型预测与真实值的不一致。常用的0-1损失函数、平方损失、绝对损失、对数损失（对数似然损失等）
		* 风险函数（期望损失），模型在联合分布下的期望损失
		* 经验风险最小化，模型在训练集上的平均损失最小化，数据量小时易出现过拟合
		* 结构风险最小化，在经验风险上添加一个正则化项（罚项）进行正则化，让结构风险最小。
	* 算法：如何求全局最优等算法
* 模型评估与选择
	* 训练误差与测试误差
	* 过拟合overfit与模型选择，过拟合是对已知数据很符合但对未知数据不适用，缺乏泛化能力
	* 一般来说，随着模型复杂度的提高，训练误差和测试误差都会先降低，但是再复杂会产生过拟合，导致训练误差小但是测试误差很大
* 模型选择的常用方法
	* 正则化regularization。在结构风险表达式上添加一个正则化项或罚项作为结构风险，使结构风险最小的模型
	* 交叉验证cross-validation。数据量大的时候可以直接分为训练集、验证集和测试集，用验证集选取最佳模型。数据量小的时候进行交叉验证：重复使用数据，分为训练集和测试集，用测试集选取误差最小的模型。
		* 简单交叉验证：随机分为训练集和测试集，选择测试误差小的模型
		* S折交叉验证：随机分为多分，选不同的几分为训练集和测试集，选取测试集平均测试误差小的模型。
		* 留一交叉验证：每次只有一个数据作为测试数据，其他都是训练数据。
* 泛化能力。泛化误差：模型对未知数据的预测误差。泛化误差上界。 
* 生成模型与判别模型
	* 生成方法->生成模型
		* 主要学习联合概率分布P(X,Y)，利用P(X,Y)/P（X）来生成P(Y|X)。主要关心各种输入对应的各种可能输出
		* 典型模型：朴素贝叶斯、隐马尔可夫
		* 特点：可以还原出P(X,Y)，学习收敛速度快，可以更加逼近真实模型，可以应用于存在隐变量的模型
	* 判别方法->判别模型
		* 主要学习决策函数f(X)或者条件概率分布P(Y|X)。主要关心给定一个输入对应的特定输出
		* 典型模型：kNN、SVM、感知机、决策树、boost等等
		* 特点：无法还原出P(X,Y)，由于直接面向输入的预测，所以准确率更高，不考虑内部因素因而可以简化学习问题
* 分类问题
	* 输出是离散的预测问题。需要进行模型学习和分类预测
	* 常用评价指标准确率precision和召回率recall，以及F1值，2/F1 = 1/pre + 1/recall。
	* 常用方法：kNN、感知机、朴素贝叶斯、决策树、SVM等等
* 标注问题
	* 输入输出都是变量序列的问题。输入是一个观测序列，输出是一个标记序列或状态序列。
	* 常用方法：隐马尔可夫模型、条件随机场
	* 常用领域：信息抽取、NLP、词性标注等。如一句话是一个输入观测序列，输出标记序列是标注的词性序列。
* 回归问题
	* 输入和输出都是连续的变量的预测问题。
	* 回归问题主要就是进行函数拟合，根据函数的特性分为一元回归和多元回归，线性回归和非线性回归等。
	* 回归问题常用损失函数是平方损失函数，常用最小二乘法求解。

---
###  Perceptron 感知机

* 感知机概念
	* 定义：将输入空间二分类到输出空间{+1，-1}的线性分类模型，函数表示为f(x)=sign(wx+b)，其中w是权值向量，b为偏置bias，wx是内积，sign是符号函数
	* 其模型对应分离超平面wx+b=0
* 感知机的学习策略
	* 损失函数：
		* 使用所有误分类点的数量不是连续可导不方便优化。
		* 使用所有误分类点到超平面的距离之和是连续可导的，感知机用损失函数为$L(w,b)=-sum_{x_i\in M}y_i(wx_i+b) $，其中M是误分类的点
	* 数据集的线性可分性。如果不是线性可分的，感知机训练会因为发散而漂浮不定。线性可分时，precetron被证明是一定会收敛。
* 感知机的学习算法
	* 原始形式求解
		* 思路：求解损失函数的极小值，可采用随机梯度下降stochastic gradient descent进行优化，然后随机选取误分类点进行w和b的更新
		* 算法步骤：选取初始w，b如0，遍历训练点，如果出现误分类（yi(w·xi+b)<0）则进行sgd调整w=w+lr·xi·yi，b=b+lr·yi，直至wb满足没有出现误分类点，其中a是学习率learning rate（0<lr<=1）
	* 对偶形式求解
		* 思路：w和b改写成为所有误分类点通过n次的lr 的表达式，训练数据只会以xi·xj的内积形式出现，可以进行预计算存储为Gram矩阵方便计算。
		* 算法步骤：初始化A、b为0，训练所有误分类点，出现误分类点(xi,yi)则进行sgd调整A=A+lr，b=b+yi，直至Ab满足没有误分类点。
* 特点
	* 适用于线性可分，二分类，属于判别模型，采用sgd求解，简单易实现，最终模型与初始参数、训练数据的迭代顺序有关。

---
### kNN k近邻法 k-Nearest Neighbor

#### 基础知识

* kNN概念
	* 在训练集中查找新数据的k个最近邻数据，根据决策规则给新数据做出决策。kNN常用于分类和回归。回归：如通过找出一个样本的k个最近邻居，将这些邻居的属性的平均值或加权值赋给该样本，就可以得到该样本的属性。
	* kNN实际上将特征空间划分为多个子空间cell单元，每一个cell对应一个决策结果。
* kNN模型
	* 三个基本要素：距离度量方法、k值选取、分类决策规则
	* 距离度量方法：Lp距离、Minkowski距离等。Lp距离是(sum|xi-yi|^p)^(1/p)，当p=1就是曼哈顿距离（坐标差的绝对值之和），当p=2就是欧氏距离，当p=无穷大就是各个坐标距离的最大值。
	* k值的选取：k较小时容易过拟合、受噪声影响，k过大时模型简单但会产生较高预测误差。k=1就是最近邻算法。k一般较小，一般低于训练样本数的平方根，并用交叉验证选取。
	* 分类决策规则：常用多数表决的方式。
* kd-tree
	* 参考文章
		* [KNN（三）--KD树详解及KD树最近邻算法](http://blog.csdn.net/app_12062011/article/details/51986805)
		* [KNN之KD树实现](http://m.blog.csdn.net/article/details?id=20542209)
	* 基本概念
		* kd树是一个二叉树，kd代表k-dimension，每个节点即为一个k维的点。每个非叶节点可以想象为一个分割超平面，用垂直于坐标轴的超平面将空间分为两个部分，这样递归的从根节点不停的划分，直到没有实例为止。
		* 如果数据是k维度的，就分别从第1,2,...k维度分，然后再从1,2...k维度分，直到所有点二叉树构造完成。
	* 为什么用kd-tree
		* 在knn计算中，传统线性计算方法是一个数据要和所有样本点计算距离，再进行比较，计算量和存储量都比较大
		* kd-tree的目标是将样本数据进行分层存储，所以在查找一个数据的knn时避免和不必要的样本点进行计算，降低计算量。
	* 构造kd-tree
		* 经典的构造k-d tree的规则如下：随着树的深度增加，循环的选取坐标轴，作为分割超平面的法向量。对于3-d tree来说，根节点选取x轴，根节点的孩子选取y轴，根节点的孙子选取z轴，根节点的曾孙子选取x轴，这样循环下去。每次均为所有对应实例的中位数的实例作为切分点，切分点作为父节点，左右两侧为划分的作为左右两子树。建立kd-tree的时间复杂度为`O(k*n*logn)`。
		* 根据方差选取split域的构造方法：
			1.确定：split域=x。具体是：6个数据点在x，y维度上的数据方差分别为39，28.63，所以在x轴上方差更大，故split域值为x；
			2.确定：Node-data=（7,2）。具体是：根据x维上的值将数据排序，6个数据的中值(所谓中值，即中间大小的值)为7，所以Node-data域位数据点（7,2）。这样，该节点的分割超平面就是通过（7,2）并垂直于split=x轴，即直线x=7；
			3.确定：左子空间和右子空间。具体是：分割超平面x=7将整个空间分为两部分：x<=7的部分为左子空间，包含3个节点={(2,3),(5,4),(4,7)}；另一部分为右子空间，包含2个节点={(9,6)，(8,1)}；
			4.递归：转1。对左子空间和右子空间内的数据重复根节点的过程就可以得到一级子节点（5,4）和（9,6），同时将空间和数据集进一步细分，如此往复直到空间中只包含一个数据点。
	* kd-tree的使用
		* kd-tree的搜索、插入、删除等。
		* kd-tree的搜索：
			* 在kd树种找出包含目标点x的叶结点：从根结点出发，递归地向下搜索kd树。若目标点x当前维的坐标小于切分点的坐标，则移动到左子结点，否则移动到右子结点，直到子结点为叶结点为止。以此叶结点为“当前最近点”。
			* 递归的向上回溯，在每个结点进行以下操作：
			（a）如果该结点保存的实例点比当前最近点距离目标点更近，则更新“当前最近点”，也就是说以该实例点为“当前最近点”。
			（b）当前最近点一定存在于该结点一个子结点对应的区域，检查子结点的父结点的另一子结点对应的区域是否有更近的点。具体做法是，检查另一子结点对应的区域是否以目标点位球心，以目标点与“当前最近点”间的距离为半径的圆或超球体相交：如果相交，可能在另一个子结点对应的区域内存在距目标点更近的点，移动到另一个子结点，接着，继续递归地进行最近邻搜索；如果不相交，向上回溯。
		* 当查询点的邻域与分割超平面两侧空间交割时，需要查找另一侧子空间，导致检索过程复杂，效率下降。一般来讲，最临近搜索只需要检测几个叶子结点即可。但是，如果当实例点的分布比较糟糕时，几乎要遍历所有的结点。但在实际的应用中，如SIFT特征矢量128维，SURF特征矢量64维，维度都比较大，直接利用k-d树快速检索（维数不超过20）的性能急剧下降，几乎接近贪婪线性扫描。所以这就引出了一系列对k-d树算法的改进：BBF算法，和一系列M树、VP树、MVP树等高维空间索引树。

#### kNN总结补充

* 算法
	* 准备训练集和测试集，选定不同的k（一般k小于训练样本数的平方根，k太小会受噪声点影响，k太大会包含太多其他类的点）。
	* 对每一个测试点，计算他和所有训练点的距离，保留最相近的k个训练点（可以存在优先级队列中），并设置k中所属类最多的为该测试点的类别
	* 统计所有测试集的分类误差，选取误差最小的k作为kNN参数，以后就用这个k对新数据进行分类。

* 优点
	* 适合对稀有事件进行分类：发生概率很小的分类问题（例如当流失率很低时，比如低于0.5%，构造流失预测模型）
	* 简单方便：直接设置多次的k在样本集和测试集上进行分类，选取误差率较小的k即可。
	* 适用于多分类或分类界限不明显的情况：由于数据分类时只和相近的样本有关，与类和类之间的分类界限无关。这方面比SVM可能更好。

* 缺点
	* 分类局限：样本分类不均衡时，分类会偏移向该类，因为k中占得太多。
	* 时空消耗大：因为对每一个待分类的文本都要计算它到全体已知样本的距离，才能求得它的K个最近邻点。
	* 可理解性差，无法给出像决策树那样的规则。

* 改进
	* 分类效率：处理掉影响较小的属性...
	* 分类效果：合适的权值计算方式、内部可变的k、合适的距离衡量方法：高维度不宜使用欧式距离，值域越大的变量应先对变量进行标准化...


---
### Naive Bayes 朴素贝叶斯法

* 朴素贝叶斯法的概念 Naive Bayes
	* 朴素贝叶斯法是基于“贝叶斯定理”和“特征条件独立假设”的分类方法。
	* 基本思路：首先基于特征条件独立假设，学习输入与输出之间的联合概论分布（典型的生成模型），根据该模型，对给定的输入x，利用贝叶斯定理求出各个输出的后验概率，选取概率最大的作为输出。
	* 特点：学习和预测的效率都比较高，是一种常用的方法。
* 相关概念
	* 先验与后验：先验表示的是已发生的、已确定的知识（没有加入要观测值的情况下的概率），后验是未发生、预测出来的知识（加入要观测值后的概率）。
	* 输出的先验概率分布：P(Y=Ck)，各个输出C的概率。条件概率分布：P(X=x|Y=Ck) = P(X1=x1,X2=x2,..Xn=xn | Y=Ck)，Y输出为Ck时，且输入为x的条件概率，X=x表示输入为x（x是n维的）。
	* 贝叶斯定理：P(Yi|X)= (P(Yi)P(X|Yi) ) / (sum(i): P(Yi)P(X|Yi))。根据条件概率公式：P(X|Y)=P(XY)/P(Y)很容易推导的。
	* 条件独立性假设：上面的P(X=x|Y=Yi) = P(X1=x1,X2=x2,..Xn=xn | Y=Yi) = (假设特征条件独立) P(X1=x1| Y=Yi)P(X2=x2| Y=Yi)...P(Xn=xn| Y=Yi)，这个假设使模型变简单，但是也比较naive，会降低准确率。
	* 将二者结合就可以得到P(Y=Yi|X=x)的表达式，选取最大输出的Yi作为分类进行后验概率最大化，决策函数就是：`f(x)=max(Ck): P(Y=Ck| X=x)` = (利用贝叶斯定理)max(Ck): (P(Ck)P(x|Ck)) / (sum(i): P(Ck)P(x|Ck)) 不同Ck的分母相同，只求分子最大即可 max(Ck): P(Ck)P(x|Ck) = (利用特征条件独立假设) max(Ck): P(Y=Ck) P(X1=x1| Y=Ck)P(X2=x2| Y=Ck)...P(Xn=xn| Y=Ck)
* 参数估计
	* 上述表明分类算法只要求先验概率 P(Y=Ck) 以及条件分布概率 P(Xi=xi| Y=Ck)。根据极大似然估计进行求解：用训练集对应的频率表示概率值。
	* 根据训练集进行极大似然估计，求出各个概率值，然后计算各个P(Y=Ck)的值，选取概率最大的一个作为输出结果。
* 贝叶斯估计 Bayes Estimation
	* 由于极大似然估计可能出现概率为0的情况，这样会影响后验计算，出现一些永远为0等情况，一种解决方法就是贝叶斯估计。
	* 贝叶斯估计：在随机变量的各个取值的频数上添加一个正数r，分母就是添加r*取值可能数。将贝叶斯估计分别用于条件概率和先验概率的计算，防止出现频数为0的情况。
	* 如果r取的是0，就是极大似然估计，如果取得是1，就是拉普拉斯平滑估计。

---
### Decision Tree 决策树

* 基本概念
	* 决策树Decision Tree，一种基本的分类和回归方法。
	* 主要优点模型是具有可读性，分类速度快。学习时通过损失函数最小化原则建立决策树模型，预测时，利用决策树进行分类。决策树学习通常包括3个步骤：特征选择、决策树的生成、决策树的修剪。
	 
* 决策树模型与学习
	* 决策树模型：决策树由结点和叶节点组成。根节点向下，分别向下根据特征决策直至叶子结点作为分类（节点分支可以超过2分支）。
	* 决策树与if-then规则：由根节点到叶节点对应的类的每一条路径对应一条规则。每一个规则集合需要具备性质：互斥和完备，互斥是只能对应一个（多选一），完备是必须选一个（覆盖所有可能）。
	* 决策树与条件概率分布：一条路径也可以视为一个条件概率分布：在当前特征取值x的情况下，分类到Y的概率最大的一类作为其分类。
	* 决策树学习：
		* 给定训练数据训练出分类规则，使得模型与训练数据矛盾较小，但是也有一定的泛化能力。
		* 最优决策树是个NP问题，长使用启发式方法近似求解。
		* 常用求解方法是递归选取特征、进行划分子集，直到各个子集的分类满足阈值。但是这样生成的决策树可能会过拟合，常用剪枝、选取部分特征的方式进行泛华。
		* 常用的方法有生成算法：ID3、C4.5，生成并剪枝算法：CART。
* 特征选择
	* 特征选择要选取具有有效分类作用的特征。常用的特征选择准则是信息增益、信息增益比。
	* 信息增益
		* 信息的熵Entropy：用于表示随机变量X不确定性的度量，$H(p)=-\sum(p_i log P_i)$，熵和X的取值大小无关，只和其概率分布有关。如果H(p)中对数以2为底，则熵的单位是bit比特，以e为底则是纳特nat。一个变量越不确定，其熵越高，当p=0或p=1，则是完全确定，定义其熵为0log0=0。如果是通过数据估计（尤其是极大似然估计）获得的熵，叫做经验熵。
		* 条件熵：H(Y|X)表示给定X的情况下，Y的在条件概率分布下的熵的数学期望，$H(Y|X)=\sum P_iH(Y|X=x_i)$。如果是通过数据统计获得的，叫做经验条件熵。
		* 信息增益Information Gain
			* 表示得知特征X的信息而使得类Y的信息不确定性减少的程度，也就是确定X而使Y确定性提高。
			* 信息增益：g(D,A)=H(D)-H(D|A)，集合D的经验熵H(D)（关于分类结果的经验熵） 减去 D在特征A条件下的经验条件熵H(D|A)。该差又叫互信息，在决策树中信息增益等价于训练数据中的类与特征的互信息。
		* 根据信息进行特征选取的方法：对训练数据集D，计算每个特征的信息增益，选取信息增益最大的作为特征。（计算各个特征的经验熵、D的经验条件熵、信息增益）。
	* 信息增益比/信息增益率 Information Gain Ratio
		* 信息增益存在的问题是：如果一个特征的取值较多，容易偏向选取该特征。
		* 信息增益比：$g_R(D,A) = g(D,A)/H_A(D)$，用信息增益与训练数据集D关于特征A的值的熵之比，Ha(D)：条件A各个取值将D划分为多个子集Di，|Di|表示集合容量，$Ha(D)=-\sum (|Di|/|D|) log(|Di|/|D|)$。
* ID3、C4.5 决策树生成算法
	* 二者生成过程相似，只不过ID3用信息增益，C4.5用信息增益率，信息增益比理论上会比信息增益要好一些，但实际也可能差不多。
	* 输入：训练数据集D，特征集A，阈值e。
	* 输出：决策树T。
	* 生成过程：
		* 如果D全是同一个类的，就是个单结点树。
		* 如果A是空，没有特征集，还是个单结点树，选取分类最多的类为树的分类。
		* 否则递归计算信息增益（C4.5中下面对应都是信息增益率），选取最大的特征。
		* 如果一个特征的信息增益小于阈值e，将分类最多的类作为该结点的类，设置为单结点树。
		* 如果一个特征的信息增益比大于阈值e，就根据该特征的取值进行划分为子集Di，进行递归构建。
* 决策树的剪枝pruning
	* 生成树不进行剪枝的话很容易过拟合。剪枝通常通过极小化决策树整体的损失函数实现。
	* 简单的Loss Function可以定义为：$C(T)= \sum_{t=1}^{|T|} N_t H_t(T) + \alpha |T|$，其中|T|是树的叶节点个数，t是树T的叶节点，该叶节点有Nt个样本点，Ht(T)是叶节点t上的经验熵，\alpha |T|是正则化项，前面一项记为C()T是经验熵之和，所以极小化该函数就是使信息熵最小（确定性最高）、正则化项尽量小。极小化工作对应到模型就是：前面一项是最佳拟合训练数据，后面一项考虑模型复杂度控制进行正则化处理。
	* 剪枝算法：
		* 输入：生成的决策树T，正则化系数a；输出剪枝后的决策树。
		* 算法：
			* 计算每个节点的经验熵。
			* 递归地从叶节点向上回缩（叶节点全部合并成上一个叶节点），回缩前后对应两个子树分别为Ta，Tb，分别计算其损失函数，如果合并后的损失函数小，就用合并后的树（即剪枝）。
			* 如此递归，直到不能继续。
		* 剪枝只涉及到两个数的损失函数之差，可以局部进行，可以使用动态规划。
* CART算法
	* CART总览
		* CART算法，Classification And Regression Tree，分类与回归树。
		* CART算法假定决策树是二叉树，每个分支的左边是“是”，右边分支是“否”。
		* CART算法组成：生成决策树（用训练数据集生成树，树要尽量大）、决策树剪枝（用验证数据集和损失函数进行剪枝并选择最优子树）。
	* CART生成
		* CART生成回归树
			* 使用最小化平方误差的方法生成回归树。
			* 回归的时候，输出Y是连续的。不同的输入对应着输出，将输出空间划分为m个，每个输出空间对应一个输出值c。回归树的输出模型就是f(x)=各个c乘以是否在对应的输入空间（0,1）。
			* 最小化准则就是训练平方误差各个(yi-f(xi))^2的和最小。
			* 如何确定划分空间？采用启发式方法，遍历所有输入变量j和它的取值作为切分变量和切分点，每个j和s确定了两个区域，通过平方差之和最小确定**最优切分变量与最优切分点**。不断对子区域进行递归划分，构造出决策树。
		* CART生成分类树
			* 使用最小化基尼指数Gini index的方法生成分类树。
			* 对于分类问题，一共K个类，样本点属于第k类的概率为pk，则概率分布的基尼指数为：$Gini(p)=\sum_{k=1}^{K}pk(1-pk) = 1-\sum_{k=1}^{K}{pk}^2$。如果是二分类，属于第一个类概率为p，那就是2p(1-p)。如果是堆样本集和D，那么$Gini(D)=1- \sum_{k=1}^K{|Ck|/|D|}^2$，其中|Ck|是D中属于第k类的样本子集的样本数。如果样本集合D根据特征A=a分为2个子集D1和D2，则D1={(x,y)属于D|A(x)=a}，集合D在特征A的基尼指数定义为：$Gini(D,A)= (|D1|/|D|)Gini(D1) + (|D2|/|D|)Gini(D2)$
			* 基尼指数表示集合D的不确定性，数值越大说明集合的不确定性也越大，这与熵类似。
			* 生成算法：
				* 输入：训练数据集D，确定停止计算的条件（样本个数小于阈值、基尼指数小于阈值(分类基本正确)、没有更多特征等）
				* 输出：CART分类决策树
				* 训练：计算所有特征的不同取值将D划分为D1和D2后，计算Gini(D,A)指数；选取基尼指数最小的特征和值作为**最优特征和最优切分点**，分成了2个子集；递归进行上面的特征选取和切分，得到CART决策树。
	* CART剪枝
		* **剪枝生成子树序列** 子树的损失函数使用上面剪枝中的函数Ca(T)=C(T)+a|T|，学者证明：当a从0增加到无穷大，产生一系列a的区间，对应着剪枝得到的子树序列T0，T1，T2...且这些子树是嵌套的。对树的每个结点t，a很小的时候，单结点树的损失函数为C(t)+a，拟合为主，所以必然大于以t为父节点的子树Tt的损失函数C(Tt)+a|Tt|，随着a增大，二者会相等甚至反超，也就是说以泛华为主的时候倾向于单节点树。当二者相等就是表明该a能够使得剪枝前后的损失函数相等，但是可以进行剪枝，此时a=(C(t)-C(Tt)) / (|Tt|-1)。为此，自下而上对T0每个内部结点计算a的值g(t)=a，将T0减去g(t)最小的子树Tt得到T1，同时将g(t)最小的值设为a1，则T0是a在[0,a1)内的最优子树，对T1进行计算g(t)，重复剪枝得到T2，T1就是[a1,a2)之间的最优子树。递归得到a的区间和对应剪枝后的子树序列。
		* **采用交叉验证选取最优子树** 采用独立的验证集，测试子树序列各个树的平方误差或基尼指数，对应值最小的就是最优的决策树，也就对应了一个a的取值区间。
		* CART剪枝算法步骤：从a为无穷大向0进行剪枝生成子树序列，交叉验证选取最优子树。
* ID3用信息增益生成决策树，C4.5用信息增益率生成决策树，CART用Gini进行特征选取，并生成剪枝子树序列、交叉验证进行选取。剪枝操作使用信息增益、信息增益比、基尼指数加上正则化项作为损失函数，越小越好。

---
###  逻辑斯蒂回归Logistic Regression与最大熵模型Maximum Entropy Model/MEM

* Logistic Regression 逻辑斯谛回归是经典的分类方法，基于Logistic Distribution逻辑斯谛分布。最大熵是概率模型学习的一个准则，推广到分类问题得到最大熵模型Maximum Entropy Model。二者都是对数线性模型。
* 二项逻辑斯谛回归模型：二分类到0和1，概率分别是1/(1+exp(wx))和exp(wx)/(1+exp(wx))。取对数几率logit(y=1)=log(分为1的概率/不分为1的概率)=化简得到wx，表示逻辑回归分类到1的对数几率是关于特征x线性的。

* 个人理解：逻辑回归用于二分类问题，softmax用于多分类问题，都是求解对应到不同分类的概率值，选取最大概率作为分类。比如逻辑回归是0和1，概率分别是1/(1+exp(wx))和exp(wx)/(1+exp(wx))，其中的1就是exp(0)。此外，逻辑回归使用的函数就是sigmod函数g(z)=1/(1+exp(-z))


---
### VSM 支持向量机

* Vector Space Machine 支持向量机。



---
### EM算法及其推广



---
### HMM 隐马尔可夫模型




---
### CRF条件随机场Conditional Random Field





---
## 其他-《深度学习与神经网络》

---
### 概率图模型PGM Probabilistic Graphical Model

* PGM相关概念
	* 概率图模型 Probabilistic Graphical Model，PGM，将概率论和图论进行结合，用图的方法表示模型的概率分布（能量函数、密度函数）。用图的结点表示随机变量，用边表示变量之间的依赖关系，缺少边就是相互独立的。
	* 概率图模型分类：概率无向图模型、概率有向图模型、概率混合图模型
* 概率有向图模型 Probabilistic directed Graphical Model
	* 有向图使用有向边表示结点间存在因果关系，典型代表是隐马尔可夫模型（HMM，Hidden Markov Model）、贝叶斯网络、动态贝叶斯网络等。
	* 贝叶斯网络（Bayes Network，BN）是最基本的有向图模型，属于一种有向无环图。
		* 常用的贝叶斯分类器包括
			* 朴素贝叶斯网络 Naive Bayes Network，NBN
			* 通用型贝叶斯网络 General Bayes Network，GBN
			* 增强型贝叶斯网络 Tre-Argumented Bayes Network，TAN
			* 马尔可夫毯贝叶斯网络 Markov Blanket Bayes Network，MBBN
			* 动态贝叶斯网络 Dynamic Bayes Network，DBN
	* 隐马尔可夫模型（HMM，Hidden Markov Model）是马尔可夫模型的扩展，建立在马尔可夫链基础上。
		* 马尔可夫链是马尔可夫随机过程的特殊情况，即马尔可夫链的状态和时间都是离散的马尔可夫过程。马尔可夫链的m+1时刻的状态之和m时刻的状态有关，而与之前的状态无关。实际中，马尔可夫链的每个状态可以对应于一个可观测的物理事件，如天气的雨、雪、晴等，根据这个模型可以计算不同时刻的天气概率。
		* 但是实际问题比马尔可夫链描述的更为复杂，观测到的事件不不是与状态一一对应的，而是通过一组概率分布相关联，这就是隐马尔可夫HMM。MM是一个双重的随机过程，第一是马尔可夫链用于描述状态转移，第二是随机过程描述状态和观察值。第二个随机过程不是与状态一一对应的，需要通过一个随机过程感知状态的存在特性，这就是Hidden的由来。
* 概率无向图模型 Probabilistic Undirected Graphical Model
	* PUGM用于建立随机变量之间的空间相互关系，通常表示已一对结点间的相互依赖关系。
	* 典型代表是马尔可夫随机场（Markov Random Filed，MRF）和条件随机场（CRF，Conditional Random Field）
	* 马尔可夫随机场（Markov Random Filed，MRF）
		* 马尔可夫随机场（Markov Random Filed，MRF）又叫马尔可夫网络（Markov Network，MN），是一组具有马尔可夫性质的随机变量X的全联合概率分布模型。MRF可以表示贝叶斯网络BN无法表示的依赖关系，如循环关系，但是MRF也不能表示BN所表示的推导关系。
		* 当概率分布为正时，被称为Gibbs随机场，因为根据（MN与Gibbs分布的等价性）和局部马尔可夫性质，无向图的概率分布可以被定义为Gibbs公式。
* PGM的使用
	* PGM适用于以下情况：
		* 拥有噪音数据或者不确定性比较大的时候
		* 拥有很多先验知识的时候会比普通的ML有效
		* 多个变量的推理尤其是存在内部关系的变量推理
		* 从较小的模块化的模型中建立复杂的模型，如疾病诊断、故障诊断、NLP、语音识别、CV等。
* 有向图和无向图模型的对比
	* 共同点：将复杂的联合分布分解为多个因子的乘积
	* 不同点：无向图模型因子是势函数，需要全局归一。有向图模型因子是概率分布，无需全局归一。
	* 优缺点：无向图模型中势函数设计不收概率分布约束，设计灵活，但是全局归一代价高。有向图模型无需全局归一，训练相对高效。  