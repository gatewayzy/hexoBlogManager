## MV-LSTM多位置的句子语义表示的句子匹配 
2016 A Deep Architecture for Semantic Matching with Multiple Positional Sentence Representations  中科院 2015 AAAI

### 模型
 
![image](http://zcy.ckcest.cn/cdn/zy/20171012-Image-5.jpg)

一种多位置特征的句子匹配结构MV-LSTM，可用于QA匹配、句子填充等任务，图中Sx和Sy表示两个句子，使用BiLSTM构造位置特征，使用tensor layer构造交互张量，对交互张量使用k-Max Pooling采样，最后使用一个MLP进行抽象输出：用FC全连接层进行高层抽象降维，用线性变换输出最终匹配得分s。


1.  使用BiLSTM构造双向特征，每个ht←和ht→都包含了整个句子的信息，一个时刻的向量就是二者的拼接p。
2.  3种向量相似度衡量方法：
	1. Cosine余弦值、Bilinear双线性函数、Tensor Layer 张量函数
	1. Bilinear： s(u，v) = u(转置)Mv + b;  M是权重矩阵，不同的u和v可以具有不同的权值。
	1. Tensor Layer： s(u, v) = f(u^T M ^([1:c])v + W_(uv)(uv堆叠) + b)； f可以用ReLU表示相似性。
3.  关系聚合
k-Max Pooling 将互动张量采样，k取1则是两句子之间最佳互动，k取多个则利用多个位置匹配信息。输出为q。
MLP：MultiLayer Perception多层感知机，也叫Full-connection Neural Network ，这里用一个FC，然后线性输出得分s。r = f(Wq + b); s = Wr + b。

### 训练
使用(Sx,Sy+,Sy-)，loss= max(0，1 − s(Sx; Sy+) + s(Sx; Sy−))，用adagrad进行bp和sgd。

### 测试
QA任务：比较CNTN系列、LSTM-RNN、MultiGranCNN等，也说明MV-LSTM涵盖了LSTM-RNN。QA准确度用P@1以及MRR平均倒数排名。
Sentence Completion：
