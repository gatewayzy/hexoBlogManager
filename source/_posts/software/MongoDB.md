---
title: MongoDB
comments: true
date: 2017-01-12 13:58:13
updated: 2017-01-12 13:58:56
categories: Softwares
tags:
- MongoDB
---

**说明：**MongoDB配置，MongoDB的使用。
<!-- more -->


## server安装
---
###  Windows下
---
一般将解压后的MongoDB安装为windows服务
* 用管理员cmd进入其bin目录，运行：
`mongod --logpath "D:\Program Files\mongodb\data\mongo.log" --logappend --dbpath "D:\Program Files\mongodb\data" --directoryperdb --serviceName "MongoDB" --serviceDisplayName "MongoDB" –install`
其中两个路径分别设置日志路径、数据文件路径，还设置了日志是追加方式、服务命名、服务名。- -directoryperdb说明每个DB都会新建一个目录。- -install安装，mongod - -remove删除服务。

* 或者将上面的配置值写入新建的mongodb.config中，内容如下。修改并添加好对应的文件路径，到mongod.exe所在目录使用管理员cmd运行：
`mongod --config “D:\Program Files\mongodb-win32-x86_64-2008plus\mongodb.config” -install`

```
dbpath=D:\ProgramData\mongodb
logpath=D:\Program Files\mongodb-win32-x86_64-2008plus\log\mongo.log
serviceName=MongoDB
serviceDisplayName=MongoDB

```



注意：server启动失败的话，应该通过查看日志解决问题。

##  server配置
---
### 配置文件
* 密码设置

## client使用
---
### client连接server
* 浏览器访问http://127.0.0.1:27017/显示“It looks like you are...”说明server正在运行。
* Windows下可以运行自带的mongo.exe(直接执行exe或者命令行执行exe)，默认连接本地server，或运行`mongo --port27017`或`mongo 10.15.82.57:27017`
* 可视化管理工具Robomongo(客户端管理)、Mongovision(网页管理)。

### 常用命令
* 手册：mongod数据库设置，mongo是mongo shell……
* 访问网络mongodb：mongo 10.15.82.57:27017 #端口默认27017
* show dbs #显示数据库
* use myTest不存在数据库会默认创建
* db 显示当前使用的db
* db.disease.findOne() #显示一条数据，默认使用与db同名的collection
* db.createCollection("classes ") 在当前db创建数据集，有众多索引选项等
* show collections 显示当前db的collections
* db.getCollection("classes").insert({"id":1,"name":"zhangsan1"}) 在当前db的所选集合中添加一条数据，与下面写法相等，若集合不存在会默认使用db名作为集合名
* db.classes.insert({"id":2,"name":"zhangsan2"})
* db.classes.find() 显示所有数据，不能省略集合名
* db.classes.findone() 显示一个数据
* 退出连接：cmd：exit

###  MongoDB的第三方驱动
*  Mongodb-java-driver
	*  Mongodb的java版本实现中，3.4版本对之前2.x版本有很大改动。3.4的示例代码如下。


