---
title: Lucene
comments: true
date: 2017-01-12 13:58:12
updated: 2017-01-12 13:58:56
categories: Softwares
tags:
- Lucene
---

**说明：**Lucene配置使用。
<!-- more -->


---
参考文章：

## 简介
---
* 查看索引文件的内容-Luke
	* Luke说明：使用[Luke](https://www.oschina.net/news/86840/luke-6-6-0)查看存储后的lucene文件。
	* 下载Luke：直接下载release版本如果太慢，可以下载源码，使用mvn install自己编译（maven会下载很多jar然后编译）。
	* 运行Luke：运行bat，进入交互界面，选取index路径即可。

## Lucene Demo
---

### Lucene建立索引

建立索引包括文件索引和内存索引。内存索引速度上快一些，但是建好之后要写到文件中才能保存下来。

```
	@Override
	public void fileIndexing() throws Exception {
		// 文件索引更新索引
		Directory directory = FSDirectory.open(new File("D:/tcm/usercenter/luceneFeedback"));
		Analyzer analyzer = new IKAnalyzer();
		//IndexWriterConfig config = new IndexWriterConfig(Version.LUCENE_36, analyzer);
		//IndexWriter iwriter = new IndexWriter(directory, config);
		IndexWriter iwriter = new IndexWriter(directory, analyzer, true, MaxFieldLength.LIMITED);
		iwriter.deleteAll();

		List<FeedbackPo> feedbackPos = feedbackDaoImpl.selectAllPo();
		for (FeedbackPo feedbackPo : feedbackPos) {
			Document doc = new Document();
			doc.add(new Field("title", feedbackPo.getTitle(), Store.YES, Index.ANALYZED));
			doc.add(new Field("content", feedbackPo.getContent(), Store.YES, Index.ANALYZED));
			doc.add(new Field("subsysName", feedbackPo.getSubsysName(), Store.YES, Index.ANALYZED));
			doc.add(new Field("category", feedbackPo.getSubsysName(), Store.YES, Index.ANALYZED));
			doc.add(new Field("reply", feedbackPo.getReply(), Store.YES, Index.ANALYZED));
			iwriter.addDocument(doc);
			iwriter.commit();
		}
		iwriter.close();

	}

	@SuppressWarnings("deprecation")
	@Override
	public void ramFileIndexing() throws Exception {
		// 内存读文件索引，内存更新索引，文件添加内存索引
		Directory fileDir = FSDirectory.open(new File("D:/tcm/usercenter/luceneFeedback"));
		Analyzer analyzer = new IKAnalyzer();
		Directory ramDir1 = new RAMDirectory(fileDir);
		IndexWriter ramIndexWriter = new IndexWriter(ramDir1, analyzer, true, MaxFieldLength.LIMITED);

		List<FeedbackPo> feedbackPos = feedbackDaoImpl.selectAllPo();
		for (FeedbackPo feedbackPo : feedbackPos) {
			Document doc = new Document();
			doc.add(new Field("title", feedbackPo.getTitle(), Store.YES, Index.ANALYZED));
			doc.add(new Field("content", feedbackPo.getContent(), Store.YES, Index.ANALYZED));
			doc.add(new Field("subsysName", feedbackPo.getSubsysName(), Store.YES, Index.ANALYZED));
			doc.add(new Field("category", feedbackPo.getSubsysName(), Store.YES, Index.ANALYZED));
			doc.add(new Field("reply", feedbackPo.getReply(), Store.YES, Index.ANALYZED));
			ramIndexWriter.addDocument(doc);
		}
		ramIndexWriter.commit();
		ramIndexWriter.close();

		// 退出时，读取ram中的索引并写入文件索引中
		IndexWriter fileIndexWriter = new IndexWriter(fileDir, analyzer, true, MaxFieldLength.LIMITED);
		fileIndexWriter.addIndexesNoOptimize(ramDir1);
		fileIndexWriter.close();

	}
```

### Lucene查询索引

```
	@Override
	public void query(Analyzer analyzer, String pathname, String field, String keywords) throws Exception {
		Directory fileDir = FSDirectory.open(new File(pathname));
		Directory ramDir1 = new RAMDirectory(fileDir);
		IndexReader ireader = IndexReader.open(ramDir1);
		IndexSearcher isearcher = new IndexSearcher(ireader);
		QueryParser parser = new QueryParser(Version.LUCENE_36, field, analyzer);
		Query query = parser.parse(keywords);
		ScoreDoc[] hits = isearcher.search(query, null, 1000).scoreDocs;
		for (int i = 0; i < hits.length; i++) {
			Document hitDoc = isearcher.doc(hits[i].doc);
			System.out.println(hitDoc.get(field));
		}
		ireader.close();
		ramDir1.close();
	}
```




