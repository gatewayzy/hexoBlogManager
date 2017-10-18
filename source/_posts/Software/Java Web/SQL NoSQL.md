---
title: SQL NoSQL
comments: true
date: 2017-01-12 13:58:19
updated: 2017-01-12 13:58:56
categories: Softwares
tags:
- Java web
---


**说明：**SQL和NoSQL的配置使用，包括MySQL、SQL Server、Redis、MongoDB等。
<!-- more -->

# MySQL
--------------------------------------------------------------------------------------------------------
## sql 语法
---
### sql 内容

* ddl、dml、
* 设置自增起始值：alter table mytable AUTO_INCREMENT=1

## 常用函数
---
### Date_formate日期格式转换
---
将datetime转换为字符串： `Select date_format(feedback_time,'%Y-%c-%d %h:%i:%s') AS feedbackTime`

### Concat拼接函数
---
* 拼接条件 `Select * from table1 where title like concat('%', #{title} ,'%')` 
* 拼接字符串 `SELECT CONCAT('HELLO', ' WORLD') AS exp`
* 将符合条件的同一列中的不同行数据拼接, 以逗号分隔 `SELECT GROUP_CONCAT(name) AS names FROM xxx GROUP BY yy `

### 中文排序问题
---
* 如果存储使用gbk字符集，直接order by就不会出现中文拼音排序的问题。如果mysql使用UTF-8存储中文，则order by中文拼音排序有问题，需要变换编码。
`select * from user order by convert(username USING gbk) asc; `

### 服务器状态信息
---
* 常用show status查看运行统计状态。
* show status like '%queries%'

### Explain语句解析
---
* 使用explain查看sql 语句的解析。
* explain select * from… 

### 选取存在重复项的数据
---

```
SELECT * from qa_dis_dic_code a where EXISTS (SELECT 1 from qa_dis_dic_code b where a.category_2= b.category_2 and b.id<a.id)
```

## 命令行操作
---
* mysql登陆root：mysql -u root -p
	* 在Linux下mysql使用$HOME/.my.cnf读取特定的启动命令和设置，可以在里面设置连接mysql的默认密码，然后将配置文件设置为本人权限 chmod 700 .my.cnf 就可以用默认密码登录，方便shell编程等
* 显示数据库：mysql> show databases;
* 创建用户及授权：
mysql> create database zyy; mysql> create user zyy; mysql> use mysql; mysql> update user set Password values PASSWORD("112233445566") where user='zyy'; mysql> flush privileges;
* 使用更改
mysql> grant all on zyy.* to 'zyy'@'localhost' ;  mysql> flush privileges; mysql> show grants for snort@localhost; localhost换为%指可以远程登陆。
* 修改用户密码：
root登陆，mysql>update mysql.user set password=password('新密码') where User="test" and Host="localhost";mysql>flush privileges;
* 删除用户密码
mysql>Delete FROM user Where User='test' and Host='localhost';
mysql>flush privileges;
mysql>drop database testDB; //删除用户的数据库
* 删除账户及权限：
drop user 用户名@'%';
drop user 用户名@ localhost; 
* 查看表结构信息方法：
1.desc 表名;
2.show columns from 表名;
3.describe 表名;
* 修改类型：ALTER TABLE S MODIFY sno SMALLINT NOT NULL;
* 支持汉字：创表前：set names utf8;或者创建数据库时：create database yan2 default character set utf8;就可以在varchar字段里写汉字了。

### shell编程

假设设置了默认连接的密码，进行下面的shell编程
* 使用单行编程

```
#!/bin/bash
mysql1=$(which mysql)
# 查找mysql命令的位置
$mysql1 defaultdb -u root -X -e 'select * from students'
# 使用默认的密码连接数据库并运行一行命令，运行该脚本即可查看结果，与正常在mysql中运行效果一样
# -X使用XML输出结果
```

* 使用多行编程

```
#!/bin/bash
mysql1=$(which mysql)
# 查找mysql命令的位置
$mysql1 defaultdb -u root <<EOF
show tables;
select * from students;
insert ...
EOF
echo "运行该文件即对mysql进行shell多行，注意多行需要添加EOF开始，结束时的EOF必须为开头"
```
### 定时备份
* windows下添加计划任务，运行如下示例脚本。

