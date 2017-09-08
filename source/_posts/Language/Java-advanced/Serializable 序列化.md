---
title: Serializable 序列化
comments: true
date: 2017-09-01 13:58:10
updated: 2017-09-01 13:58:10
categories: Language
tags:
- Java
---

**说明：**Java Serializable 序列化：java.io.Serializable接口。
<!-- more -->


参考文章：

## 序列化说明
* 当数据中的java对象通过网络传输、或者存储到文件，另一方进行数据解析的时候需要解析数据的格式，为此java提供了对象序列化机制。
* 序列化：在一个序列化的对象被写进文件之后，它能在文件中被读出并被反序列化为类型信息和表示对象的字节，并且它的数据可以被用来重新创建在内存中的对象。

## 序列化用法
* 可序列化的对象定义要求：
    * 类必须实现 java.io.Serializable 接口。
    * 类中所有的字段必须被序列化。如果一个字段没有被序列化，它必须被标记为瞬态的transient。(序列化时，transient变量的值不会被存储，会使用相应的0、null等)
* 序列化使用java.io.ObjectOutputStream。ObjectOutputStream获取输出流，然后ObjectOutputStream使用writeObject(object)方法，或其他write方法。
* 反序列化使用java.io.ObjectInputStream。ObjectInputStream获取输入流，然后ObjectInputStream使用readObject()方法，或其他read方法。

## serialVersionUID的作用
* Java的序列化机制是通过在运行时判断类的serialVersionUID来验证版本一致性的。比如A端增加或删除了类的一个成员变量，B端的类没有相应修改，二者传输时就会版本不一致，所以两端的类中都有序列化版本号，用于验证版本一致。
* 在进行反序列化时，JVM会把传来的字节流中的serialVersionUID与本地相应类的serialVersionUID进行比较，如果相同就认为是一致的，可以进行反序列化，否则就会出现序列化版本不一致的异常(InvalidCastException)。

### serialVersionUID的显示声明
* 如果实现类没有显示声明`long serialVersionUID`，Java序列化机制会根据编译的class自动生成一个serialVersionUID作序列化版本比较用(它通过类名，方法名等诸多因素经过计算而得，不同class会有唯一的默认序列号)。这种情况下，如果class文件没有发生变化(增加空格,换行,增加注释,等等都算变化)，serialVersionUID不会变化。这也就有一个问题，如果一端的类变化，就会导致两端默认版本号不同，无法兼容工作。
* serialVersionUID有两种显示的声明方式如下。显示声明版本号的好处就是可以进行版本兼容。当旧版实现类设置了版本号，新版类只要版本号与旧版号一致，二者就可以相互序列化和反序列化，允许新版兼容旧版。
    * 默认1L，如：`private static final long serialVersionUID = 1L;`如果可兼容，可以保留旧版本号，如果不兼容，或者想让它不兼容，就手工递增版本号。
    * 根据类名、接口名、成员方法及属性等生成64位的哈希字段，如：`private static final long serialVersionUID = 123L;`可以在每次修改类后就重新生成新的版本号，不向下兼容。

### IDE生成serialVersionUID的方法
* Intellij IDEA默认关闭了继承了Serializable接口的类生成serialVersionUID的警告。如果需要ide提示生成serialVersionUID，那么需要做以下设置：
    * setting->Inspections->Serialization issues，将serialzable class without "serialVersionUID"打上勾；
    * 将光标放到类名上，按atl＋enter键，就会提示生成serialVersionUID了
* Eclipse默认开启Serializable实现类的serialVersionUID的警告。


## 序列化与反序列化Demo

```
package javaAdvanced;

import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;

public class _01Serializable {

    static class Employee implements java.io.Serializable {

        private static final long serialVersionUID = -602932866640093016L;
        public String name;
        public String address;
        public transient int SSN;
        public int number;

        public void print() {
            System.out.println("name：" + name + "\naddress：" + address + "\nSSN：" + SSN + "\nnumber：" + number);
        }
    }

    public static void main(String[] args) throws Exception {
        serialization();
        ser();
    }

    // 序列化，使用java.io.ObjectOutputStream获取输出流，然后使用writeObject()方法序列化对象
    static void serialization() throws Exception {
        System.out.println("====== 开始序列化");
        Employee employee = new Employee();
        employee.name = "name-Ali";
        employee.address = "address-Where";
        employee.SSN = 11122333;
        employee.number = 101;

        FileOutputStream fileOut = new FileOutputStream("/employee.ser");
        ObjectOutputStream out = new ObjectOutputStream(fileOut);
        out.writeObject(employee);

        out.close();
        fileOut.close();
    }

    // 反序列化，使用java.io.ObjectInputStream获取输入流，然后使用readObject()方法反序列化对象
    static void ser() throws Exception {
        System.out.println("====== 开始反序列化");
        Employee employee = null;

        FileInputStream fileIn = new FileInputStream("/employee.ser");
        ObjectInputStream in = new ObjectInputStream(fileIn);
        employee = (Employee) in.readObject();

        in.close();
        fileIn.close();
        employee.print();
    }
}
```