---
title: SQL基本知识
comments: true
date: 2017-09-01 13:58:10
updated: 2017-09-01 13:58:10
categories: Database
tags:
- SQL
---

**说明：**SQL基本知识：SQL组成、SQL示例等。
<!-- more -->

## SQL组成

## 范式


## 事务


* 数据库的事务，事务的含义？一组相互依赖的操作行为，只要有一个操作行为失败，则整个事务失败。流程为：开始事务、执行crud语句、执行成功则提交事务否则撤销事务进行回滚。
* **事务的ACID四个基本要素**？数据库事务正确执行的四个基本要素的缩写：
	* 原子性  整个事务中的所有操作，要么全部完成，要么全部不完成进行回滚
	* 一致性  事务必须始终保持系统处于一致的状态，不管在任何给定的时间并发事务有多少
	* 隔离性  隔离状态执行事务，使它们好像是系统在给定时间内执行的唯一操作
	* 持久性 Durability [ˌdjʊrəˈbɪlətɪ]  在事务完成以后，该事务对数据库所作的更改便持久的保存在数据库之中，并不会被回滚
* **事务的隔离级别**？为了兼顾并发效率和异常控制，在标准SQL规范中，定义了4个事务隔离级别
	* Read uncommitted 读未提交：允许一个更新语句没有提交,但是别的事务可以读到这个改变。最低隔离级别，不安全。可能出现**脏读、不可重复读、幻像读(幻读/虚读)** *脏读：事务1中将10变成20，但是未提交事务，事务2读到20，但是事务1之后事务提交发生错误并进行回滚，事务2的20就是脏读。*
	* Read committed 读提交：只能读取到执行commit之后的数据。Sql Server，Oracle等多数数据库默认都是该级别。可能出现**不可重复读、幻像读**  *不可重复读是指在对于数据库中的某个数据，一个事务范围内多次查询却返回了不同的数据值，这是由于在查询间隔，被另一个事务修改并提交了。比如，事务1读取10，事务2读取10并修改为20并正确提交事务，事务1再读就是20，10就是不可重复读*。不可重复读和脏读的区别是，脏读是某一事务读取了另一个事务未提交的脏数据，而不可重复读则是读取了前一事务提交的数据。
	* Repeatable read 重复读：在同一个事务里面先后执行同一个查询语句的时候,得到的结果是一样的。MySQL的默认隔离级别。可能出现**幻像读**  *幻象读：事务1读取所有用户，事务2插入或删除一个用户并提交了事务，事务1再次读取所有用户与之前不一样*
	* Serializable 序列化：事务顺序执行，不允许事务并行。最高隔离级别，代价也最高，性能低，很少用。避免脏读、不可重复读、幻像读

## 锁
* 数据库锁、并发锁？
	* 数据库系统采用锁机制来实现事务的隔离性。各数据库采用的锁的理论基本上是一致的，只是具体实现有所不同。
	* 按照封装程度，锁分为：共享锁、独占锁和更新锁。
	* 1、共享锁  
		* 共享锁用于读数据操作，它是非独占的，允许其他事务同时读取锁定的资源，但不允许其他事务更新它。
		* 加锁条件：当一个事务执行select语句时，数据库系统会为这个事务分配一把共享锁，来锁定被查询的数据。
		* 解锁条件：默认情况下，数据被读取后，数据库系统立即解除共享锁。
		* 兼容性：如果数据资源上放置了共享锁，还能再放置共享锁和更新锁。
	* 2、独占锁
		* 独占锁也叫排他锁，使用于修改数据的场合。它锁定的资源，其他事务不能读取也不能修改。
		* 加锁条件：当一个事务执行insert、update或delete语句是，数据库会放置一把排他锁。
		* 解锁条件：独占锁一直到事务结束才能被解除。
		* 兼容性：不能与其他锁兼容。
	* 3、更新锁
		* 加锁条件：在更新操作的初始化阶段来锁定可能要修改的资源，这可避免使用共享锁造成的死锁现象。
		* 兼容条件：更新锁与共享锁是兼容的

	* 下面以一条update语句执行过程为例阐述一下更新锁的作用：update account set balance = 1000 where id = 10;
	* 使用共享锁，更新数据操作分两步：
		1、获得一个共享锁，读取account表中id为10的记录
		2、将共享锁升级为独占锁，再执行更新操作
		此种情况下，很容易产生死锁问题。
	* 使用更新锁，更新数据的操作分两步：
		1、获得一个更新锁，读取account表中id为10的记录
		2、将共享锁升级为独占锁，再执行更新操作

    * 我们在编写应用程序时，也会用到锁，这就是所谓的悲观锁和乐观锁
		* 乐观锁：完全依靠数据库的事务隔离级别来自动管理锁的工作
		* 悲观锁：悲观锁对事态很悲观，当事务访问数据资源时，先要锁定资源
	* Oracle、SqlServer和Mysql都支持select ... from update这样的语句，以上语句显示指定独占锁来锁定查询的记录。执行该条查询语句的事务持有这把锁，直到事务结束才会释放锁。

