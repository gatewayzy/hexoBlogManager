---
title: Throwable 类及其继承体系
comments: true
date: 2017-09-01 13:58:10
updated: 2017-09-01 13:58:10
categories: Language
tags:
- Java
- Throwable
---

**说明：**Java Throwable 类及其继承体系。
<!-- more -->


参考文章：[Java提高篇—Java 异常处理](http://www.cnblogs.com/Qian123/p/5715402.html)


## Throwable 类及其继承体系
java.lang.Throwable 类

* Throwable类有子类：Error类*（往往很严重的错误）*和Exception类*（严重或者不严重的错误）*。
	* Error类有子类：VirtualMachineError*（jvm运行错误）*、AWTError*（Abstract Window Toolkit抽象窗口工具集错误，属于javaGUI）*
	* Exception类有子类：IOException、RuntimeException*（运行时异常，和Error类共同称为Unchecked Exception）*

![image](http://zcy.ckcest.cn/cdn/zy/20170901-Image-3.jpg)
![image](http://zcy.ckcest.cn/cdn/zy/20170901-Image-4.jpg)

    

### Error类和Exception类
* Error类：JVM错误子类*（含OutOfMemoryError、StackOverflowError等）*或者AWT错误子类，往往直接终止线程运行。
    * Error类对象由 Java 虚拟机生成并抛出，大多数错误与代码编写者所执行的操作无关。例如，Java虚拟机运行错误（Virtual MachineError），当JVM不再有继续执行操作所需的内存资源时，将出现 OutOfMemoryError。这些异常发生时，Java虚拟机（JVM）一般会选择线程终止；还有发生在虚拟机试图执行应用时，如类定义错误（NoClassDefFoundError）、链接错误（LinkageError）。这些错误是不可查的，因为它们在应用程序的控制和处理能力之 外，而且绝大多数是程序运行时不允许出现的状况。对于设计合理的应用程序来说，即使确实发生了错误，本质上也不应该试图去处理它所引起的异常状况。

* Exception类：
    * 在Exception分支中有一个重要的子类RuntimeException（运行时异常），该类型的异常自动为你所编写的程序定义ArrayIndexOutOfBoundsException（数组下标越界）、NullPointerException（空指针异常）、ArithmeticException（算术异常）、MissingResourceException（丢失资源）、ClassNotFoundException（找不到类）等异常，这些异常是不检查异常，程序中可以选择捕获处理，也可以不处理。这些异常一般是由程序逻辑错误引起的，程序应该从逻辑角度尽可能避免这类异常的发生。
    * 而RuntimeException之外的异常我们统称为非运行时异常，类型上属于Exception类及其子类，从程序语法角度讲是必须进行处理的异常，如果不处理，程序就不能编译通过。如IOException、SQLException等以及用户自定义的Exception异常。
    
* Error和Exception的区别：
    * Error通常是灾难性的致命的错误，是程序无法控制和处理的，当出现这些异常时，Java虚拟机一般会选择终止线程；
    * Exception通常情况下是可以被程序处理的，并且在程序中应该尽可能的去处理这些异常。

### Checked Exception 与 Unchecked Exception
*  Unchecked Exception 包括RuntimeException及其子类和Error类，往往是严重的无法挽回的错误，不需要捕获异常。
*  Checked Exception 除了RuntimeException及其子类之外的Exception都是检查异常，需要使用tray-catch处理异常或者throws抛出异常，否则无法通过编译。

### try-catch-finally throw throws
* Checked Exception必须使用tray-catch-finally捕获异常，或者throws抛出异常。
* Java异常处理涉及到五个关键字，分别是：try、catch、finally、throw、throws
	* try  -- 用于监听。将要被监听的代码(可能抛出异常的代码)放在try语句块之内，当try语句块内发生异常时，异常就被抛出。
	* catch   -- 用于捕获异常。catch用来捕获try语句块中发生的异常。多个catch会短路：匹配上一个则跳过之后的catch，所以先catch子类异常。
	* finally  -- finally语句块总是会被执行。它主要用于回收在try块里打开的物理资源(如数据库连接、网络连接和磁盘文件)。只有finally块，执行完成之后，才会回来执行try或者catch块中的return或者throw语句，如果finally中使用了return或者throw等终止方法的语句，则就不会跳回执行，直接停止。*也就是说，try或catch中有return时，return语句不会立马返回到调用者，而是记录下返回值待finally代码块执行完毕之后再向调用者返回其值，然后如果在finally中修改了返回值，就会返回修改后的值。显然，在finally中返回或者修改返回值会对程序造成很大的困扰，所以一般禁止在finally中修改return的值*
	* throw   -- 用于抛出异常。 throw new Exception("err"); 必须再使用try-catch捕获或者throws抛出方法异常。
	* throws -- 用在方法签名中，用于声明该方法可能抛出的异常。

### 自定义异常
* 自定义异常一般直接继承Exception，可以重写或添加一些方法。

```
public class _03Exception extends Exception {
    public _03Exception(String mes) {
        super(mes);// super()使用父类构造器
    }

    void printMes() {
        System.out.println(super.getMessage());//调用父类的方法获取message
    }

    public static void main(String[] args) {
        try {
            throw new _03Exception("出错啦");
        } catch (_03Exception e) {
            e.printMes();
        }
    }
}
```