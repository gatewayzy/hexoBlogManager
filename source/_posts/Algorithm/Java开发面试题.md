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

1.HashMap的实现原理。
2.什么是AOP？AOP的使用场景？AOP的实现原理？
3.垃圾回收算法有哪些？
4.class装载步骤？
5.http的请求方法？
6.常用的http响应头？
7.简述java的内存模型
8.springboot的优势和劣势？
9.springcloud的优势和劣势以及springcloud的组成？
10.collection和collections的区别？

## 经典面试题

1.jvm，jre以及jdk三者之间的关系？
2.synchronized是怎么实现锁的？
3.spring是怎么实现单例的？
4.mybaits中#和$两者的区别？
5.mysql的性能优化，你会考虑哪些？
6.tomcat的优化，你知道哪些？
7.缓存的雪崩以及穿透的理解？
8.HashMap的key可以重复吗？
9.synchronized和lock的区别？
10.开发一个大型网站你会考虑哪些问题?

##美团面试题

最近做的比较熟悉的项目是哪个，画一下项目技术架构图
JVM老年代和新生代的比例？
YGC和FGC发生的具体场景？
jstack，jmap，jutil分别的意义？如何线上排查JVM的相关问题？
线程池的构造类的方法的5个参数的具体意义？
单机上一个线程池正在处理服务如果忽然断电怎么办（正在处理和阻塞队列里的请求怎么处理）？
使用无界阻塞队列会出现什么问题？
接口如何处理重复请求？
具体的处理方案的是什么？
如何保证共享变量修改时的原子性？

## 滴滴面试题

自我介绍
技术特点
兴趣是什么，优势是什么
Dubbo的底层原理，Zookeeper是什么
cincurrentMap的机制；TreeMap；Volatil关键字
快速排序；广度优先搜索（队列实现）

## 京东面试题

Dubbo超时重试；Dubbo超时时间设置
如何保障请求执行顺序
分布式事务与分布式锁（扣款不要出现负数）
分布式session设置
Zookeeper有那些作用
数据库垂直和水平拆分
MyBatis如何分页；如何设置缓存；MySQL分页
熟悉IO么？与NIO的区别，阻塞与非阻塞的区别
分布式session一致性
分布式接口的幂等性设计（不能重复扣款）