```

package study.frame.mongoDB;

import com.mongodb.*;
import com.mongodb.client.FindIterable;
import com.mongodb.client.MongoCollection;
import com.mongodb.client.MongoCursor;
import com.mongodb.client.MongoDatabase;
import com.mongodb.client.model.Filters;
import com.mongodb.client.model.Updates;
import com.mongodb.client.model.geojson.Point;
import com.mongodb.client.model.geojson.Position;
import org.bson.Document;

import java.util.*;
import java.util.logging.Filter;

/**
 * Created by dell on 2016/12/12.<br/>
 * 注意： mongodb 3.x对之前版本进行了大量改动
 * 参考文章： http://blog.csdn.net/autfish/article/details/51356537
 */
public class TestMongoDB {

    public static void main(String[] args) {
        System.out.println("[info] starting...");


        // 使用用户名、数据库、密码连接数据库
        //MongoCredential credential = MongoCredential.createCredential("guoxin01", "guoxin", "123456".toCharArray());
        //ServerAddress addr = new ServerAddress("192.168.1.137", 27017);
        //MongoClient passwdClient = new MongoClient("10.15.82.57", 27017);

        //MongoClient mongoClient = new MongoClient("10.15.82.57", 27017);

        MongoClient mongoClient = new MongoClient("127.0.0.1", 27017);
        MongoDatabase mongoDatabase = mongoClient.getDatabase("myTest");
        System.err.println("[info] connection ok! use database");
        mongoDatabase.drop();//删除db下所有集合，但是同名的集合默认会存在的
        mongoDatabase.createCollection("myTest");//创建一个集合
        MongoCollection<Document> collection = mongoDatabase.getCollection("classes");
        System.out.println("[info] use collection");

        collection.drop();//删除集合原有数据
        /*
        * 检索所有文档
        * 1. 获取迭代器
        * 2. 获取游标
        * 3. 用游标检索文档
        * */
        int count = (int) collection.count();
        System.out.println("[info] total doc after delete: " + count);

        FindIterable<Document> findIterable = collection.find();
        MongoCursor<Document> mongoCursor = findIterable.iterator();
        while (mongoCursor.hasNext()) {
            Document doc = mongoCursor.next();
            System.out.println("[info] selected doc: " + doc.get("name"));
        }

        /*增加一个数据*/
        Document newDoc = new Document("id", 1).append("name", "zhangsan1").append("text", "可以用map或append建doc");
        collection.insertOne(newDoc);
        /*增加一个数据*/
        Map<String, Object> newMap = new HashMap<>();
        newMap.put("id", 2);
        newMap.put("name", "name2");
        Document document = new Document(newMap);
        collection.insertOne(document);

        /*增加多个数据*/
        Document doc1 = new Document("name", "tom").append("raid", Arrays.asList(10, 10)).append("gps", new Point(new Position(10, 10)));
        Document doc2 = new Document("name", "jone").append("raid", Arrays.asList(10.1, 10)).append("gps", new Point(new Position(10.1, 10)));
        Document doc3 = new Document("name", "john").append("raid", Arrays.asList(10, 10.1)).append("gps", new Point(new Position(10, 10.1)));
        Document doc4 = new Document("name", "jack").append("raid", Arrays.asList(9.9, 10)).append("gps", new Point(new Position(9.9, 10)));
        Document doc5 = new Document("name", "mary").append("raid", Arrays.asList(10, 9.9)).append("gps", new Point(new Position(10, 9.9)));
        Document doc6 = new Document("name", "abby").append("raid", Arrays.asList(10.2, 10)).append("gps", new Point(new Position(10.2, 10)));
        Document doc7 = new Document("name", "adam").append("raid", Arrays.asList(10.3, 10)).append("gps", new Point(new Position(10.3, 10)));
        Document doc8 = new Document("name", "barry").append("raid", Arrays.asList(10.4, 10)).append("gps", new Point(new Position(10.4, 10)));
        Document doc9 = new Document("name", "anne").append("raid", Arrays.asList(10.5, 10)).append("gps", new Point(new Position(10.5, 10)));
        collection.insertMany(Arrays.asList(doc1, doc2, doc3, doc4, doc5, doc6, doc7, doc8, doc9));

        /*条件查询*/
        findIterable = collection.find(new Document("id", 2));
        Iterator<Document> iteSelect = findIterable.iterator();
        while (iteSelect.hasNext()) {
            System.out.println("[info] 条件查找id为2的：" + iteSelect.next().getString("name"));
        }

        /*查询并修改*/
        collection.findOneAndReplace(new Document("id", 2), new Document("name", "name2"));//repalce是替换一个
        //3.4版本中的过滤词在Filters，更改词在Updates
        collection.findOneAndUpdate(Filters.eq("id", 1), Updates.set("raid", "这是使用updates设置的值"));
        //collection.updateOne(new Document("id",1),set("rank", 100));

        //$geoWithinPolygon 在多边形内搜索
        List<Double> p1 = new ArrayList<>();
        List<Double> p2 = new ArrayList<>();
        List<Double> p3 = new ArrayList<>();
        p1.add(10d);
        p1.add(10d);
        p2.add(10.1);
        p2.add(10.16);
        p3.add(10.2);
        p3.add(10d);
        List<List<Double>> polygon = Arrays.asList(p1, p2, p3);
        findIterable = collection.find(Filters.geoWithinPolygon("raid", polygon));
        Iterator<Document> iteSelGeo = findIterable.iterator();
        while (iteSelGeo.hasNext()) {
            System.out.println("[info] 多边形内查找到：" + iteSelGeo.next().getString("name"));
        }

        /*使用Filters删除多个数据*/
        collection.deleteMany(Filters.or(Filters.eq("name", "tom"), Filters.eq("name", "jack")));

        System.out.println("[info] total doc finally: " + collection.count());
        System.out.println("[info] over!!!");

        mongoClient.close();
    }
}

```

## 问题与解决
---
### 模糊查询速度太慢
* Mongodb自带的模糊检索可能效率很低。
	* 一种解决方案是使用mongodb存储全文，然后将需要查找的内容使用Lucene建立索引，利用Lucene进行模糊查询并返回结果，也就是说不使用mongodb进行检索业务。
