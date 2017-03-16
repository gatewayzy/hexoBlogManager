---
title: JavaTools
comments: true
date: 2017-01-12 13:58:10
updated: 2017-01-12 13:58:56
categories: Softwares
tags:
- tools
---

**说明：**JavaTools配置使用，包括jdbc、分词工具、quartz定时等。
<!-- more -->


---
参考文章：

## Ajax跨域请求允许
---
Ajax在请求本项目之外的网站时，产生跨域请求，所有网站默认是拒绝跨域请求的。

### 修改被请求网站响应设置

* 只有前端访问后端存在跨域问题，应考虑通过后端进行httpclient等方式请求再封装避免跨域问题。
* 通过设置请求网站的响应允许跨域请求。

```
@RequestMapping(value="/posttrack",method = RequestMethod.POST)
public void posttrack(@RequestParam String data,HttpServletRequest request,HttpServletResponse response) {
	response.addHeader("Access-Control-Allow-Origin","*");
	return;
}
```

## 中文分词工具
---
### IKAnalyzer与Lucene

### IKAnalyzer特点
* IKAnalyzer支持中文分词，支持拓展词典和停用词。
* Lucene在全文上建立索引文件，通过java等方式进行查询。中文中经常使用IKAnalyzer作为分词工具，将全文进行分词后建立索引。

### 配置

* 后缀名.dic的词典文件，必须使用文档里所说的无BOM的UTF-8编码保存的文件。如果不确定什么是无BOM的UTF-8编码，最简单的方式就是用Notepad++编辑器打开，Encoding->选择 Encoding in UTF-8 without BOM，然后保存。
* 项目preferences里编码选择utf8。
* 词典和IKAnalyzer.cfg.xml配置文件的路径问题。IKAnalyzer.cfg.xml必须在src根目录下。词典可以任意放，但是在IKAnalyzer.cfg.xml里要配置对。如下：我的两个词典文件my.dic 和 mine.dic 放在src下的com.org.config包下，注意com前面一定不要加/，否则是绝对路径找不到。
* 注意：maven项目中IKAnalyzer.cfg.xml放在resources下即可，里面路径以resources为/。

### 配置demo

* IKAnalyzer.cfg.xml（IK是3.2.8）

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE properties SYSTEM "http://java.sun.com/dtd/properties.dtd">  
<properties>  
    <comment>IK Analyzer 扩展配置</comment>
    <!-- 用户可以在这里配置自己的扩展字典 -->
     <entry key="ext_dict">/dic/ext_dic.dic</entry> 
     <!-- 用户可以在这里配置自己的扩展停止词字典    -->
    <entry key="ext_stopwords">/dic/stopwords.dic</entry> 
</properties>
```

* MyLucene.java（Lucene是3.5.0）

```
package lucene;
import java.io.IOException;
import java.io.StringReader;
import org.apache.lucene.analysis.Analyzer;
import org.apache.lucene.analysis.TokenStream;
import org.apache.lucene.analysis.tokenattributes.CharTermAttribute;
import org.apache.lucene.document.Document;
import org.apache.lucene.document.Field;
import org.apache.lucene.document.Field.Index;
import org.apache.lucene.document.Field.Store;
import org.apache.lucene.index.IndexReader;
import org.apache.lucene.index.IndexWriter;
import org.apache.lucene.index.IndexWriterConfig;
import org.apache.lucene.index.IndexWriterConfig.OpenMode;
import org.apache.lucene.queryParser.QueryParser;
import org.apache.lucene.search.IndexSearcher;
import org.apache.lucene.search.Query;
import org.apache.lucene.search.ScoreDoc;
import org.apache.lucene.search.TopDocs;
import org.apache.lucene.store.Directory;
import org.apache.lucene.store.FSDirectory;
import org.apache.lucene.store.RAMDirectory;
import org.apache.lucene.util.Version;
import org.wltea.analyzer.lucene.IKAnalyzer;