## 主从备份
* 数据库主从备份？
	* 对于一个mysql服务器，一般有两个线程来负责复制和被复制。当开启复制之后。
	* 1. 作为主服务器Master，会把自己的每一次改动都记录到二进制日志 Binarylog 中。（从服务器会负责来读取这个log， 然后在自己那里再执行一遍）
	* 2. 作为从服务器Slave，会用master上的账号登陆到 master上，读取master的Binarylog, 写入到自己的中继日志 Relaylog，然后自己的sql线程会负责读取这个中继日志，并执行一遍。通过执行相同的sql语句实现主从一致性。

## 索引

* 索引类型分为Primary key主键索引、Unique索引、Normal普通索引、Fulltext全文索引。
* 索引方法有btree和hash两种索引方法。
* 创建索引可以使用alter table和create index的方式。

### alter table
1. PRIMARY  KEY（主键索引）
mysql>ALTER  TABLE  `table_name`  ADD  PRIMARY  KEY (  `column`  ) 
2. UNIQUE(唯一索引)
mysql>ALTER  TABLE  `table_name`  ADD  UNIQUE (`column` ) 
3. INDEX(普通索引)
mysql>ALTER  TABLE  `table_name`  ADD  INDEX index_name (  `column`  )
4. FULLTEXT(全文索引)
mysql>ALTER  TABLE  `table_name`  ADD  FULLTEXT ( `column` )
5. 多列索引
mysql>ALTER  TABLE  `table_name`  ADD  INDEX index_name (  `column1`,  `column2`,  `column3`  )

* 举例：ALTER  TABLE  users  ADD  INDEX index1 (userid desc,  username asc)，将会添加index1对id和name进行Normal联合索引，默认使用BTREE索引（而不是HASH）

### create index
类似于alter的类型：
CREATE INDEX index_name ON `table_name` (`column_list`)
CREATE UNIQUE INDEX index_name ON `table_name` (`column_list`)

### 查看索引
show index from tblname; 或者
show keys from tblname;

### 删除索引
ALTER TABLE `table_name` DROP INDEX `index_name`
删除主键索引也可以： ALTER TABLE table_name DROP PRIMARY KEY

## 存储过程

### 创建存储过程
1. 一般自定义的变量使用'@'符号开头，如 set @name='test'。
2. 存储过程中的分号是必须的，begin、end等不需要分号。

```
CREATE PROCEDURE proc1(IN name1 CHAR(20))   /*IN 表示输入参数，char限定类型*/
BEGIN 
	IF name1 is null or name1='' THEN 
		SELECT * FROM users; 
	ELSE 
		SELECT * FROM users WHERE username LIKE CONCAT('%',name1,'%');
	END IF; 
END 
```

### 调用存储过程
1. 基本语法：call proc1('qq')，注意：存储过程名称后面必须加括号，哪怕该存储过程没有参数传递


### 删除存储过程
1. 基本语法：drop procedure proc1

2. 注意事项: 不能在一个存储过程中删除另一个存储过程，只能调用另一个存储过程

### 其他常用命令
1. show procedure status  显示数据库中所有存储的存储过程基本信息，包括所属数据库，存储过程名称，创建时间等
2. show create procedure proc1  显示某一个MySQL存储过程的详细信息


### 存储过程-遍历所有表的字段
* 功能：将整个数据库中，值为'NULL'的字段全部置为null。
* 思路：
	* 先从MySQL的information_schema数据库中的COLUMNS表中，选取出目标数据库的所有表和字段到tmp表中。
	* 然后使用存储过程，遍历tmp表，进行update操作。

