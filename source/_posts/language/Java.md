---
title: Java
comments: true
date: 2017-01-10 13:58:10
updated: 2017-01-10 13:58:56
categories: Language
tags:
- Java
- Language
---

**说明：**Java的使用。
<!-- more -->


参考文章：

## 简介
---

* 语言的分类
	* 其实，现在用编译型、解释型来分类编程语言已经有点力不从心了。
	* JAVA的第一道工序是javac编译，当然目标文件是bytecode字节码文件。后续可能有三种处理方式：
		* 运行时，BYTECODE由JVM逐条解释执行，
		* 运行时，部分代码可能由JIT翻译为目标机器指令（以method为翻译单位，还会保存起来，第二次执行就不用翻译了）直接执行；
		* RTSJ。继JAVAC之后执行AOT二次编译，生成静态的目标平台代码（典型的就是IBM WEBSHPERE REAL TIME）。
	* 有的时候，可能是以上三种方式同时在使用，至少1和2是同时使用的，3需要程序员手工指定。所以讨论语言需要更细化一点了：强类型的、弱类型的，静态的、动态的，GC-based的、手工管理内存的，有没有VM...
* 目前看来，Java语言特点为：
	* 强类型（类型在编译期严格确定和检查）
	* 动态的（java语言编译时不需要进行连接工作，所以得到的class文件不能直接在内存中运行。java语言中，类型的加载、连接和初始化操作是在程序运行期间完成的，这种策略虽然会有一定性能开销，但是可以为java应用程序提供高度灵活性。java的动态拓展性就是依赖于运行期动态加载和动态连接实现的。）
	* GC-based
	* 有jvm。

## 语法
### abstract和interface
** 【需要整理】 abstract 类才能有abstract方法，方法可以不实现。abstract类不能用new实例化，一般用于存放一些通用的方法，给一些具体的类实现其方法：abstract方法需要子类重写并实现；普通方法可以重写，也可以不重写而直接调用。*

##  问题与解决
---

### 子类与父类的静态代码块的加载
---
* 父类的静态代码块 static{} // 类加载阶段的初始化要先运行父类的<clinit>类构造器，而<clinit>合并了类变量赋值和static代码块。
* 子类的静态代码块 static{}
* 父类的非静态代码块 {}  // **需要整理**
* 子类的非静态代码块 {}
* 父类的构造器 constructor
* 子类的构造器 constructor


### Static与final
---
* static是静态修饰关键字，可以修饰变量和程序块以及类方法：
	* 当定义一个static的变量的时，jvm会将将其分配在内存堆上，所有程序对它的引用都会指向这一个地址而不会重新分配内存；
	* 当修饰一个程序块的时候(也就是直接将代码写在static{...}中)时候，虚拟机就会优先加载静态块中代码，这主要用于系统初始化，只会执行一次；
	* 当修饰一个类方法时候可以直接通过类来调用而不需要新建对象，Integer.parseInt(i)。
* final可以修饰变量、方法及类：
	* 当定义一个final变量时，jvm会将其分配到常量池中，程序不可改变其值；
	* 当定义一个方法时，改方法在子类中将不能被重写；
	* 当修饰一个类时，该类不能被继承。

###  map与list
* List集合都是有序的，linkedList会保存原有顺序，map集合是无序的。
* List与map都可以放入null，map.put(null,null)。
* List可以放入相同的对象不会覆盖，map放入相同的key时会产生覆盖。


* 如果一个对象的一个方法用了synchronize关键字,那么其他的线程可以访问该对象的其他方法么？
假定该方法是A ()分为两种情况 <1> A是static的 分为2种情况        a.其他方法是synchronized 或没加锁的  那么其他的线程可以访问该对象的其他方法       b.其他方法是static  synchronized   不可以访问<2>A是非static的 那么又分三种情况a.其他方法是synchronize 不可访问b.其他方法没有加synchronize 可以访问c.其他方法是 static synchronized 可以访问
也就是说，如果AB同时是非static synchronized，那么不可以同时访问；如果AB同时是static synchronized，不可以同时访问。其他情况，如A是static syn，B是syn或没加锁，B是可以访问该对象的；再如A是非static synchronized，B是没加锁或者是static syn可以访问A锁定的对象。















