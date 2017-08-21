---
title: nlp与神经网络
comments: true
date: 2017-01-18 13:58:23
updated: 2017-01-18 13:58:56
categories: AI Deep Learning
tags:
- Word2vec
- Deep Learning
- NLP
---

**说明：**word2vec介绍。
<!-- more -->


参考文章：


## NLP相关
---
* 会议：AAAI、ACL、NAACL、IJCAI、EMNLP
* 比赛：Semantic Evaluation、NLPCC


## 自动问答

### 问答方式
---
* 基于模板匹配
	* 问题：
		* 新词发现
		* 实体识别：用户表述不规范、别名问题
		* 语意消歧
	* 流程：新词发现、分词、词向量训练
	* 工具选取：
		* 新词发现：Stanford Tokenizer、ansj、HanLP、庖丁分词、结巴分词等等
		* 词向量训练：Word2Vec、LSI、GloVe
	* 检索匹配：
		* 关键词扩充：
			* 字符串相似度匹配：编辑距离、改进版本
			* 单词语义相似度匹配：LSI、Word2Vec
* 基于检索查询
	* 问题->问题->答案
	* 问题->答案：适合cQA，QA-LSTM（Bi-LSTM）
* 基于产生式


