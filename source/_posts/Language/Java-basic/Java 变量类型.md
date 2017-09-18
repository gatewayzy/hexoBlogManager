---
title: Java 变量类型
comments: true
date: 2017-09-01 13:58:10
updated: 2017-09-01 13:58:10
categories: Language
tags:
- Java
---

**说明：**Java 数据类型。
<!-- more -->


参考文章：


## Java 数据类型

* Java 变量类型有：
    * 基本数据类型，primitive类型：注意java没有无符号类型（unsigned int）。
        * boolean，1字节，true或false
        * byte，1字节，-128~127
        * char，2字节，0~65535
        * short，2字节，-32768~32767
        * int，4字节，大约-21亿~21亿，在10^(10-1)级别
        * long，8字节，大约10^(20-1)级别
        * float，4字节，浮点型
        * double，8字节，双精度类型
    * 对象型变量，属于引用类型Reference。
* java 参数传递：不管参数是什么类型，传递的都是参数的拷贝（副本）。
    * 基本变量类型的参数，传递的是值的拷贝。（这一点java与c++相同）
    * 引用类型的参数（Reference），传递的是引用的拷贝：传递reference的副本，reference本质上就是对象的指针，所以引用传递会修改对象。（对于引用类型的参数，c++传递的是真实的地址，不像java这样传递的是“引用/真实的地址”的副本）。引用类型更不可能直接传递对象的拷贝，因为对象可能很大，而且访问堆的速度比较慢。