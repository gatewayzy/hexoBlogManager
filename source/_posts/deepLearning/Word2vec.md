---
title: word2vec
comments: true
date: 2017-01-18 13:58:23
updated: 2017-01-18 13:58:56
categories: Deep Learning
tags:
- word2vec
- Deep Learning
- NLP
---

**说明：**word2vec介绍。
<!-- more -->


参考文章：

## 词向量
---
* 参考文章： [用 Word2vec 轻松处理新金融风控场景中的文本类数据](http://www.toutiao.com/i6358244582278300162/)
* one-hot vector 与 distributed representation
	* one-hot vector：将所有词放在一维空间中，根据是否存在标为0/1或者使用出现的次数构成向量，如[5,0,0]，由于要表示一个词语就会增加一个维度，因此会很长，空间极大浪费。
	* distributed representation：使用多个维度表示词向量，如[1,0,1]，缩短了很多。他可以简称词向量，但其实one-hot本质也是一种词向量。
* distributed representation 分布式表征词向量
	* 构造思想：由于相似的词语拥有相似的上下文，所以可以利用一个词语的上下文，如一个词语与其它词语共同出现的次数，这样一个次数组成的向量，来表示这个词语。当然，如果句子特别长，我们可以限定窗口，只取该单词前后 n 个单词的词共现次数来表示这个单词。
	* 举例：
		* 仅有以下三个句子的一个语料库：I like deep learning. I like NLP. I enjoy modeling.
		* 取窗口长度 n=1，则下图每一列都是该列单词的词向量

|counts	|I	|like	|enjoy	|deep	|learning	|NLP	|modeling	|.	|
|:---	|:---	|:---	|:---	|:---	|:---	|:---	|:---	|:---	|
|I	|0	|2	|1	|0	|0	|0	|0	|0	|
|like	|2	|0	|0	|1	|0	|1	|0	|0	|
|enjoy	|1	|0	|0	|0	|0	|0	|1	|0	|
|deep	|0	|1	|0	|0	|1	|0	|0	|0	|
|learning	|0	|0	|0	|1	|0	|0	|0	|1	|
|NLP	|0	|1	|0	|0	|0	|0	|0	|1	|
|modeling	|0	|0	|1	|0	|0	|0	|0	|1	|
|.	|0	|0	|0	|0	|1	|1	|1	|0	|

## word2vec
---
* 构建思想：与之前一般的共现计数不同，word2vec 作为现在主流的词嵌入（word embeddings）算法，主要是通过预测一个窗口长度为 c 的窗口内每个单词的周边单词概率，来作为这个单词的词向量。
* 阅读文章：[词嵌入系列博客Part1：基于语言建模的词嵌入模型](http://www.jiqizhixin.com/article/1702)


## 词向量到文本向量
---
通过word2vec、LDA等方法构造好词向量之后，表示一个文本向量的方法通常有：

* 使用doc2vec：结果并不理想。
* 词向量求和：将文本中各个词的向量直接进行向量相加，或者相加之后再求均值。
* 词向量加权求和：如对文本中各个词计算TF*IDF作为权值，进行加权求和。




