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
----------------------------------------------------------------------------------------------------------

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
* java语言的特性
	* java 从C++改进而来，去掉了多继承、指针等，使用自动垃圾回收等。java前身是Oak。
	* 单继承、封装、多态、抽象 
* jvm使用unicode编码。
* jsp：include会动态加载内部修改，使用`<%include%>`不会监听修改，属于静态加载。
* i++先返回i当前值，再i变成新值，++i先把i变新值，再返回i当前值，所以是修改后的值。




## 关键字
----------------------------------------------------------------------------------------------------------
### abstract和interface
* abstract 类才能有abstract方法，抽象类的方法可以实现也可以是abstract不实现的。
* abstract类不能用new实例化，一般用于存放一些通用的已经实现的方法，提供给具体的类使用：
	* abstract 类的abstract方法需要子类重写并实现；
	* abstract 类的普通方法可以重写，也可以不重写而直接调用。

### static与final
---
* static是静态修饰关键字，可以修饰变量和程序块以及类方法：
	* 当定义一个static的变量的时，jvm会将其分配在内存堆上，所有程序对它的引用都会指向这一个地址而不会重新分配内存；
	* 当修饰一个程序块的时候(也就是直接将代码写在static{...}中)时候，虚拟机就会优先加载静态块中代码，这主要用于系统初始化，只会执行一次；
	* 当修饰一个类方法时候可以直接通过类来调用而不需要新建对象，Integer.parseInt(i)。
* final可以修饰变量、方法及类：
	* 当定义一个final变量时，jvm会将其分配到常量池中，程序不可改变其值；
	* 当定义一个方法时，改方法在子类中将不能被重写；
	* 当修饰一个类时，该类不能被继承。


## 代码块与方法
----------------------------------------------------------------------------------------------------------
### 子类与父类的静态代码块的加载
---
* 父类的静态代码块 static{} // 类加载阶段的初始化要先运行父类的`<clinit>`类构造器，而`<clinit>`合并了类变量赋值和static代码块。
* 子类的静态代码块 static{}
* 父类的非静态代码块 {}  
* 子类的非静态代码块 {}  (子类的clinit执行之前需要先执行父类的clinit方法)
* 父类的构造器 constructor
* 子类的构造器 constructor (初始化一个子类必须先初始化一个父类)



## 方法与接口
----------------------------------------------------------------------------------------------------------
### String StringBuffer StringBuilder
* String是final常量，各种split、+、substring等方法都是新建一个String而不是修改原有String。注意s="a"+"b"这种会在编译期直接转为s="ab"，但是s=s1+"a"，还是会转成sb（jdk1.5以前用线程安全的StringBuffer，jdk1.5开始变成非线程安全的StringBuilder）并append；
* StringBuffer可变，sb.append("123")，而且StringBuffer是线程安全的，自动使用sb的对象锁进行同步。
* StringBuilder可变，sb.append("123")，但是StringBuilder非线程安全，速度更快。
* String（先转成sb再操作）慢于StringBuffer（线程安全，加锁耗时），StringBuffer慢于StringBuilder（非线程安全）。

### Throwable接口的类继承体系

### Map与Collection接口
* 继承extends Collection接口的有List接口、Set接口。List的实现类有ArrayList、LinkedList等，Set接口实现类有TreeSet、HashSet等
	* Collections类(注意有s)里面提供了Collection接口子类的很多static方法。
	* 对于List、Set等接口的实现类，分别继承提供AbstractList，AbstractSet等类提供方法集合（已经实现的和未实现的abstract方法），具体类就拥有了这些方法（已实现的可以重写，未实现的抽象方法必须实现）
* Extends Map 接口的有，implements Map接口的有HashMap、ConcurrentHashMap、TreeMap等。

### Arrays类 Collections类
* 类里面提供很多static方法，为数组array、collection（list、set）等具体类提供通用方法。







## 源码与数据结构
----------------------------------------------------------------------------------------------------------
###  HashSet LinkedHashSet TreeSet



###  ArrayList LinkedList




### HashMap、Hashtable、ConcurrentHashMap、SynchronizedMap