```
# 先创建一个带输入参数的存储过程，用于更新指定的表和字段。
# 注意这里使用参数值作为表/列名的方法，直接使用变量名会以变量名为表/列名。
create PROCEDURE proc(in tab VARCHAR(50), in col VARCHAR(50))
BEGIN

set @t=CONCAT('update ',tab,' set ', col, '=null where ', col, ' ="NULL"');
PREPARE statement1 from @t;
EXECUTE statement1;
DEALLOCATE PREPARE statement1;

commit;
end
```

```
# 然后创建一个存储过程，使用游标进行遍历，调用上一个proc进行处理。
create PROCEDURE pro4()
BEGIN

DECLARE col VARCHAR(40);
DECLARE tab VARCHAR(40);

DECLARE done int DEFAULT FALSE;

DECLARE mycursor CURSOR for (SELECT tabs,  cols FROM `tmp` where tabs='symsynmap');
DECLARE CONTINUE HANDLER for not found SET done = true;

open mycursor;

myloop: LOOP
	FETCH mycursor into tab,col;
	if done THEN
		LEAVE myloop;
	end IF;
	call proc(tab, col);
	COMMIT;
end LOOP myloop;

CLOSE mycursor;
END;
```




## 引擎

```
 数据库中的存储引擎其实是对使用了该引擎的表进行某种设置，数据库中的表设定了什么存储引擎，那么该表在数据存储方式、数据更新方式、数据查询性能以及是否支持索引等方面就会有不同的“效果”。在MySQL数据库中存在着多种引擎（不同版本的MySQL数据库支持的引擎不同），熟悉各种引擎才能在软件开发中应用引擎，从而开发出高性能的软件，MySQL数据库中的引擎有哪些呢？一般来说，MySQL有以下几种引擎：ISAM、MyISAM、HEAP（也称为MEMORY）、CSV、BLACKHOLE、ARCHIVE、PERFORMANCE_SCHEMA、InnoDB、 Berkeley、Merge、Federated和Cluster/NDB等，除此以外我们也可以参照MySQL++ API创建自己的数据库引擎。下面逐次介绍一下各种引擎：
    
ISAM
该引擎在读取数据方面速度很快，而且不占用大量的内存和存储资源；但是ISAM不支持事务处理、不支持外来键、不能够容错、也不支持索引。该引擎在包括MySQL 5.1及其以上版本的数据库中不再支持。
MyISAM
该引擎基于ISAM数据库引擎，除了提供ISAM里所没有的索引和字段管理等大量功能，MyISAM还使用一种表格锁定的机制来优化多个并发的读写操作，但是需要经常运行OPTIMIZE TABLE命令，来恢复被更新机制所浪费的空间，否则碎片也会随之增加，最终影响数据访问性能。MyISAM还有一些有用的扩展，例如用来修复数据库文件的MyISAMChk工具和用来恢复浪费空间的 MyISAMPack工具。MyISAM强调了快速读取操作，主要用于高负载的select，这可能也是MySQL深受Web开发的主要原因：在Web开发中进行的大量数据操作都是读取操作，所以大多数虚拟主机提供商和Internet平台提供商（Internet Presence Provider，IPP）只允许使用MyISAM格式。
MyISAM类型的表支持三种不同的存储结构：静态型、动态型、压缩型。
静态型：指定义的表列的大小是固定（即不含有：xblob、xtext、varchar等长度可变的数据类型），这样MySQL就会自动使用静态MyISAM格式。使用静态格式的表的性能比较高，因为在维护和访问以预定格式存储数据时需要的开销很低；但这种高性能是以空间为代价换来的，因为在定义的时候是固定的，所以不管列中的值有多大，都会以最大值为准，占据了整个空间。
动态型：如果列（即使只有一列）定义为动态的（xblob, xtext, varchar等数据类型），这时MyISAM就自动使用动态型，虽然动态型的表占用了比静态型表较少的空间，但带来了性能的降低，因为如果某个字段的内容发生改变则其位置很可能需要移动，这样就会导致碎片的产生，随着数据变化的增多，碎片也随之增加，数据访问性能会随之降低。
对于因碎片增加而降低数据访问性这个问题，有两种解决办法：
a、尽可能使用静态数据类型；
b、经常使用optimize table table_name语句整理表的碎片，恢复由于表数据的更新和删除导致的空间丢失。如果存储引擎不支持 optimize table table_name则可以转储并        重新加载数据，这样也可以减少碎片；
压缩型：如果在数据库中创建在整个生命周期内只读的表，则应该使用MyISAM的压缩型表来减少空间的占用。
HEAP（也称为MEMORY）
该存储引擎通过在内存中创建临时表来存储数据。每个基于该存储引擎的表实际对应一个磁盘文件，该文件的文件名和表名是相同的，类型为.frm。该磁盘文件只存储表的结构，而其数据存储在内存中，所以使用该种引擎的表拥有极高的插入、更新和查询效率。这种存储引擎默认使用哈希（HASH）索引，其速度比使用B-+Tree型要快，但也可以使用B树型索引。由于这种存储引擎所存储的数据保存在内存中，所以其保存的数据具有不稳定性，比如如果mysqld进程发生异常、重启或计算机关机等等都会造成这些数据的消失，所以这种存储引擎中的表的生命周期很短，一般只使用一次。
CSV（Comma-Separated Values逗号分隔值）
使用该引擎的MySQL数据库表会在MySQL安装目录data文件夹中的和该表所在数据库名相同的目录中生成一个.CSV文件（所以，它可以将CSV类型的文件当做表进行处理），这种文件是一种普通文本文件，每个数据行占用一个文本行。该种类型的存储引擎不支持索引，即使用该种类型的表没有主键列；另外也不允许表中的字段为null。
BLACKHOLE（黑洞引擎）
该存储引擎支持事务，而且支持mvcc的行级锁，写入这种引擎表中的任何数据都会消失，主要用于做日志记录或同步归档的中继存储，这个存储引擎除非有特别目的，否则不适合使用。详见博客《BlackHole 存储引擎》
ARCHIVE
该存储引擎非常适合存储大量独立的、作为历史记录的数据。区别于InnoDB和MyISAM这两种引擎，ARCHIVE提供了压缩功能，拥有高效的插入速度，但是这种引擎不支持索引，所以查询性能较差一些。
PERFORMANCE_SCHEMA
该引擎主要用于收集数据库服务器性能参数。这种引擎提供以下功能：提供进程等待的详细信息，包括锁、互斥变量、文件信息；保存历史的事件汇总信息，为提供MySQL服务器性能做出详细的判断；对于新增和删除监控事件点都非常容易，并可以随意改变mysql服务器的监控周期，例如（CYCLE、MICROSECOND）。
InnoDB
该存储引擎为MySQL表提供了ACID事务支持、系统崩溃修复能力和多版本并发控制（即MVCC Multi-Version Concurrency Control）的行级锁;该引擎支持自增长列（auto_increment）,自增长列的值不能为空，如果在使用的时候为空则自动从现有值开始增值，如果有但是比现在的还大，则直接保存这个值; 该引擎存储引擎支持外键（foreign key） ,外键所在的表称为子表而所依赖的表称为父表。该引擎在5.5后的MySQL数据库中为默认存储引擎。
Berkeley（BDB）
该存储引擎支持COMMIT和ROLLBACK等其他事务特性。该引擎在包括MySQL 5.1及其以上版本的数据库中不再支持。
Merge
该引擎将一定数量的MyISAM表联合而成一个整体。参见博客《MySQL Merge存储引擎》
Federated
该存储引擎可以不同的Mysql服务器联合起来，逻辑上组成一个完整的数据库。这种存储引擎非常适合数据库分布式应用。
Cluster/NDB
该存储引擎用于多台数据机器联合提供服务以提高整体性能和安全性。适合数据量大、安全和性能要求高的场景。
以上是对MySQL数据库中存储引擎的总结，只是重点总结了一下各种不同存储引擎的特点，不对的地方还望各位指正，不胜感激。
```


* 数据库的表的类型？InnoDB和MyISAM是许多人在使用MySQL时最常用的两个表类型，这两个表类型各有优劣，视具体应用而定。基本的差别为：
	* MyISAM类型不支持事务处理等高级处理，而InnoDB类型支持。
	* MyISAM类型的表强调的是性能，其执行数度比InnoDB类型更快，但是不提供事务支持，而InnoDB提供事务支持以及外部键等高级数据库功能。
	* 两种类型最主要的差别就是InnoDB 支持事务处理、外键和行级锁。