``
rem 数据库备份
set target_db=tcm
set file_dir=G:/MySQL_backup/%target_db%
rem 备份文件所在的目录
set file_name=%target_db%_%date:~0,4%-%date:~5,2%-%date:~8,2%
rem 备份文件名
C:
cd C:/Program Files/MySQL/MySQL Server 5.5/bin/
echo 备份到文件：%file_dir%/%file_name%.sql
mysqldump.exe -uroot -p123 %target_db%>%file_dir%/%file_name%.sql
rem D:/develop/MySQL/MySQL Server 5.5/bin/mysqldump.exe为mysqldump程序所在地址，root为用户名，mysql为密码，tcm为待备份的数据库名

```


# SQL Server
--------------------------------------------------------------------------------------------------------
## 简介

## 安装启动
---
* 安装
	* 安装sql server2016等版本，安装功能选择包括服务器和管理工具。
* 启动
	* 启动sql server 管理配置工具配置进行服务状态管理

## 管理配置
---
启动sql server management studio连接数据库，进行数据库、安全性、管理等方面的配置。

* 数据库：进行数据库、数据的增查改删等操作。
* 安全性：进行登录用户名管理、角色管理、加密等管理。

# MongoDB
--------------------------------------------------------------------------------------------------------
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






# Redis
--------------------------------------------------------------------------------------------------------
参考文章：[Redis的三种启动方式](http://www.tuicool.com/articles/aQbQ3u)

## 简介
* 内存数据库，cs架构，服务器将数据存到文件，运行时读入内容，采用键值对。

### redis与memcached的比较
* memcached仅支持key-value数据，且不能数据持久化，redis支持key-value、list、set、hash等结构，支持数据持久化，二者擅长点不同

##  server安装
---
### Linux下

* 安装：
	* 下载源码并解压cd src，make，（是否安装build-essential，make MALLOC=jemalloc等问题）make all等命令，主要看运行中的提示信息。不推荐make insatll成service。
	* 使用源安装
* 源安装的服务命令：service redisd start | stop | restart...
* 启动server：
	* ./redis-server &，默认端口6379，&是为了后台运行，不使用配置文件（无密码等配置）
	* ./redis-server path/redis.conf & ，指定使用的配置文件
* 查看服务：
	* ps -ef |grep redis
	* ps -aux |grep redis
* 停止server：
	* redis-cli -a passwd shutdown
	* redis-cli -h 127.0.0.1 -p 6379 shutdown
	* ps -aux |grep redis 然后运行： kill -9 pid


###  Windows下

* 下载压缩包进行解压。
* 作为服务：在目录下运行命令`redis-server --service-install redis.windows.conf` 注意修改相应的redis.config
* 卸载服务`redis-server --service-uninstall`

## server 配置
* 设置server密码：
	* 打开redis.conf配置文件，找到requirepass，然后修改requirepass一行为:requirepass yourpassword
	* 然后使用./redis-server path/redis.conf & ，指定使用的配置文件启动server
* 配置tomcat的session共享
	* 由于redis存在安全漏洞，应确保redis server启用密码。
	* tomcat的lib中添加相应的jar包，如tomcat-redis-session-manager-2.0.0.jar、commons-pool-1.6.jar、jedis-2.1.0.jar
	* 在tomcat的conf/context.xml中配置<Context>添加如下内容。

```
    <WatchedResource>WEB-INF/web.xml</WatchedResource>  
    <Valve className="com.orangefunction.tomcat.redissessions.RedisSessionHandlerValve" />
    <Manager className="com.orangefunction.tomcat.redissessions.RedisSessionManager"
           host="10.15.82.57"
           port="6379"
           password="password"
           database="0"
           maxInactiveInterval="60" />

```
## client使用
---
###  client 连接server
* 运行./redis-cli，默认连接本地的server端口
* 使用可视化管理工具RedisDesktopManager等

### 语法


### Jedis


##  问题与解决
---
### 密码设置

* 修改config文件，使用该配置启动server，上文已详述。

### 数据库备份与恢复

* 方案：重写所有数据
	* 使用程序查询所有数据，写到新的redis
* 方案：rdb快照
	* 关闭server，可将dump.rdb视为数据库备份文件（数据库快照）
	* 将dump.rdb复制到新的redis下，启动即可，注意会覆盖原有的rdb文件问题。
	* 也可是使用client连接server，运行auth密码之后，使用SAVE命令生成rdb，存储路径在conf文件的working directory设置。
* 方案：aof拓展
	