public class MyLucene {
	 public static void main(String[] args) {  
		// 使用Lucene分词  
		//Lucene Document的域名
		String fieldName = "text";
		//检索内容
		String text = "据说WWDC要推出iPhone6要出了？与iPhone5s土豪金相比怎样呢？";
		//实例化IKAnalyzer分词器
		Analyzer analyzer = new IKAnalyzer();

		Directory directory = null;
		IndexWriter iwriter = null;
		IndexReader ireader = null;
		IndexSearcher isearcher = null;
		try {
		    //建立内存索引对象
		    directory = new RAMDirectory();     

		    //配置IndexWriterConfig
		    IndexWriterConfig iwConfig = new IndexWriterConfig(Version.LUCENE_34 , analyzer);
		    iwConfig.setOpenMode(OpenMode.CREATE_OR_APPEND);
		    iwriter = new IndexWriter(directory , iwConfig);
		    //写入索引
		    Document doc = new Document();
		    doc.add(new Field("ID", "10000", Field.Store.YES, Field.Index.NOT_ANALYZED));
		    doc.add(new Field(fieldName, text, Field.Store.YES, Field.Index.ANALYZED));
		    iwriter.addDocument(doc);
		    iwriter.close();

		    //搜索过程**********************************
		    //实例化搜索器   
		    ireader = IndexReader.open(directory);
		    isearcher = new IndexSearcher(ireader);            

		    String keyword = "iPhone5s土豪金";            
		    //使用QueryParser查询分析器构造Query对象
		    QueryParser qp = new QueryParser(Version.LUCENE_34, fieldName, analyzer);
		    qp.setDefaultOperator(QueryParser.AND_OPERATOR);
		    Query query = qp.parse(keyword);
		    System.out.println("Query = " + query);

		    //搜索相似度最高的5条记录
		    TopDocs topDocs = isearcher.search(query , 5);
		    System.out.println("命中：" + topDocs.totalHits);
		    //输出结果
		    ScoreDoc[] scoreDocs = topDocs.scoreDocs;
		    for (int i = 0; i < topDocs.totalHits; i++){
		        Document targetDoc = isearcher.doc(scoreDocs[i].doc);
		        System.out.println("内容：" + targetDoc.toString());
		    }

		} catch (Exception e) {
		    e.printStackTrace();
		} finally{
		    if(ireader != null){
		        try {
		            ireader.close();
		        } catch (Exception e) {
		            e.printStackTrace();
		        }
		    }
		    if(directory != null){
		        try {
		            directory.close();
		        } catch (IOException e) {
		            e.printStackTrace();
		        }
		    }
		}
	 }
}
```

## Quartz 定时任务
---

* 引入Quartz版本

```
		<dependency>
			<groupId>org.quartz-scheduler</groupId>
			<artifactId>quartz</artifactId>
			<version>1.7.3</version>
		</dependency>
```
* 测试任务：任务是普通类。

```
package quartz;
import java.text.SimpleDateFormat;
import java.util.Date;

import org.quartz.Job;
import org.quartz.JobExecutionContext;
import org.quartz.JobExecutionException;

public class TestJob implements Job {
	SimpleDateFormat DateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
	Date d = new Date();
	String returnstr = DateFormat.format(d);

	public void execute(JobExecutionContext arg0) throws JobExecutionException {
		System.out.println(returnstr + " a testJob is running★★★★★★★★★★★");
	}

}
```

* 任务管理工具类

```
package quartz;

import java.text.ParseException;
import org.quartz.CronTrigger;
import org.quartz.Job;
import org.quartz.JobDetail;
import org.quartz.Scheduler;
import org.quartz.SchedulerException;
import org.quartz.SchedulerFactory;
import org.quartz.Trigger;
import org.quartz.impl.StdSchedulerFactory;

/**
 * @Title:Quartz管理类
 * 
 */
public class QuartzManager {
	private static SchedulerFactory sf = new StdSchedulerFactory();
	private static String JOB_GROUP_NAME = "group1";
	private static String TRIGGER_GROUP_NAME = "trigger1";

