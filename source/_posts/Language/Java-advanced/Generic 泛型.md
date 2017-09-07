---
title: Generic 泛型
comments: true
date: 2017-09-01 13:58:10
updated: 2017-09-01 13:58:10
categories: Language
tags:
- Java
---

**说明：**Java Generic 泛型。
<!-- more -->


参考文章：


## Generic 泛型

### 泛型说明
* 泛型Generic从jdk1.5出现。泛型有泛型方法、泛型类（泛型接口）。泛型常用于框架、库的设计中。
* 泛型本质上是参数化类型，类型是任意的，是经过传递的类型参数。但是不能传递基本类型，只能传递引用类型，但是基本数据会自动装箱为包装类型。
* 参数泛型的种类
	* 自定义泛型 `<T>` 用字母表示如T，V，T1等表示自定义泛型。
	* 通配符泛型 `<?>` 用?表示通配符泛型。
	* ？和T都表示不确定的类型，但是？不能用于之后的代码，常用于类型限定；而T可以在函数中使用和实例化等操作。
* 类型限定
	* ? extends E ： 接收E类型或者E的子类型。
		* 使用extends向下泛型，可以`<? extends E>`或者`<T extends E>`，因为向下泛型可以对应到一个E的一个子类，所以编译可以使用E的类型进行编译。
	* ? super E ： 接收E类型或者E的父类型。
		* 使用supe进行向上泛型(向父类泛型)，那么前面只能用通配符?，不能用一个具体泛型T，因为一个类的父类可能直接只有Object类，也因此super的T只能用Object类进行编译。


### 泛型示例
* jdk8源码中List等都是使用了泛型。
	* 泛型接口、泛型类：声明规则是在类名后面添加一个类型参数，如 class Apple<T>，类内部就可以直接用T作为类型。
	* 泛型方法：返回值之前写泛型参数，参数列表里面使用泛型
	* 
		* 泛型方法举例 `public static  <T extends Number, S> T[] toArray(T[] a)`

```
public interface List<E> extends Collection<E> {  // 泛型接口
    int size();
    Iterator<E> iterator();

    <T> T[] toArray(T[] a);  // 接口内的public abstract toArray方法，加入新的泛型，由实现类实现

    default void sort(Comparator<? super E> c) {  // jdk8中接口的default方法，可以直接使用E，不需要引入新的泛型
        Object[] a = this.toArray();
        Arrays.sort(a, (Comparator) c);
        ListIterator<E> i = this.listIterator();
        for (Object e : a) {
            i.next();
            i.set((E) e);
        }
    }
}


// 泛型类
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable{ 
    public int size() { return size; }
    public Iterator<E> iterator() { return new Itr(); }

    public <T> T[] toArray(T[] a) {    // 典型的泛型接口的写法：返回值之前写泛型参数，参数列表里面使用泛型
        if (a.length < size)
            // Make a new array of a's runtime type, but my contents:
            return (T[]) Arrays.copyOf(elementData, size, a.getClass());
        System.arraycopy(elementData, 0, a, 0, size);
        if (a.length > size)
            a[size] = null;
        return a;
    }

}


Class Main{
    public static <T> void test(T t) {
        List<String> list1 = new ArrayList<>();  // 后面的参数默认是前面，否则编译错误
        List<String> list2 = new ArrayList<String>();
        List<T> tList = new ArrayList<>();
        tList.add(t); 
    } 
}
```


