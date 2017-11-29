## 2016 Community-Based Question Answering via Heterogeneous Social Network Learning
---


## 摘要
---
* 社交qa（cQA）主要是通过**问题与回答的内容相似度**进行推荐，问题在于无法解决**数据稀疏性**的问题（[sparsity] 2010 Routing questions to appropriate answerers in community question answering services)
* 采用方法：融入用户的社交关系信息


## 介绍
---
* 大多实现qa的方法是短文本匹配，如查找最相似的问题、查找最优的回答，一个缺点在于文本内容的特征不够有效。
* 传统的bow方法在文本建模有效，但是不能捕捉文本时序信息。（bow的提出，使用，改进）
* 近年来，多种方法学习相似词语的语义并进行词序信息的编码，用来将文本映射到低维连续的embedding space。（word2vec的理论、使用、改进）
* 本文采用random walk来利用上社交关系信息以解决数据稀疏性，用RNN（循环网络）进行文本建模。模型称为HSNL。
	* 作用：针对一个问题，查询最佳答案、从相似问题推荐合适的答案；模型学习的结果可以用于专家发现
	* 主要特点：使用了社交信息、random walk方法、深度学习 （2014 Deepwalk Online learning of social representations）

## 本文工作
---
### 相关工作
---
* 近年来，深度学习在cQA中应用广泛
	* 用经典CNN计算各个问答对的相似矩阵，含有词汇和顺序信息（2015 Question answer matching for CQA system via combining）
	* 用动态CNN对不同长度的问答进行语义编码，并用一个张量层进行关系的建模（2014 A convolutional neural network for modelling sentences）
	* 用RNN（循环recurrent）将文本语句表示为密集向量，并用来预测文本词语（2014 Distributed representations of sentences and documents）
	* 用多层RNN将输入文本映射到固定维度的向量上（2014 Sequence to sequence learning with neural networks）
* 和本文思想一致的方法是利用引入侧面信息以提高qa质量
	* 用RNN（递归Recursive）和sentence dependency-free tree构建仿真问答（2014 A neural network for factoid question answering over paragraphs）
	* 用Wikipedia构建语义词典，利用语义关系增强问答检索（2013 Improving question retrieval in community question answering using world knowledge）
	* 用图正则矩阵完成算法推断用户模型，并提升专家发现的效果（2015 Zhao）
	* 用跨域的社交信息，将社交网表示成以一个社交领域为中心的混合图模型，用random walk方法混合跨域社交信息来预测指定领域内的用户实体链接。

### 用循环RNN构建qa模型
---
* 循环RNN对变长、时序的学习内容很有帮助，能够将问答文本编码为固定长度的特征向量。一个序列的词语用循环RNN构建词向量。（2013）
* 朴素的循环RNN难以训练，因为存在long term dependencies。（2014 Sequence to sequence learning with neural networks）
* 所以使用LSTM进行问答文本的embedding。

### 加入社交信息
---
* 在问答和社交信息的网络中，进行deep random walk利用社交信息，构建路径。
* 对每个节点，用文本窗口对路径进行采样，分别训练问题、回答的LSTM和用户的embedding。
* 计算问题与回答之间matching score
* 设计三个不同的loss函数分别对应当前节点是问题、回答和用户的情况，进行bp训练。

### 训练过程
---
* 用DeepWalk在问题、回答、用户三层中不断构建关系队列。用walker生成path。其中walker只允许沿着具有follow关系的用户的问答数据采样。
* 对不同的path，将问题、回答与user分别进行模型计算，得到固定长度的q、a、u的特征向量。
	* question使用lstm、maxpooling
	* answer使用lstm、maxpooling
	* user使用构建的embedding矩阵
* 对每一个结点，计算loss值
	* 如果是question，loss就+(问题与不匹配回答的匹配得分)-(问题与更匹配回答的匹配得分)
	* 如果是answer，loss就+(回答与不匹配问题的匹配得分)-(问回答与更匹配问题的匹配得分)
	* 如果是user，loss就+当前用户与user矩阵的欧氏距离。
* 也就是说，loss对应path中问题与回答的匹配度、回答与问题的匹配度、用户所有用户矩阵的距离。通过对loss进行bp和sgd就能够优化q、a、u之间的参数系数，如qa之间的最佳匹配。

* 使用SGD（随机梯度下降）和对角线型AdaGrad进行目标函数最小化（2015 Convolutional neural tensor network architecture for community-based question answering）

* 算法summary
	* **input**：社交网G(V,E)，V是节点集合，E是边的集合。walk目标的n个节点，最长walk长度t，进行m轮迭代
	* 用DeepWalk训练user embedding matrix
	* 1.开始m轮迭代
	* 2.开始n个节点的walk
	* 打乱所有节点（shuffle）
	* 3.对每个节点
	* 生成路径p=RandomWalk（G,v,t），计算该路径中每个节点的loss
	* end 3
	* 计算所有结点的loss之和+。。
	* 使用SGD进行bp，将目标函数最小化
	* end 2
	* end 1

### 实验
---
* 数据集
	* 使用Quora的问答数据，并获取Quora用户对应的Twitter follow关系。
	* 数据爬取清洗，使用支持数/反对数进行答案的评分（Zhao 2015）
	* 无重叠分为训练、验证和测试集
* 排名评价指标
	* NDCG 
	* Precision@1 
	* Accuracy 
* baseline和实验设置
	* BOW
	* LDA
	* Doc2Vec
	* DeepWalk
	* CNTN
	* S-matrix
	* 前四个无监督，常用于构建语义特征空间，后两种是监督式，常用于计算问答之间的匹配得分。
* 结论
	* cQA中，监督式比非监督要好，监督信息很有用，如问答匹配得分。
	* 非监督式方法鲁棒性更好，数据不过时监督式更容易过拟合。
	* deepwalk和nn网络有助于cQA任务。
