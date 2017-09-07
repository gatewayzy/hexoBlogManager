---
title: Java 集合与数据结构
comments: true
date: 2017-09-01 13:58:10
updated: 2017-09-01 13:58:10
categories: Language
tags:
- Java
---

**说明：**Java 集合与数据结构。
<!-- more -->


参考文章：


## Java 集合与数据结构
* Java 2 之前，Java 为对象组的存储和操作提供了特别的类比如 字典Dictionary，向量Vector，堆栈Stack和属性Properties。缺乏明确主题和分类。
* 后来提出了集合框架。集合框架中由接口、实现类、算法接口组成，构成完整的分类体系。

![image](http://zcy.ckcest.cn/cdn/zy/20170907-Image-1.jpg)


### 集合接口
|接口名称 |接口描述|
---|---
Collection |这让你可以使用对象组；它是集合层次阶段的顶端
List |它继承了 Collection 并且 List 的一个实例存储了元素的一个有序集合
Set |它继承了 Collection 来处理集，它必须含有特殊的元素
SortedSet |它继承了 Set 来处理 排序的 set
Map |它将独特的键和值匹配
Map Entry |这描述了映射中的一个元素(一个键值对)。它是 Map 的一个内部类。
SortedMap |它继承了 Map 因此键按升序保持
Enumeration->Iterator |它是旧有的接口并定义了你可以在对象的集合中列举(一次获得一个)元素的方法。这个旧有的接口被迭代器Iterator取代了。

### 集合实现类
|实现类的名称 |类的描述|
---|---
AbstractCollection  |实现大部分的 Collection 接口  
AbstractList  |继承 AbstractCollection 并且实现大部分 List 接口
AbstractSequentialList  |通过一个使用有序的而不是随机访问它的元素的集合继承 AbstractList
LinkedList  |通过继承 AbstractSequentialList 实现一个链表
ArrayList   |通过继承 AbstractList 实现一个动态数组
AbstractSet  |继承 AbstractCollection 并实现大部分的 Set 接口
HashSet  |用一个哈希表继承 AbstractSet
LinkedHashSet  |继承 HashSet 来允许插入顺序迭代
TreeSet  |实现在树中存储的一个集。继承 AbstractSet
AbstractMap  |实现大部分的 Map 接口
HashMap  |用一个哈希表继承 AbstractMap
TreeMap  |用一棵树继承 AbstractMap
WeakHashMap  |用一个使用弱键的哈希表来继承 AbstractMap
LinkedHashMap  |继承 AbstractMap 来允许插入顺序迭代
IdentityHashMap  |继承 AbstractMap 类并且当比较文档时平等使用参考

### 旧有的集合类
|类的名称 |类的描述|
---|---
 Vector |这实现一个动态数组。它和 ArrayList 类似，但也有一些不同。
 Stack |Stack 是 Vector 的实现标准的后进先出栈的子类
 Dictionary |是一个抽象的代表一个键值对存储库的类并且操作起来非常像 Map
 Hashtable |Hashtable 是初始的 java.util 的一部分并且是 Dictionary 的具体实现
 Properties |Properties 是 Hashtable 的一个子类。它被用来保持键是一个字符串并且值也是一个字符串的值的列表
 BitSet |一个 BitSet 类创建一个特殊的保持 bit 数值的数组类型。这个数组的大小能根据需要增长

 ### 集合的算法
|算法 |类的描述|
---|---
Iterable 接口    |一个Collection可以用迭代器，是Collection接口的父类。
Iterator 接口   |一个Collection的迭代器，用于获得或者删除元素。其子接口类ListIterator接口，允许双向遍历和修改。
Comparator 接口 |比较器，compare、equals、reversed等方法 jdk1.8
Comparable 接口 |元素可比较，需要实现compareTo方法

### 补充说明
* 很多抽象类，如AbstractCollection, AbstractSet, AbstractList, AbstractSequentialList 和 AbstractMap 类提供了核心集合接口的实现，减少代码重复。
* Collections类和Arrays类实现了大量static的方法，提供常用的集合操作。
* 还有其他一些接口和实现类并没有提到。
* 很多接口和类之间不是通过继承实现关系传递，而是通过程序组合起来的。比如Comparator和Comparable必然是联系的，但是不是继承关系。