* 参考文章 [极客学院](http://wiki.jikexueyuan.com/project/java-collection/concurrenthashmap.html)
* HashMap
	* 继承Map接口，线程不安全。HashMap可以接受为null的键值(key)和值(value)，而Hashtable则不行。
	* 存储结构：HashMap内部存储使用了一个Node数组(默认大小是16)，而Node类包含一个类型为Node的next的变量，每个Node就相当于一个链表。Node结构（key，value，hash（key的hashCode），Node next）。
	* 自动翻倍扩容：HashMap提供了自动扩容机制，当元素个数达到数组大小loadFactor后会扩大数组的大小，在默认情况下，数组大小为16，loadFactor为0.75，也就是说当HashMap中的元素超过16\0.75=12时，会把数组大小扩展为2*16=32。扩容是因为如果hash bucket太大会降低效率。
	* 整体上，HashMap就是一个数组(16个hash桶)，每个桶用链表存放着对象。put时根据key的hash值放到对应的桶里，如果桶里有key 存在就覆盖；get查找的时候，根据key的hash值找到桶，如果桶里有多个值，就根据key进行equals比较，获取value。
	* 实现安全的HashMap有三种方式：
		* Hashtable：  Map<String, String> hashtable = new Hashtable<>();
		* ConcurrentHashMap： Map<String, String> concurrentHashMap = new ConcurrentHashMap<>();
		* SynchronizedMap： Map<String, String> synchronizedHashMap = Collections.synchronizedMap (new HashMap<String, String>());
* Hashtable (注意t小写)
	* Hashtable的所有方法都是synchronized，所以Hashtable是线程安全的，多个线程可以共享一个Hashtable。Hashtable效率低，没并发需求不需要，有并发需求优先使用ConcurrentHashMap。
	* Hashtable源码： `public synchronized V get(Object key) {...}`
	* 所以当一个线程访问HashTable时，其他线程如果也要访问，会被阻塞住。举个例子，当一个线程使用put时，另一个线程不但不可以使用put方法，连get方法都不可以，效率很低，现在基本不会使用它了。
* ConcurrentHashMap （简写CHM）
	* synchronized(map){  if (map.get(key) == null){return map.put(key, value);} else{return map.get(key);} }
	* 上面这段代码在HashMap和HashTable中是好用的，但在CHM中是有出错的风险的。这是因为CHM在put操作时并没有对整个Map加锁，所以一个线程正在put(k,v)的时候，另一个线程调用get(k)会得到null，这就会造成一个线程put的值会被另一个线程put的值所覆盖。当然，你可以将代码封装到synchronized代码块中，这样虽然线程安全了，但会使你的代码变成了单线程。CHM提供的putIfAbsent(key,value)方法原子性的实现了同样的功能，同时避免了上面的线程竞争的风险。
	* 加锁操作是针对的 hash 值对应的某个 Segment（个人理解一个segment类似于一个HashMap，ConcurrentHashMap默认是16个segment），而不是整个 ConcurrentHashMap。因为 put 操作只是在这个 Segment 中完成，所以并不需要对整个 ConcurrentHashMap 加锁。所以，此时，其他的线程也可以对另外的 Segment 进行 put 操作，因为虽然该 Segment 被锁住了，但其他的 Segment 并没有加锁。同时，读线程并不会因为本线程的加锁而阻塞。正是因为其内部的结构以及机制，所以 ConcurrentHashMap 在并发访问的性能上要比Hashtable和同步包装之后的HashMap的性能提高很多。在理想状态下，ConcurrentHashMap 可以支持 16 个线程执行并发写操作（如果并发级别设置为 16），及任意数量线程的读操作。
	* CHM适用于大量读。当写量大于等于读时，CHM的性能是低于Hashtable和synchronized Map的。这是因为当锁住了整个Map时，读操作要等待对同一部分执行写操作的线程结束。CHM适用于做cache,在程序启动时初始化，之后可以被多个请求线程访问。正如Javadoc说明的那样，CHM是HashTable一个很好的替代，但要记住，CHM的比HashTable的同步性稍弱。
* SynchronizedMap
	* jdk8中源码如下，map= Collections.synchronizedMap(new HashMap<String, String>())返回一个将map封装之后的同步类，该类内部使用synchronized (mutex)方法保证线程安全。（使用synchronized对一个final对象的对象级锁进行互斥实现线程安全）
	* Collections提供了很多这种同步类封装方法，如Collections.synchronizedCollention(Collection c)、 Collections.synchronizedSortedMap(SortedMap sm)、 synchronizedSortedSet(SortedSet ss)等等。

```
Map<String, String> synchronizedHashMap = Collections.synchronizedMap (new HashMap<String, String>());

// jdk8中 Collections.synchronizedMap 源码如下
public static <K,V> Map<K,V> synchronizedMap(Map<K,V> m) { return new SynchronizedMap<>(m);}

private static class SynchronizedMap<K,V> implements Map<K,V>, Serializable {
       private static final long serialVersionUID = 1978198479659022715L;
 
       private final Map<K,V> m;     // Backing Map
       final Object      mutex;        // Object on which to synchronize
 
       SynchronizedMap(Map<K,V> m) {
           this.m = Objects.requireNonNull(m);
           mutex = this;
       }
       SynchronizedMap(Map<K,V> m, Object mutex) {
           this.m = m;
           this.mutex = mutex;
       }
       public int size() {
           synchronized (mutex) {return m.size();}
       }
```













