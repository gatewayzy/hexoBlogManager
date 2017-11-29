---
title: QA
comments: true
date: 2017-10-14 13:58:15
updated: 2017-10-14 13:58:56
categories: QA
tags:
- QA
---

**说明：** QA，比如qa pair问题，chatbot智能聊天，dialog对话系统。
<!-- more -->
---
# Tutorial

## QA的常见思路

解决QA，一般是先确定用户的意图（可以看成是提取出文本的特征），然后根据特征返回最佳的答案。

* 提取文本的特征：一般有传统机器学习的方法、深度学习的方法。
  * Traditional传统方法，比如进行语法分析、实体识别、实体链接、限制关系确定等步骤，根据语法特征、句子特征等确定实体和关系等。
  * DeepLearning深度学习的方法，深度学习往往需要大规模语料，训练一个模型，模型中也加入语法特征、句子特征等，得到用户意图的向量空间（高层语义特征）。
* 确定意图之后，返回答案的策略包括：模板匹配、检索式和生成式
  * 模板匹配 pattern match，模板制作麻烦且数量有限，但是一旦匹配上则准确率比较高。
  * 检索式 retrieval based，先搜索相似的问题或答案，然后计算问题和候选问题之间的特征相似度，或者计算问题与候选答案之间的匹配得分。
  * 生成式 generative model，比如训练GAN，VAE等模型，根据问题的特征直接生成候选问题或候选答案。

## QA的分类

QA处理的数据不同，可以进行不同角度的分类。

* 根据数据的组织结构，分为：
  * 知识库KBQA，Knowledge Base QA。KBQA特点是数据权威，可进行推理拓展，答案质量高，但是目前实现复杂推理比较难，仍以单一三元组为主。
  * 问答对CQA，Community QA。（由于以研究cqa为主，后文的cqa有时会被简称为qa）。CQA特点是数据噪声稍多，推理难。
* 根据数据的领域性，分为：专业领域 和 开放域open-domain。

---
# CQA综述

![CQA 百度脑图](http://zcy.ckcest.cn/cdn/zy/20171011-Image-1.jpg)

## CQA的分类

CQA处理的应用场景不同划分为：简单的QA Pair问题和复杂的chatbot问题。

* QA Pair问题就是最简单的问答匹配问题，不考虑历史对话内容，完全根据用户的一个问题生成答案。这种情况下，用户往往会提供尽可能完整的信息以获取准确的答案。
* Chatbot或者Dialog System，在对话系统中，往往需要加入全局信息、用户意图分析，比如加入所有对话内容特征、用户主题特征、用户信息特征等。这是因为，在多轮对话时，虽然上一句的信息关联性最大，但是可能缺乏全局信息，比如用户说“那怎么办”就没有主体。

## 语料集与任务 Corpus and Task

* 语料集的survey
  * [对话语料集调研1512.05742](1512.05742 A Survey of Available Corpora for Building Data-Driven Dialogue Systems.pdf)
* 语料集按照内容分类及举例：
  * 经典的语言建模：[Penn TreeBank]，[Text8]
  * 故事理解：儿童书、新闻等 [Children’s Book Test]，[News articles]
  * 开放域问答：wiki问答等 [Web Questions]，[WikiQA]
  * 以目标为导向的对话聊天：电影字幕、专业社区等 [Movie Dialog]，[Ubuntu Corpus]
* 常见的CQA语料集：
  1. [Twitter语料 Sordoni1506](1506.06714 A neural network approach to context-sensitive generation of conversational responses.pdf)
  1. [Ubuntu社区语料 Lowe1506](1506.08909 The Ubuntu Dialogue Corpus A Large Dataset for Research in Unstructured Multi-Turn Dialogue Systems.pdf)
  1. [SQuAD Stanford QA 1606](1606.05250 SQuAD 100,000+ Questions for Machine Comprehension of Text.pdf)
    * 数据示例：提问Question：where is PK？ 根据文本Passage：`PK is in **China**. You can...`，回答是这一段的位置
    * 对于一个问题，目标是给出答案所在的段落，并将答案标出。用来做QA，机器理解MC
    * 该数据集提供[实时的算法排名](https://rajpurkar.github.io/SQuAD-explorer/)
  1. [微软MS MARCO 1611](1611.09268 MS MARCO A Human Generated MAchine Reading COmprehension Dataset.pdf)
  1. [SemEval task 2017](2017 SemEval-2017 Task3 Community Question Answering.pdf) 
	  * CQA任务中包含问答对，答案质量标注为`很好/一般/不好`。
  1. [百度中文CQA数据集](http://idl.baidu.com/WebQA.html)

## CQA模型的评价标准 Evaluation Metric
* 针对模型的评价，可以从不同方面考虑：rank算法的排序效果、generative算法与正确答案的重叠率/人工评判等。
* 常见的模型评价标准，在task和论文中都会进行说明，比如 [SemEval task 2017]、[HSNL 2016]：
	* MAP，Mean Averaged Precision：比如用top-10中命中的命中率的均值
	* BLEU [Papineni 2002](2002 BLEU a method for automatic evaluation of machine translation.pdf)。
	* Meteor [Lavie 2007](2007 METEOR An automatic metric for MT evaluation with high levels of correlation with human judgments.pdf)
	* P@1，precision@1，排名第一的结果是正确结果的比例，越高越好。
	* P@5, precision@5
	* Hit@3，正确答案的排名在前3名中的比例，越高越好。
	* MRR，Mean Reciprocal Rank，比如top-10中正确答案的排名的倒数的平均值，越高越好。


* [Liu 2016](1603.08023How NOT To Evaluate Your Dialogue System An Empirical Study of Unsupervised Evaluation Metrics for Dialogue Response Generation.pdf)指出使用BLEU等popular方法进行模型评估的结果与人类的判断是不一致的。


---
## CQA-seq2seq

* seq2seq解决CQA时面临的问题：
  * 过长文本的时候产生长期依赖。使用attention机制[Attention Mechanisms Bahdanau1409](1409.0473 Neural Machine Translation by Jointly Learning to Align and Translate.pdf)
  * 需要大量数据问题。使用公开数据、自己获取数据。
  * 容易产生通用回复，如“不知道”。
    * 多种目标提升[Diversity-Promoting Objective Bahdanau1510](1510.03055 A Diversity-Promoting Objective Function for Neural Conversation Models.pdf)
    * 对问答中的通用问答进行惩罚
