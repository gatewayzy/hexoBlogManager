---
title: Java开发面试题
comments: true
date: 2017-09-01 13:58:10
updated: 2017-09-01 13:58:10
categories: Algorithm
tags:
- Java开发
---

**说明：**Java开发面试题。
<!-- more -->


参考文章：

## 常规面试题

1. HashMap的实现原理。
2. 什么是AOP？AOP的使用场景？AOP的实现原理？
3. 垃圾回收算法有哪些？
4. class装载步骤？
5. http的请求方法？
6. 常用的http响应头？
7. 简述java的内存模型
8. springboot的优势和劣势？
9. springcloud的优势和劣势以及springcloud的组成？
10. collection和collections的区别？

## 经典面试题

1. jvm，jre以及jdk三者之间的关系？
2. synchronized是怎么实现锁的？
3. spring是怎么实现单例的？
4. mybaits中#和$两者的区别？
5. mysql的性能优化，你会考虑哪些？
6. tomcat的优化，你知道哪些？ 
	1. 关掉对web.xml的监视，把jsp提前编辑成Servlet。有富余物理内存的情况，加大tomcat使用的jvm的内存
	2. 服务器资源，服务器所能提供CPU、内存、硬盘的性能对处理能力有决定性影响。
　　		1. 对于高并发情况下会有大量的运算，那么CPU的速度会直接影响到处理速度。
　　		2. 内存在大量数据处理的情况下，将会有较大的内存容量需求，可以用-Xmx -Xms -XX:MaxPermSize等参数对内存不同功能块进行划分。我们之前就遇到过内存分配不足，导致虚拟机一直处于full GC，从而导致处理能力严重下降。
　　		3. 硬盘主要问题就是读写性能，当大量文件进行读写时，磁盘极容易成为性能瓶颈。最好的办法还是利用下面提到的缓存。
	3. 利用缓存和压缩
		1. 对于静态页面最好是能够缓存起来，这样就不必每次从磁盘上读。这里我们采用了Nginx作为缓存服务器，将图片、css、js文件都进行了缓存，有效的减少了后端tomcat的访问。
		2. 为了能加快网络传输速度，开启gzip压缩也是必不可少的。但考虑到tomcat已经需要处理很多东西了，所以把这个压缩的工作就交给前端的Nginx来完成。
		3. 除了文本可以用gzip压缩，其实很多图片也可以用图像处理工具预先进行压缩，找到一个平衡点可以让画质损失很小而文件可以减小很多。曾经我就见过一个图片从300多kb压缩到几十kb，自己几乎看不出来区别。
	4. 采用集群。单个服务器性能总是有限的，最好的办法自然是实现横向扩展，那么组建tomcat集群是有效提升性能的手段。我们还是采用了Nginx来作为请求分流的服务器，后端多个tomcat共享session来协同工作。可以利用nginx+tomcat+memcached组建web服务器负载均衡。
	5. 优化tomcat参数。这里以tomcat7的参数配置为例，需要修改conf/server.xml文件，主要是优化连接配置，关闭客户端dns查询。
```
<Connector port="8080"   
protocol="org.apache.coyote.http11.Http11NioProtocol"  
connectionTimeout="20000"  
redirectPort="8443"   
maxThreads="500"   
minSpareThreads="20"  
acceptCount="100" 
disableUploadTimeout="true" 
enableLookups="false"   
URIEncoding="UTF-8" />
```
 
7. 缓存的雪崩以及穿透的理解？
	1. 什么是缓存穿透：一般的缓存系统，都是按照key去缓存查询，如果不存在对应的value，就应该去后端系统查找。如果key对应的value是一直不存在，并且对该key并发请求量很大，就会对后端系统造成很大的压力。这就叫做缓存穿透。
	2. 避免缓存穿透：
		1. 对查询结果为空的情况也进行缓存，缓存时间设置短一点，或者该key对应的数据insert了之后清理缓存。
		2. 对一定不存在的key进行过滤。可以把所有的可能存在的key放到一个大的Bitmap中，查询时通过该bitmap过滤。（第2种应该用的不多吧）
	3. 什么是缓存雪崩？ 当缓存服务器重启或者大量缓存集中在某一个时间段失效，在失效的时候，也会给后端系统(如DB)带来很大压力。
	4. 如何避免缓存雪崩？ 
		1. 在缓存失效后，通过加锁或者队列来控制读数据库写缓存的线程数量。比如对某个key只允许一个线程查询数据和写缓存，其他线程等待。
		2. 不同的key，设置不同的过期时间，让缓存失效的时间点尽量均匀。
		3. 做二级缓存，A1为原始缓存，A2为拷贝缓存，A1失效时，可以访问A2，A1缓存失效时间设置为短期，A2设置为长期
8. HashMap的key可以重复吗？
9. synchronized和lock的区别？
10. 开发一个大型网站你会考虑哪些问题?

##美团面试题

- 最近做的比较熟悉的项目是哪个，画一下项目技术架构图
- JVM老年代和新生代的比例？
- YGC和FGC发生的具体场景？
- jstack，jmap，jutil分别的意义？如何线上排查JVM的相关问题？
	- jps，java process status，进程状态
	- jstack，java stack，堆栈信息
	- jmap，java memory map，堆内存使用状态，常和jhat java heap analysis toolkit一起使用
	- jstat jvm统计监测
	- hprof Heap/CPU Profiling Tool，CPU使用率和堆使用状态
- 线程池的构造类的方法的几个参数的具体意义？

```
ThreadPoolExecutor executor = new ThreadPoolExecutor(
	corePoolSize,// 核心线程数  
	maximumPoolSize, // 最大线程数  
	keepAliveTime, // 闲置线程存活时间  
	TimeUnit.MILLISECONDS,// 时间单位  
	new LinkedBlockingDeque<Runnable>(),// 线程队列  
	Executors.defaultThreadFactory(),// 线程工厂  
	new AbortPolicy()// 队列已满,而且当前线程数已经超过最大线程数时的异常处理策略  
); 
// 比如设置如下
new ThreadPoolExecutor(
10, 100, 60, TimeUnit.SECONDS, //限制最大线程，如果是CPU密集型的任务，核心线程就用核数，最大线程就用核数的N倍
new ArrayBlockingQueue<Runnable>(1000),  //固定大小，防止内存堆积过多
new NamedThreadFactory("third_retrieval"),  //自己的ThreadFactory
new ThreadPoolExecutor.DiscardPolicy())        //当任务对接的时候直接丢弃
```

- 单机上一个线程池正在处理服务如果忽然断电怎么办（正在处理和阻塞队列里的请求怎么处理）？
- 使用无界阻塞队列会出现什么问题？
- 接口如何处理重复请求？
- 具体的处理方案的是什么？
- 如何保证共享变量修改时的原子性？

## 滴滴面试题

- 自我介绍
- 技术特点
- 兴趣是什么，优势是什么
- Dubbo的底层原理，Zookeeper是什么
- concurrentMap的机制；TreeMap；Volatil关键字
- 快速排序；广度优先搜索（队列实现）

## 京东面试题

- Dubbo超时重试；Dubbo超时时间设置
- 如何保障请求执行顺序
- 分布式事务与分布式锁（扣款不要出现负数）
- 分布式session设置
- Zookeeper有那些作用
- 数据库垂直和水平拆分
- MyBatis如何分页；如何设置缓存；MySQL分页
- 熟悉IO么？与NIO的区别，阻塞与非阻塞的区别
- 分布式session一致性
- 分布式接口的幂等性设计（不能重复扣款）