	/**
	 * 添加一个定时任务，使用默认的任务组名，触发器名，触发器组名
	 */
	public static void addJob(String jobName, Job job, String time) throws SchedulerException, ParseException {
		Scheduler sched = sf.getScheduler();
		JobDetail jobDetail = new JobDetail(jobName, JOB_GROUP_NAME, job.getClass());// 任务名，任务组，任务执行类
		// 触发器
		CronTrigger trigger = new CronTrigger(jobName, TRIGGER_GROUP_NAME);// 触发器名,触发器组
		trigger.setCronExpression(time);// 触发器时间设定
		sched.scheduleJob(jobDetail, trigger);
		// 启动
		if (!sched.isShutdown())
			sched.start();
	}

	/**
	 * 添加一个定时任务
	 */
	public static void addJob(String jobName, String jobGroupName, String triggerName, String triggerGroupName, Job job,
			String time) throws SchedulerException, ParseException {
		Scheduler sched = sf.getScheduler();
		JobDetail jobDetail = new JobDetail(jobName, jobGroupName, job.getClass());// 任务名，任务组，任务执行类
		// 触发器
		CronTrigger trigger = new CronTrigger(triggerName, triggerGroupName);// 触发器名,触发器组
		trigger.setCronExpression(time);// 触发器时间设定
		sched.scheduleJob(jobDetail, trigger);
		if (!sched.isShutdown())
			sched.start();
	}

	/**
	 * 修改一个任务的触发时间(使用默认的任务组名，触发器名，触发器组名)
	 */
	public static void modifyJobTime(String jobName, String time) throws SchedulerException, ParseException {
		Scheduler sched = sf.getScheduler();
		Trigger trigger = sched.getTrigger(jobName, TRIGGER_GROUP_NAME);
		if (trigger != null) {
			CronTrigger ct = (CronTrigger) trigger;
			ct.setCronExpression(time);
			sched.resumeTrigger(jobName, TRIGGER_GROUP_NAME);
		}
	}

	/**
	 * 修改一个任务的触发时间
	 */
	public static void modifyJobTime(String triggerName, String triggerGroupName, String time)
			throws SchedulerException, ParseException {
		Scheduler sched = sf.getScheduler();
		Trigger trigger = sched.getTrigger(triggerName, triggerGroupName);
		if (trigger != null) {
			CronTrigger ct = (CronTrigger) trigger;
			// 修改时间
			ct.setCronExpression(time);
			// 重启触发器
			sched.resumeTrigger(triggerName, triggerGroupName);
		}
	}

	/**
	 * 移除一个任务(使用默认的任务组名，触发器名，触发器组名)
	 */
	public static void removeJob(String jobName) throws SchedulerException {
		Scheduler sched = sf.getScheduler();
		sched.pauseTrigger(jobName, TRIGGER_GROUP_NAME);// 停止触发器
		sched.unscheduleJob(jobName, TRIGGER_GROUP_NAME);// 移除触发器
		sched.deleteJob(jobName, JOB_GROUP_NAME);// 删除任务
	}

	/**
	 * 移除一个任务
	 */
	public static void removeJob(String jobName, String jobGroupName, String triggerName, String triggerGroupName)
			throws SchedulerException {
		Scheduler sched = sf.getScheduler();
		sched.pauseTrigger(triggerName, triggerGroupName);// 停止触发器
		sched.unscheduleJob(triggerName, triggerGroupName);// 移除触发器
		sched.deleteJob(jobName, jobGroupName);// 删除任务
	}
}
```
* 测试函数

```
package quartz;

import java.text.SimpleDateFormat;
import java.util.Date;

public class QuartzTest {

