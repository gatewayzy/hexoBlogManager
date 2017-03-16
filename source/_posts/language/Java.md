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
* Java是强类型语言。
* 属于编译型语言。


 
## 安装
---
jre用于运行Java，jdk用于开发调试Java，注意jdk中也包含一个jre。
### Windows安装jdk
---
* 下载jdk或安装jdk，添加JAVA_HOME类似于`D:\Program Files\Java\jdk1.8.0_65x64`；
在path中添加`%JAVA_HOME%\bin； %JAVA_HOME%\jre\bin； 
%JAVA_HOME%\lib`。
* 运行`java -version`查看安装状态。
* 运行`javac Hello.java`编译生成了Hello.class，运行`java Hello`运行程序。

### Ubuntu安装jdk
---
* 下载解压jdk，添加path的操作为：将下面内容添加到/etc/profile，并运行`source /etc/profile`

```
export JAVA_HOME=/opt/jdk
export JRE_HOME=${JAVA_HOME}/jre  
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib  
export M2_HOME=/opt/maven
export PATH=$PATH:$M2_HOME/bin:$JAVA_HOME/bin
```

## 语法
---
* 略。

## Java模板引擎
---
* Velocity、FreeMarker、Beetl、Httl、Smarty4j等等开源模板引擎，用于替代解决JSP加EL、JSTL等的复杂性。

##  问题与解决
---

### 子类与父类的静态代码块的加载
---
* 父类的静态代码块 static{}
* 子类的静态代码块 static{}
* 父类的非静态代码块 {}
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










