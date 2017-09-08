---
title: Java 多线程
comments: true
date: 2017-09-01 13:58:10
updated: 2017-09-01 13:58:10
categories: Language
tags:
- Java
- 多线程
---

**说明：**Java 多线程：notify、Executor框架、Fork/Join 框架、Lock/Condition。
<!-- more -->


参考文章：《Java 并发编程实战》



## 多线程基础：Thread接口 Runnable接口
---
* run()只是在当前线程执行程序，并不会启动新的线程，start()才能启动新的线程。
* 继承Thread类，重写run方法：class MyThread extends Thread{ public void run(){ }} 然后new MyThread().start(); 启动线程。
* 实现Runnable接口，实现run方法：class MyRun implements Runnable{ public void run(){ }}，然后MyRun myrun = new MyRun();new Thread(myrun).start(); 如果使用多个new Thread(myrun).start() 访问的是同一个my对象，需要考虑并发安全问题。
* ThreadFactory接口，属于工厂模式、创建模式。需要实现其Thread newThread(Runnable r)方法。
* thread1.setDaemon(true); // 将该线程设置为守护线程，一旦主线程运行结束，daemon线程自动退出。守护线程不要涉及业务、资源，可以用于其他任务。




## sychronized 关键字 基本同步机制
---
* synchronized 关键字，使用 wait notify等待和同步。
* 互斥同步机制应该尽量轻量级，锁的范围越小越好：
    * 能不用锁就不用锁，比如用并发的数据结构实现业务
    * 能用对象级别锁就不要用类级别锁
    * 锁的范围越小越好，比如同步方法最好缩小到同步代码块

## Lock/Condition 基本同步机制
---
* Lock接口，常见的实现类有 ReentrantLock、ReentrantReadWriteLock.ReadLock/WriteLock。




## 高级同步机制
---
### Semaphore 信号量同步机制
* Semaphore信号量进行acquire() release()。


### CountDownLatch 同步辅助类
* CountDownLatch 一种同步辅助类jdk1.7，让当前线程await()其他操作。初始化数值作为计数器，监听await()事件（会在cd结束的时候收到异常通知），继续运行。

### CyclicBarrier 同步辅助类
* CyclicBarrier 一种同步辅助类，允许多个线程在一个集合点相互等待。

### Phaser 同步辅助类
* Phaser 一种同步辅助类，适合具有多个步骤的并发任务。在每个阶段结束时，同步等待其他线程，因此，只有所有线程都执行完各自的第一个步骤，才会开始第二个步骤的任务。arrive()。

### Exchanger 同步辅助类
* Exchanger 一种同步辅助类，为两个线程提供数据交换点。




## Executor Framework 执行器框架  Callable接口
---
* Executor 执行器框架将线程创建和执行分离。jdk1.5开始增加Executor框架。
* Executor 框架基于Executor接口和ExecutorService接口，以及这两个接口的实现类ThreadPoolExecutor。
* Executor有一个内部线程池，可以将任务传递到线程池并执行。传递的任务有下面两种。尝试调用线程池中的线程执行任务，而不是每次都新建和销毁线程，线程池提高了性能和效率。
    * Runnable接口实现的任务，不会返回任务执行结果。
    * Callable接口实现的任务，将返回执行结果。

### Callable接口
---
* Callable接口属于Executor框架，该接口类似于Runnable接口，但是有两点增强：
    * 接口需要实现方法call()，可以返回结果。
    * 发送一个Callable对象给Executor时，会获得一个实现了Future接口的对象，该对象可以用于控制Callable对象的状态和结果。





## Fork/Join Framework 分解/合并框架
---
* jdk7 增加了Fork/Join框架。ForkJoin Framework与Executor Framework的主要区别在于Work-Stealing Algorithm 工作窃取算法。
* ForkJoin框架核心类是：
    * ForkJoinPool： 实现了Executor接口和Work-Stealing Algorithm。用于管理工作者线程，提供任务的状态和执行信息。
    * ForkJoinTask：作为在ForkJoinPool中执行的基类。
* 两种任务：RecursiveAction 适用于没有返回结果的任务，RecursiveTask 适用于有返回结果的任务。






## 并发集合
---
* 并发类的集合也分为阻塞式和非阻塞式：
    * 阻塞式集合 Blocking Collection 集合已满或者为空时，进行添加或者删除操作时，会阻塞当前线程，直到方法可以被成功执行。
    * 非阻塞式集合 Non-Blocking Collection 如果添加或删除方法不能被立即执行，会返回null或异常，方法所属线程不会阻塞。
* Java集合框架中的并发集合：
    * 非阻塞式列表 ConcurrentLinkedDeque 类
    * 阻塞式列表 LinkedBlockingDeque 类
    * 原子变量对应的实现类 AtomicLong和AtomicIntegerArray。可以自己继承AtomicLong实现原子类，利用父类的CAS原子性操作public final boolean compareAndSet(int expect, int update) 等方法。
    * ……