	public static void main(String[] args) {
		SimpleDateFormat DateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
		Date d = new Date();
		String returnstr = DateFormat.format(d);

		TestJob job = new TestJob();
		String job_name = "11";
		try {
			// 每2秒钟执行一次
			System.out.println(returnstr + "【系统启动】");
			QuartzManager.addJob(job_name, job, "0/2 * * * * ?");
			Thread.sleep(10 * 1000);

			// 更改任务设置，好像不起作用，可以移除再添加一个任务
			System.out.println(returnstr + "【修改时间】");
			QuartzManager.modifyJobTime(job_name, "0/5 * * * * ?");
			Thread.sleep(10 * 1000);

			// 移除任务
			System.out.println(returnstr + "【移除定时任务】");
			QuartzManager.removeJob(job_name);
			Thread.sleep(10 * 1000);

			// 添加任务
			System.out.println(returnstr + "【添加定时任务】");
			QuartzManager.addJob(job_name, job, "0/5 * * * * ?");

		} catch (Exception e) {
			e.printStackTrace();
		}
	}
}
```

## Sitemesh
---
### sitemesh简介

将url请求进行过滤，使用配置的界面进行渲染。

### 配置

* SiteMesh 是一个网页布局和修饰的框架，利用它可以将网页的内容和页面结构分离，以达到页面结构共享的目的。
* web.xml中配置filter，由于按filter按先后顺序执行，所以更小的filter放在前面。比如sitemesh-filter放在jfinal-filter之前。

```
	<filter>
		<filter-name>sitemesh</filter-name>
		<filter-class>org.sitemesh.config.ConfigurableSiteMeshFilter</filter-class>
	</filter>
	<filter-mapping>
		<filter-name>sitemesh</filter-name>
		<url-pattern>/*</url-pattern>
	</filter-mapping>
```
* sitemesh3.xml中配置模板路径等信息。

```
<?xml version="1.0" encoding="UTF-8"?>
<sitemesh>
	
	<content-processor>
     <tag-rule-bundle class="com.usercenter.tools.sitemesh.MyTagRuleBundle" />
   </content-processor>
   
	<!-- 指明满足/*的页面被指定的模板装饰 -->
	<mapping path="/to*" decorator="/WEB-INF/views/commonpages/model.jsp" />
	
	<!-- 指明满足排除条件的页面不被装饰 -->
	<!-- <mapping path="/statics/*" exclude="true" /> -->
</sitemesh>
```

## JDBC
---
JDBC是java为数据库操作提供的接口，具体实现由各数据库厂家决定。

### 基础知识

* 驱动类名：对应相应的驱动jar包中的类路径。
* 驱动URL：协议、子协议、主机、端口、数据库等，如`jdbc:sqlserver://10.15.82.58:1433;DatabaseName=EngineeringMedicine`
* Statement/PreparedStatement/CallableStatement区别
	* Statement最原始最普通，可查询、添加，查询容易被sql注入。
	* PreparedStatement预编译的sql，安全性比较高，本身使用set方法，对重复的查询高效。
	* CallableStatement调用存储过程。存储过程是数据库底层实现并优化的一系列数据操作。

### JDBC使用

#### 使用流程

一般的步骤为：
1.	搭建环境：创建工程，导入数据库包，创建数据库。
2.	编程，加载数据库驱动
	* 方式一：Class.forName(driver);
	* 方式二：DriverManager.registerDriver(new com.mysql.jdbc.Driver());//不推荐，因为会加载两次driver。
3.	建立连接
Connection connection = DriverManager.getConnection(url, user, pwd);//JDBC操作核心
4.	创建数据库操作对象，发送sql
Statement st = connection.createStatement();
ResultSet rs = st.executeUpdate("insert into user1 (name) values('zhangsan')");
5.	从代表结果集的ResultSet中取出数据等操作。
6.	断开数据库连接，释放资源。statement.close();connection.close();

#### 示例代码

* Sql操作

```
public class MySQL {
	public static final void main(String[] args) throws Exception {
		/**类名 */
		String driver = "com.mysql.jdbc.Driver";
		String url = "jdbc:mysql://127.0.0.1:3306/mytest";// 主协议：子协议/ip：port/db
		String user = "root";
		String pwd = "1234";
		Connection connection = null; //接口
		Statement statement = null;
		
		try {
			/**连接配置 */
			// 1 加载驱动类 
			Class.forName(driver);
			// 另一种方式如下，但不推荐，因为会加载两次driver
			// DriverManager.registerDriver(new com.mysql.jdbc.Driver());
			
			// 2 获取连接
			connection = DriverManager.getConnection(url, user, pwd);
			// 3 通过statement向数据库发出sql指令
			//statement = connection.createStatement();//createStatement()不支持游标向后（向上）
			statement = connection.createStatement(ResultSet.TYPE_SCROLL_INSENSITIVE, ResultSet.CONCUR_READ_ONLY);
			
			int n = statement.executeUpdate("insert into user1 (name) values('zhangsan')");//此方法进行增改删CRUD之cud
			System.out.println(n + "行已添加到：" + connection);//输出connection可以查看数据库连接信息
			
			/**事务自动提交设置 */
			connection.setAutoCommit(false);//默认是自动提交
			connection.commit();
			
			/**ResultSet */
			ResultSet resultSet = statement.executeQuery("select * from user1");//此方法进行查create,retrive,update,del 
			while(resultSet.next()){//数据最后的next返回false
				System.out.println(resultSet.getInt(1) + resultSet.getString("name"));//游标所指数据
			}
			resultSet.beforeFirst();//重新使用resultSet游标，回到第一行
			System.out.println("重新使用游标");
			while(resultSet.next()){//数据最后的next返回false
				System.out.println(resultSet.getInt(1) + resultSet.getString("name"));//游标所指数据
			}
			System.out.println("resultSet的其他方法");
			resultSet.beforeFirst();//最前面的前面
			resultSet.afterLast();//最后面的后面
			resultSet.last();//定位到最后
			resultSet.previous();
			resultSet.absolute(2);//定位到第2行
			System.out.println(resultSet.getString("id"));
			
		} catch (Exception e) {
			e.printStackTrace();
			connection.rollback();//不是自动提交的时候，commit之前的出错可以回滚
		} finally {
			if (statement != null) {
				try {
					statement.close();
				} catch (SQLException e) {
					e.printStackTrace();
				}
				statement = null; // 使用垃圾回收机制
			}
			if (connection != null) {
				try {
					connection.close();
				} catch (SQLException e) {
					e.printStackTrace();
				}
				connection = null;
			}
		}
	}
}
```

* SqlHelper/DbUtil
由于数据库操作较多，所以可以进行封装。

```
public class DbUtil {
	/**如果并发量比较多就不能设置成static*/
	private static Connection connection = null;
	private static Statement statement= null;
	private static ResultSet resultSet = null;
	
	/**数据库参数*/
	private static String url;
	private static String userName;
	private static String driver;
	private static String passwd;
	private static Properties properties;
	private static FileInputStream fileInputStream;
	
	//加载驱动，只需一次
	static{
		try {
			//读取数据库配置文件
			fileInputStream = new FileInputStream("src/main/resources/jdbc.properties");
			properties = new Properties();//实例化
			properties.load(fileInputStream);
			
			url = properties.getProperty("url");
			userName = properties.getProperty("userName");
			driver = properties.getProperty("driver");
			passwd = properties.getProperty("passwd");
			
			Class.forName(driver);//加载驱动类
		} catch (Exception e) {
			e.printStackTrace();
			//加载失败，可以退出程序了
		}
	}
	
	public static Connection getConnection() throws Exception{
		connection = DriverManager.getConnection(url, userName, passwd);
		return connection;
	}
	
	/**
	 * 测试入口
	 * @param args
	 * @throws Exception
	 */
	public static void main(String[] args) throws Exception {
		System.out.println(DbUtil.getConnection());
	}
	
}
```

* PreparedStatement
PreparedStatement采用字段名与？传参的方式，可以防止sql注入。如在statement中添加“or 1=1”的方式，所以一般不用statement。

```
PreparedStatement ps = null;
ps = connection.prepareStatement("select * from user1 where id = ? and name = ?");
ps.setInt(1, 1);	//从1开始
ps.setString(2, "zhangsan");
ResultSet resultSet = ps.executeQuery();
```

### Sql注入

#### 基本概念

* Sql注入漏洞是因为系统对传递到后台数据库的参数限制存在漏洞，导致可以通过设计特定的语句获取到超出设计预期的数据库权限。
* 例如：后台实现如果是select * from user where name= str，设计初衷是只能选取一个用户，但如果被用户输入str的值为 “a” or 1=1，则会输出所有数据。

#### 防止sql注入

* **使用安全的sql封装语句或框架**：一些封装的语句或框架会将参数作为一个整体，不会对参数内部进行解析成sql条件，如jdbc的preparedStatement类、mybatis的#{para}等。
* **参数过滤**：一般sql注入的语句会很长很复杂，可以限定参数长度并且禁止含有*、%特殊字符。

