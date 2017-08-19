---
title: Java JVM
comments: true
date: 2017-01-10 13:58:10
updated: 2017-01-10 13:58:56
categories: Language
tags:
- Java
- Language
---

**说明：**Java virtual machine，主要来自《深入理解Java虚拟机》，有关java内存如何分配，类加载过程等。
<!-- more -->


参考文章：
* 《深入理解Java虚拟机》

## Java JVM
---
### jvm内存分配、垃圾收集策略
---

java内存分配有线程共享内存、线程私有内存。GC主要发生在Java堆。

#### 线程共享区域
* 方法区 Method Area：存储类信息（Class文件）、运行时常量池、静态变量（static）等
	* Class文件包括：类的版本、字段、方法、接口、常量池（称为Class文件常量池，存放类在编译后得到的各种字面量和符号引用）
	* 运行时常量池：Class文件常量池在类加载（大约在解析阶段）时，将符号引用和解析后的直接引用都放到运行时常量池中。
		* Class文件常量池在编译后就固定了，但是运行时常量池经过类加载解析后，还是可变的。如String a="a";a.intern();就是添加到运行时常量池。
	* 常量池中的4类符号引用（类或接口、字段、类方法、接口方法）对应着4种常量类型CONSTANT_Class_info、CONSTANT_Fieldref_info、CONSTANT_Methodref_info、CONSTANT_InterfaceMethodref_info。
	* GC：方法区一般又叫“永久代”，但是实际上是可以GC的，只不过GC起来会很麻烦，主要是回收常量池、卸载类。
* 堆 Heap ： 存放所有对象实例和数组（不绝对），占很大内存，GC发生的主要区域。	

#### 线程私有区域
* 虚拟机栈 VM Stack：不同方法对应不同栈帧，依靠栈运行方法。栈帧里面有局部变量表、动态链接、方法出口等信息
* 本地方法栈： 与虚拟机栈功能相似，有的直接将二者统一为一个栈。本地方法栈运行Native Method，虚拟机栈运行Java方法即字节码服务。
* 程序计数器 PC Register

#### GC 垃圾回收机制
* 对象存活性判断：引用计数法、可达性分析法（由GC Roots（含类的静态属性、栈引用的对象）进行Trace）。
* 引用强弱：强、软、弱、虚引用。GC发生时，强引用对象不可删；软就是有用但是不必要，第一次GC别删，第二次GC可删；弱就是第一次GC就可回收该对象；虚表示无用，用于对象被GC时可以收到一个系统通知。
* GC算法：标记-清除算法、复制收集算法（常用于新生代）、标记-整理算法（常用于老年代）
* 堆分为新生代和老年代。新生代有Eden、From survivor、To Survivor（如比例8:1:1），GC他叫Minor GC，很频繁但很快；老年代GC叫Major或Full GC，不频繁但是慢Minor GC要10倍以上。
* 内存分配与GC的策略：
	* 新对象优先分配到新生代的Eden和From：二者占90%。这个0.9不够就GC：Eden和From用复制算法到To，To够用就拷贝回Eden，To不够用需要老年代的担保机制。
	* 大对象直接接入老年代：大量连续内存的对象（如数组和字符串）直接放老年代。
	* 长期存活的对象进入老年代：使用存活过的GC次数计数Age。
	* 动态判定对象年龄：年龄Age都比较大或比较小，就可设置阈值，存活久的放老年代。
	* 空间分配担保：Minor GC之前，如果老年代有连续空间大于所有新生对象，那Minor GC肯定安全，因为老年代担保；但是如果老年代没有这么大连续空间，需要设置是否冒险。允许冒险且新生空间小于经验值，则尝试Minor GC，大不了放老年代；如果不允许或者允许冒险但是需要太大，则老年代GC保证肯定够新生用。
* 常见GC收集器：stop the world 问题、标记、清除、整理
	* 新生代的Serial、ParNew、Parallel Scavenge
	* 老年代的CMS、Serial Old、Parallel Old
	* 实验性G1


### java代码编译和执行的整个过程
---
java从编译到执行经过3个重要机制：源码编译机制、类加载机制、类的执行机制。

### 源码编译机制
---
* Java 源码编译由以下过程组成：分析和输入到符号表、注解处理、语义分析和生成 class 文件
* Class文件
	* class文件由01字节码组成，以字节为基础单元，是一种没有任何分隔符的字节流。各种运行在jvm之上的语言也都是编译生成class文件。class文件包括无符号数和表，根据jvm规范进行严格的解析。
	* class文件格式：
		* **class版本号**：magic/u4、minor_version/u2、major_version/u2，  4字节表magic确定class与jvm兼容性，2字节表次版本号，2字节表主版本号。
		* **常量池**：constant_pool_count/u2、constant_pool/cp_info,  2字节表常量数量(class唯一一个从1计数的count)，cp中存放**字面常量（字符串、final的常量等java常量）和符号引用（类和接口的全限定名、方法的名称和描述符、字段的名称和描述符）**。
		* **访问标志**：access_flag/u2, 2字节数值对应到该类或接口否为类或接口、是否为public、是否为abstract、是否final等信息的编码。
		* **类、父类、接口的索引**：this_class/u2、super_class/u2、interfaces_count/u2、interfaces_count/u2， 分别确定this类、父类和接口的全限定名。由于单一继承，因此除了java.lang.Object，其他类的super都不是0且唯一。由于多接口（类implements接口或接口extends接口），接口索引是个集合，根据定义时的从左向右放入集合中。
		* **字段表集合**：fields_count/u2、fields/fields_info，  java变量类型--类级变量（类的非static变量）、实例级变量（类的static变量）、局部变量（方法内部的变量）。字段表集合描述了类级变量和实例级变量的信息：字段作用域（public private protected）、是否实例级变量（static）、是否可变（final）、并发可见性（volatile要求强制从内存读写）、是否被序列化（transient）、字段数据类型（基本类型、对象、数组）、字段名称。具体表示使用复用access_flags编码、u2数据集合。
		* **方法表集合**：methods_count/u2、methods/method_info，  方法表集合的定义和字段表类似，但是volatile和transient不能修饰方法，而synchronized、native、strictfp、abstract可以修饰方法，所以取值稍微有些变化。此外，方法的代码所编译生成的部分存放在属性表集合的Code属性中。对于重载overload，class文件格式的特征签名包括了返回值、参数等修饰符，所以是支持仅有返回值不同的重载，但是java语言的特征签名不包括返回值，因此不支持仅有返回值不同的重载。
		* **属性表集合**：attributes_count/u2、attributes/attribute_info，  属性表集合用于存放专有的信息，class文件、字段表、方法表都可以在属性表集合中添加相应的数据。Code属性可以存放方法表中的方法的详细内容，ConstantValue存放字段表的final定义的常量，Deprecated属性存放deprecated的类、方法表、字段表，Exceptions属性存放方法表的异常信息等等，属性表内容非常广泛。
	* Class文件的特征签名包括了返回值、方法名、参数等修饰符，但是Java语言的特征签名没有包括返回值，所以**java不能通过返回值的不同来区分两个方法，Java只允许方法返回一个返回值。**
		* 重载 overload，指的是方法名一样，但是参数类型或者个数不同（修饰符和返回值没有区分作用）。最常见的就是写一个类有多个构造器，或者写多个只有参数不同的方法。
		* 重写又叫覆盖 override，用@override注解，指的是子类对父类的方法进行重写，要求返回值、方法名和参数都相同（修饰符可以缩小），通过动态分派自底向上查找继承关系找到对应的方法。
		
* 字节码指令：
	* jvm使用一个字节长度、代表着某种特定操作含义的数字表示操作码以及0到多个操作数构成指令。jvm指令是面向操作数栈而不是面向寄存器的架构。
	* 优点在于：放弃操作数对齐，指令集不必进行填空和间隔，操作码都是0-255之间的数字表示，短小精悍。
	* 缺点在于：如果操作数长度超过一个字节，需要运行时从字节重构出具体数据，导致解释执行字节码时有一定性能损失。
* JIT 编译
	* Java 程序最初是仅仅通过解释器解释执行的，即对字节码逐条解释执行，这种方式的执行速度相对会比较慢，尤其当某个方法或代码块运行的特别频繁时，这种方式的执行效率就显得很低。
	* 后来虚拟机引入了 JIT 编译器（即时编译器just in time），当虚拟机发现某个方法或代码块运行特别频繁时，就会把这些代码认定为“Hot Spot Code”（热点代码），为了提高热点代码的执行效率，在运行时，虚拟机将会把这些代码编译成与本地平台相关的机器码，并进行各层次的优化，完成这项任务的正是 JIT 编译器。
	* 由于JIT编译器的引入，java从“解释执行”语言，添加了“编译执行”的语言特性。

### 类的加载机制
---
* java语言编译时不需要进行连接工作，所以得到的class文件不能直接在内存中运行。java语言中，类型的加载、连接和初始化操作是在程序运行期间完成的，这种策略虽然会有一定性能开销，但是可以为java应用程序提供高度灵活性。java的动态拓展性就是依赖于运行期动态加载和动态连接实现的。这一特性常用于：面向接口编程，在运行时再指定其实现类；通过java加载器或者自定义加载器，让程序在运行时加载一个class文件到程序中。

#### 类的加载器
* 类的加载器是什么？为什么？怎么做？
* 类的加载器：通过一个类的全限定名来获取描述该类的二进制字节流的模块。这个动作放在jvm外部去实现，让应用程序自己决定如何获取所需要的类。
* 类在jvm中的唯一性是通过类加载器的类名称空间+类本身共同确定的，不同的类加载器加载的同一个类是不相等的（equals）。
* 类加载器：
	* Bootstrap ClassLoader 启动类加载器，使用C++实现，使用java访问到的是null。负责加载<JAVA_HOME>\lib目录特定文件。
	* Extension ClassLoader 扩展类加载器，负责加载<JAVA_HOME>\lib\ext目录，用户可以直接使用。
	* Application ClassLoader 应用程序类加载器，也成为系统类加载器，负责加载用户类路径Classpath的指定类库，可以直接使用，没有定义自己的类加载器时会默认使用该加载器。
	* Customer ClassLoader 用户自定义类加载器，只要extends java.lang.ClassLoader，重写loadclass(),如 Tomcat、jboss 都会根据 J2EE 规范自行实现 ClassLoader.
* 虽然这几个类加载器之间有父子关系，但是不是通过继承方式实现的，而是通过组合方式（代码内部控制）实现的。除了启动类加载器是c++写的，其他加载器都需要继承java.lang.ClassLoader。
* 双亲委派模型：如果一个类加载器收到了类加载的请求，它首先不会自己去尝试加载，而是将请求委派给父类加载器，每一个加载器都如此；一旦遇到一个加载器已经加载该类，就视为已加载此类，保证此类只被加载一次；如果父加载器反馈自己无法加载目标类时，子加载器才会尝试自己去加载。

#### 类加载
类加载的过程：加载、验证、准备、解析（这三者统称为连接）、初始化。正常使用、卸载。
* **类的加载**：加载器加载类的二进制字节流（Class文件）。
* **类的验证**：验证class文件的格式规范、元数据、字节码、符号引用等是否安全有效。
* **类的准备**：为类变量（static变量）分配内存并设置类变量初始值，这些内存都在方法区。但是实例变量是不在这里的，实例变量如 private int a 会随着实例对象放在堆中。而且初始值是jvm内部默认的类型初始值，如数值初始为0，引用初始为null，如int a=3初始化为0，只在初始化的`<clinit>`()中才会进行类变量赋值变成3。但是如果是 static final的ConstValue则会直接分配并设置为指定的初始值，再也不会改变。
* **类的解析**：虚拟机将常量池中的符号引用转化为直接引用的过程。（Class文件中的常量池解析到运行时常量池、方法的静态解析等工作）
* **类的初始化**
	* 类的初始化是什么？为什么？怎么做？
	* 类的初始化：执行类构造器`<clinit>`()方法。用于执行程序中的初始化操作和其他资源。问题在于什么是类构造器`<clinit>`()。
	* 类构造器`<clinit>`()方法：
		* **`<clinit>`()方法是编译器自动收集类中的所有类变量的赋值动作和静态代码块（static块）的语句合并产生的**，收集顺序与程序编写顺序有关。static块只能访问定义在其之前的变量，对于定义在其后面的变量只能进行赋值，而不能访问，否则就是非法向前引用。
		* `<clinit>`()方法与类的构造函数（实例构造器<init>()）不同，`<clinit>`()不需要显示调用父类的`<clinit>`()，虚拟机会自动保证在子类的`<clinit>`()执行之前，已经执行完成父类的`<clinit>`()。
		* `<clinit>`()总是先完成父类的`<clinit>`()，也就是先完成父类的类变量赋值、static{}。
		* 一个类并不总是需要有`<clinit>`()，如果没有类变量赋值和static{}，编译器可以不生成其`<clinit>`()。
		* 接口中没有static{}，但还是有类变量赋值，所以也是有`<clinit>`()，但子接口和实现类不需要调用父接口的`<clinit>`()，除非使用到了父接口（如访问父接口的变量）。
		* 虚拟机可以保证`<clinit>`()在多线程时也被正确的加锁、同步，多线程初始化一个类时，会同步锁，只有一个线程执行`<clinit>`()，其他需要阻塞等待，所以`<clinit>`()太繁重也会造成长时间阻塞。
	* 类的初始化时机有且只有5种情况（称为主动引用）：
		* 遇到new、getstatic、putstatic、invokestatic这4个字节码指令时，如果类没有进行过初始化，必须先对其初始化。常见场景是：使用new创建实例对象、读取或设置一个类的静态字段（被final修饰、已在编译期把结果放入常量池的静态字段除外）、调用一个类的静态方法。
		* 使用java.lang.reflect包的方法对类进行反射调用的时候，如果类没有初始化，需要进行初始化。
		* 当初始化一个类的时候，如果其父类没有初始化，需要先对其父类初始化。*但是一个接口在初始化时，并不要求其接口已经初始化，只有在真正使用到父接口的时候才会进行父接口的初始化（虽然没有static静态语句块，但是也有变量初始化赋值）。另外，接口的实现类在初始化时也不要求接口初始化。*
		* jvm启动时，用户需要指定一个要执行的主类（含有main()方法的那个类），jvm会先初始化这个主类。
		* 当使用jdk1.7的动态语言支持时，如果一个java.lang.invoke.MethodHandle实例最后的解析结果为REF_getStatic、REF_putStatic、REF_invokeStatic的方法句柄，而该方法句柄对应的类没有进行初始化，则需要先触发其初始化。
	* 除了5种主动引用，其他都是被动引用，不会触发类的初始化，比如：子类引用父类的静态字段，不会导致子类初始化；通过数组定义来引用类，不会触发数组类的初始化；常量在编译时存入调用类的常量池，本质上没有直接引用定义类，不会触发定义常量的类的初始化。

* java代码运行 
	* --》  jvm需要加载运行主类main()，自底向上检查该类是否已经加载，发现该类未加载 
	* --》  自顶向下调用加载器尝试加载类，类加载时会开始验证、准备、解析操作，但是各阶段结束先后不确定 
	* --》  从Object向下加载了很多类，当前类也加载完成  
	* --》  尝试初始化：运行本类的clinit方法（对类变量赋值和static{}代码块），但是发现父类的clinit尚未执行 
	* --》  于是需要先完成父类的clinit，一直向上查找运行clinit，所以一定是Object类最先完成clinit。但是注意子接口或者实现类不需要执行父接口的clinit，只在使用到时才初始化  
	* --》  自顶向下完成了clinit  
	* --》  运行程序，遇到主动引用时进行类加载检验。


### 类的执行机制
---
虚拟机执行涉及到栈帧结构、方法调用、字节码执行。

#### 栈帧和方法计数器
* JVM 是基于栈的体系结构来执行 class 字节码的。
* 线程创建后，都会产生程序计数器（PC）和栈（Stack，虚拟机栈和本地方法栈）
	* 程序计数器存放下一条要执行的指令在方法内的偏移量
	* 栈中存放一个个栈帧，每个栈帧对应着每个方法的每次调用，而栈帧又是有局部变量区和操作数栈两部分组成，局部变量区用于存放方法中的局部变量和参数，操作数栈中用于存放方法执行过程中产生的中间结果。

####  方法调用
---
* 方法调用并不是方法执行，方法调用的目的是确定被调用方法的版本（到底运行哪一个方法）。
* 方法执行是在方法调用确定了方法之后，执行方法的代码，执行方式有解释执行（java主要用基于栈的字节码解释执行引擎）和编译执行（java也有JIT等编译器）。
* 方法调用的方式有解析、分派。
* jvm共提供了四条方法调用的字节指令，分别是：
	* invokestatic：调用静态方法。
	* invokespecial：调用实例构造器方法、私有方法和父类方法。
	* invokevirtual：调用所有的虚方法。
	* invokeinterface：调用接口方法，会在运行时再确定一个实现此接口的对象。

##### 解析 Resolution
* 方法解析又叫方法的静态解析，静态调用。静态解析是指： Class 文件的符号引用在**类加载阶段或第一次使用时**转化为直接引用。
* 静态解析成立的前提是：方法在程序真正执行前就有一个可确定的调用版本，并且这个方法的调用版本在运行期是不可改变的，也就是方法具有“编译器可知，运行期不可变”的特点。换句话说，调用目标（方法）在编译器进行编译时就必须确定下来（运行时不会再改变），这类方法的调用称为方法解析。
* 解析调用一定是个静态过程，在编译期间就完全确定对应的方法，在**类加载的解析阶段**就会把涉及的符号引用转化为可确定的直接引用，不会延迟到运行期再去完成方法调用。
* 只要能被 invokestatic 和 invokespecial 指令调用的方法，都可以在解析阶段确定唯一的调用版本，符合这个条件的有静态方法、私有方法、实例构造器和父类方法四类，它们在类加载时就会把符号引用解析为该方法的直接引用。这些方法可以称为非虚方法，与之相反，其他方法就称为虚方法。非虚方法都可以通过解析实现方法调用。
* 注意：特别地，final 方法是一种非虚方法，它使用的是 invokevirtual 指令，但是由于它无法覆盖，没有其他版本，所以也无需对方发接收者进行多态选择。final方法属于非虚方法，可以通过解析实现方法调用。
	

##### 分派 Dispatch
* 分派调用可能是静态的也可能是动态的，根据分派依据的宗量数（方法的调用者和方法的参数统称为方法的宗量）又可分为单分派和多分派。两类分派方式两两组合便构成了静态单分派、静态多分派、动态单分派、动态多分派四种分派情况。
* 动态连接
	* Class 文件的符号引用在每一次的运行期间转换为直接引用。
	* 这个特性给 Java 带来了更强大的动态扩展能力，使得可以在类运行期间才能确定某些目标方法的直接引用。
* 分派机制是多态性的实现机制。目前的 Java 语言是一门静态多分派、动态单分派的语言，但也在增加一些动态语言特性，如jdk1.7增加invokedynamic指令等。
* **静态分派**
	* 静态分派：根据静态类型来定位方法的执行版本的方法分派。最典型应用就是多态性中的方法重载 overload。
	* 静态分派发生在编译阶段，因此确定静态分配的动作实际上不是由虚拟机来执行的。
	* Human man = new Man();hi(man);
	* 我们把上面代码中的“Human”称为变量的静态类型，后面的“Man”称为变量的实际类型。静态类型和实际类型在程序中都可以发生一些变化，区别是静态类型的变化仅仅在使用时发生，变量本身的静态类型不会被改变，并且最终的静态类型是在编译期可知的（如hi((Man)man);//强制变换静态类型为Man，但man本身的静态类型仍是Human，之后再用还是Human），而实际类型变化的结果在运行期才可确定（如 man=new Woman();//静态类型不变但是实际类型变Woman，之后以Woman使用）。
	* 重载等静态分派是通过静态类型和参数个数定位方法的，所以这里hi(man)调用hi(Human h)方法，与man的实际类型Man无关。所以hi(new Human())、hi(new Man())、hi(new Woman())分别静态分派到hi(Human h)、hi(Man h)、hi(Woman h)，没有对应方法是无法编译通过的。
	* 重载的结果可能并不唯一，比如hi('a')，由于字面量'a'没有显式的静态类型，所以可能会先后尝试重载到hi(char)/hi(int)/hi(long)等等方法。只要有显式的静态类型，没有对应的方法就只能通不过编译了。
* **动态分派**
	* 动态分派：在运行期根据实际类型确定方法执行版本的分派。最典型应用就是多态性中的方法重写 override。
	* Human man2 = new Man(); Human woman2 = new Woman(); man2.get(); woman2.get();
	* 二者的静态类型都是Human，如果使用静态分配就无法访问子类中的不同方法。动态分派在运行期根据实际类型确定方法执行版本。
	* 在这里，程序运行时使用invokevirtual字节码指令解析程序，其方法调用基本流程为：取操作数栈顶的栈顶元素的**实际类型**，如果能找到相符的方法则进行访问验证，验证失败则抛出异常，验证通过则返回其直接引用；如果找不到相符的方法，则按照对象的继承关系**自底向上**查找其父类对应的方法，并进行验证。直至结束或者异常。这就是动态分派和重写的本质。
* **单分派与多分派**
	* 方法的接受者（即方法的调用者）与方法的参数统称为方法的宗量。单分派是根据一个宗量对目标方法进行选择，多分派是根据多于一个宗量对目标方法进行选择。
	* java中，静态分派（如重载）区分参数（个数、类型），也区分调用者的静态类型，区分2个宗量，所以java是静态多分派。比如Integer.toString()和Long.toString()肯定是类型信息和参数信息均有效。
	* java中，动态分派（如重写）由于编译期已经了确定了目标方法的参数类型（编译期根据参数的静态类型进行静态分派），可以影响方法的选择的只有调用者的实际类型，只有一个宗量作为选择依据，所以 Java 语言的动态分派属于单分派类型。（即使有静态分派，动态分派也在静态分派之后）

### 语法糖
* 语法糖（Syntactic Sugar），也称糖衣语法，是由英国计算机学家 Peter.J.Landin 发明的一个术语，指在计算机语言中添加的某种语法，这种语法对语言的功能并没有影响，但是更方便程序员使用。
* Java 中最常用的语法糖主要有*泛型、变长参数、条件编译、自动拆装箱、内部类*等。虚拟机并不支持这些语法，它们在编译阶段就被还原回了简单的基础语法结构，这个过程称为解语法糖。



## 多线程与并发性
---
### 互斥与可见性
* 锁提供了两种主要特性：互斥（mutual exclusion） 和可见性（visibility）。
* 互斥即一次只允许一个线程持有某个特定的锁，因此可使用该特性实现对共享数据的协调访问协议，这样，一次就只有一个线程能够使用该共享数据。
* 可见性要更加复杂一些，它必须确保释放锁之前对共享数据做出的更改对于随后获得该锁的另一个线程是可见的 —— 如果没有同步机制提供的这种可见性保证，线程看到的共享变量可能是修改前的值或不一致的值，这将引发许多严重问题。

### 线程调度
* java线程调度也有协同式和抢占式。
* 协同式：由各个线程自身运行完成之后才会通知系统切换下一个线程。优点是实现简单，不需要太多线程同步问题。缺点是长时间的线程会导致阻塞后续线程。
* 抢占式：系统分配各个线程时间。java早期用协同式，现在都用抢占式，Thread.yield()虽然是让出执行时间，但是真正的执行还需要系统安排，不会真的决定当前线程最后运行。抢占式调度的优点是各线程可控，缺点是同步比较麻烦。

### java线程状态
总的来说，就是new一个得到new状态的线程；start成runable得到CPU是running，等待cpu是ready；runable遇到无限期等待和定时等待变成等待状态，等待对应的时间或者唤醒事件；runable遇到同步块变成blocked阻塞，等待获取锁；运行结束为terminated。

* New 新建  通过new Thread创建的处于新建态。线程运行start()进入runable态。
* Runable 运行 包括了得到CPU时间的running态和等待分配CPU时间的ready态。
* Waiting 无限期等待 这种状态需要其他线程显示唤醒（notify()、notifyAll()），一般没有设置Timeout参数的Object.wait()和Thread.join()
* Timed waiting 定时等待  不需要其他线程显示唤醒，一般是设置了Timeout参数的Thread.sleep()，Object.wait()，Thread.join()方法。
* Blocked 阻塞 程序进入同步区域的时候线程会阻塞。阻塞是在等待获取一个排它锁，定时等待和无限期等待是在等待时间或者等待唤醒事件。 
* Terminated 结束  已终止线程状态，线程正常运行结束或异常退出。


### java实现线程安全的方法
#### 互斥同步
* 同步是指在多线程并发访问共享数据时，保证共享数据在同一时刻只被一个线程使用。互斥是实现同步的一种方法，常用临界区critical section、互斥量mutex、信号量semaphore。
* java实现互斥常用synchronized关键字（对象级锁和类级别锁），早期效果不太好，现在优化之后synchronized的效率提高了很多。
* java实现互斥也用java.util.concurrent包（JUC包）的ReentrantLock可重入锁。ReentrantLock与synchronized同样具有线程可重入特性。不同点在于ReentrantLock使用的是lock() unlock() condition() 实现，以及提供了高级特性：等待可中断、可实现公平锁、锁可以绑定多个条件。
	* 等待可中断：持有锁的线程长时间不释放锁的话，等待线程可以放弃等待，改为处理其他事情。适用于用时很长的同步块。
	* 公平锁：多个线程等待同一个锁时按照申请顺序依次获得锁。默认synchronized和ReentrantLock的锁都是非公平锁，ReentrantLock提供传入boolean实现公平锁的方法。
	* 锁可以绑定多个条件：ReentrantLock可以使用多个newCondition()方法实现多个条件，但是synchronized需要额外添加锁才能实现多个条件。
* 互斥同步主要问题在于线程阻塞和唤醒需要转入内核态运行，非常影响并发性能，属于阻塞同步Blocking Synchronization。
* 互斥同步这种阻塞同步属于悲观的并发策略、**悲观锁**：总认为必须进行加锁才能实现安全，否则就会出错。

#### 非阻塞同步
* 非阻塞同步Non-Blocking Synchronization采用基于冲突检测的乐观并发策略**乐观锁**：先进行操作，如果没有其他线程争用共享数据，则操作成功，如果有共享数据争用，产生了冲突，就采用补偿措施，常用的是不断重试直到成功。
* 非阻塞同步不需要线程挂起等待，通常基于硬件指令集保证同步。
	* 硬件指令集之**CAS 比较并交换Compare-and-Swap**指令：一种硬件上保证语义上需要多次操作的行为只需要一条处理器指令就能完成。除了CAS还有swap、fetch-andincrement、test-and-set、load-linked/store-conditional等等。

#### 无同步方法
* 可重入代码
* 线程本地存储



### 锁优化
* **自旋锁和自适应锁**：
	* 自旋锁：线程挂起和恢复是很费事的，而共享数据的锁定往往很快就会结束，所以在多处理器可以并行执行两个及以上线程时，第一个线程锁定共享数据后，第二个请求共享数据的线程并不挂起，不会放弃CPU时间，而是执行一个忙循环（自旋），看看持有锁的线程是否会很快释放锁。
	* 锁定的时间越短，自旋等待的效果越好，否则就是白白消耗cpu在等待。自旋等待超过了限定次数（默认10），应该正常挂起线程。
	* 自适应的自旋锁：根据之前的自旋效果决定本次自旋等待次数（自旋时间），如果该锁上一次的自旋等待成功了，现在又要对其自旋等待，那么可以适当增加本次自旋等待时间，否则就减少等待时间。
* 锁消除：
	* 逃逸分析技术：分析对象动态作用域。当一个对象在方法中被定义后，如果被外部方法引用，就是**方法逃逸**，比如对象作为方法的调用参数传递到其他方法；如果被外部线程访问到，就是**线程逃逸**，比如对象赋给类变量，或者赋给其他线程的实例变量。
	* 基于逃逸分析技术，如果一段代码中所有堆上数据都不会逃逸到其他线程，就可以作为栈上数据对待，认为线程私有，不必同步加锁，实现锁消除。
* 锁粗化
	* 大部分情况下同步块应尽量小，减少同步操作数量，但是频繁加锁和解锁的操作需要粗化扩大加锁同步的范围，典型例子就是连续的sb.append()。
* 轻量级锁
	* HotSpot虚拟机中对象头包括了自身运行时数据Mark Word，是实现轻量级锁和偏向锁的关键。
	* 轻量级锁是在无竞争的情况下使用CAS操作避免使用同步互斥量mutex。
	* 如果存在竞争（多于两个线程在竞争同一个锁），轻量级锁会失效，需要膨胀使用重量级锁（传统使用互斥量实现的锁机制），使用轻量级锁反而会增加开销。
* 偏向锁
	* 偏向锁是在无竞争的情况下把整个同步都消除掉，连CAS都不做。
	* 偏向锁是指这个锁总是偏向于第一个获得它的线程，如果接下来该锁一直不被其他线程获取，则持有偏向锁的线程永远不需要再同步。
	* 和轻量级锁一样，偏向锁也只适合有同步但无竞争的程序，如果出现竞争，就会退出偏向模式，徒增同步开销。


## 多线程具体技术
---
### CompareAndSwap CAS乐观锁
* CAS指令包括内存位置V、旧值A、V新值B，操作过程为：“我认为V的值应该为A，如果是，那么将V的值更新为B，否则不修改，最后返回V的值实际为多少”。
* CAS属于乐观锁技术，当多个线程尝试使用CAS同时更新同一个变量时，只有其中一个线程能更新变量的值，而其它线程都失败，失败的线程并不会被挂起，而是被告知这次竞争中失败，并可以再次尝试。
* 使用CAS
	* JDK1.5开始java中可以使用CAS操作，但是封装在sun.misc.Unsafe类里面，包装为compareAndSwapInt()、compareAndSwapLong()等几个方法，编译之后的结果就是平台相关的一条CAS指令。
	* Unsafe类是无法直接调用的，java限定只能由Bootstrap ClassLoader加载的类才能访问，所以开发人员只能用反射机制或者使用封装CAS方法的类才能使用CAS。
	* JVM会将CAS特殊处理，编译为一条与平台相关的CAS处理器指令。

```
// CAS 的C语言实现
int compare_and_swap (int* reg, int oldval, int newval) 
{
  ATOMIC();
  int old_reg_val = *reg;
  if (old_reg_val == oldval) 
     *reg = newval;
  END_ATOMIC();
  return old_reg_val;
}
```

```
package jvm;
import sun.misc.Unsafe;
import java.lang.reflect.Field;
import java.util.Arrays;

/**
 * 使用反射机制使用CAS CompareAndSwap
 */
public class _14CompareAndSwap {
    private static Unsafe unsafe;

    static {
        try {
            // 通过反射获取rt.jar下的Unsafe类, getDeclaredField是可以获取一个类的所有字段. getField只能获取public字段
            Field field = Unsafe.class.getDeclaredField("theUnsafe");
            //Unsafe类提供了一个private static final Unsafe theUnsafe，可以用于反射
            field.setAccessible(true);
            // 在java的反射中,通过字段获取对象的字段值field.get(object),字段不是静态字段的话,要传入反射类的对象
            // 如果传null是会报错，如果字段是静态字段的话,传入任何对象都是可以的,包括null
            unsafe = (Unsafe) field.get(null);  // 获取Unsafe类的 private static final Unsafe theUnsafe 对象。
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    static class Target {
        int intParam = 3;
        long longParam;
        String strParam;
        String StrParam2;
    }

    /**
     * compareAndSwapInt是通过反射根据字段偏移去修改对象的.
     * 可以看到int是4个字节的偏移量,long是4个字节的偏移量,string是4个字节的偏移量
     * 注意 Unsafe的对象不能直接new,要通过反射去获取.
     */
    public static void main(String[] args) throws Exception {
        Class targetClass = Target.class;
        Field[] fields = targetClass.getDeclaredFields();
        System.out.println("fieldName:fieldOffset");
        // 获取属性偏移量
        Arrays.asList(fields).forEach(field ->
                System.out.println(field.getName() + ":" + unsafe.objectFieldOffset(field)));
        Target target = new Target();
        Field intFiled = targetClass.getDeclaredField("intParam");
        int a = (Integer) intFiled.get(target); // 反射，field.get(obj)获取对象相应的field
        System.out.println("原始值是:" + a);
        //intParam的字段偏移是12 原始值是3 我们要改为10
        // CAS方法封装，对target进行内存偏移V旧值是否是3，是则更新为新值10，返回更新boolean
        System.out.println(unsafe.compareAndSwapInt(target, 12, 3, 10));
        int b = (Integer) intFiled.get(target);
        System.out.println("改变之后的值是:" + b);
        //这个时候已经改为10了,所以会返回false
        System.out.println(unsafe.compareAndSwapInt(target, 12, 3, 10));
        System.out.println(unsafe.compareAndSwapObject(target, 24, null, "5"));
    }
}

```

```
package jvm;
import java.util.concurrent.atomic.AtomicInteger;

/**
 * AtomicInteger AtomicLong等封装了CAS的方法，封装为getAndSet()、compareAndSet()、accumulateAndGet()等原子性方法
 * 调用这些原子性方法
 * Created by dell on 2017/8/19.
 */
public class _15AtomicInteger {
    static AtomicInteger atomicInteger = new AtomicInteger(0);

    static void increase() {
        atomicInteger.incrementAndGet();  // 调用AtomicInteger类的原子性方法进行原子性自增
        System.out.println(atomicInteger.getAndIncrement());
    }

    public static void main(String[] args) {
        int threadCount = 20;
        Thread[] threads = new Thread[threadCount];
        for (int i = 0; i < threadCount; i++) {
            threads[i] = new Thread(() -> {
                for (int j = 0; j < 1000; j++) {
                    increase();
                }
            });
            threads[i].start();
        }

//        while (Thread.activeCount() > 1) {
//            Thread.yield();
//        }
        System.out.println(atomicInteger);

    }
}

```

### Thread类和Runnable接口
* 继承Thread类，重写run方法：class MyThread extends Thread{ public void run(){ }} 然后new MyThread().start(); 启动线程。
* 实现Runnable接口，实现run方法：class MyRun implements Runnable{ public void run(){ }}，然后MyRun my = new MyRun();new Thread(my).start(); 如果使用多个new Thread(my).start() 访问的是同一个my对象，注意是否存在线程安全问题。

### volatile 关键字
* 共享内存(主存)与私有拷贝
 	* Java 语言规范中指出：为了获得最佳速度，允许线程保存共享成员变量的私有拷贝，而且只当线程进入或者离开同步代码块时才将私有拷贝与共享内存中的原始值进行比较。
	* 在当前的 Java 内存模型下，线程可以把变量保存在本地内存（比如机器的寄存器）中，而不是直接在主存中进行读写。这就可能造成一个线程在主存中修改了一个变量的值，而另外一个线程还继续使用它在寄存器中的变量值的拷贝，造成数据的不一致。
* volatile 修饰符
	* class a{private volatile int a;}指示 JVM，这个变量a是不稳定的，线程每次使用它都到主存中进行读取：线程每次访问成员变量时，都强迫从共享内存中重读该成员变量的值；而且，当成员变量发生变化时，强迫线程将变化值回写到共享内存。这样在任何时刻，两个不同的线程总是看到某个成员变量的同一个值。
	* volatile 是一种稍弱的同步机制，在访问 volatile 变量时不会执行加锁操作，也就不会执行线程阻塞，因此 volatile 的变量是一种比 synchronized 关键字更轻量级的同步机制。但是volatile屏蔽掉了 JVM 中必要的代码优化（如代码重排序以提高cpu效率），所以效率比较低。
* volatile使用要求
Volatile 变量具有 synchronized 的可见性特性，但是不具备原子特性。这就是说线程能够自动发现 volatile 变量的最新值。Volatile 变量可用于提供线程安全，但是只能应用于非常有限的一组用例：多个变量之间或者某个变量的当前值与修改后值之间没有约束。因此，单独使用 volatile 还不足以实现计数器、互斥锁或任何具有与多个变量相关的不变式（Invariants）的类（例如 “start <=end”）。

要使 volatile 变量提供理想的线程安全，必须同时满足下面两个条件：
* 对变量的写操作不依赖于当前值。
* 该变量没有包含在具有其他变量的不变式中。

实际上，这些条件表明，可以volatile 变量要 独立于任何程序的状态，包括变量的当前状态。

第一个条件的限制使 volatile 变量不能用作线程安全计数器。虽然增量操作（x++）看上去类似一个单独操作，实际上它是一个由读取－修改－写入操作序列组成的组合操作，如果多个线程凑巧试图同时对 volatile 计数器执行增量操作，那么它的更新值有可能会丢失，因此增量操作必须以原子方式执行，而 volatile 不能提供必须的原子特性。实现正确的操作需要使 x 的值在操作期间保持不变，而 volatile 变量无法实现这点。（然而，如果将值调整为只从单个线程写入，那么可以忽略第一个条件。）大多数编程情形都会与这两个条件的其中之一冲突，使得 volatile 变量不能像 synchronized 那样普遍适用于实现线程安全。
* volatile常用场景
	* 模式 #1：状态标志  比如使用一个布尔状态标志，用于指示发生了一个重要的一次性事件，例如完成初始化或请求停机。
	* 模式 #2：一次性安全发布  
	* 模式 #3：独立观察  比如一个线程在读写，其他线程只能读
	* 模式 #4：“volatile bean” 模式
	* 模式 #5：开销较低的读－写锁策略

### synchronized 关键字
采用 synchronized 修饰符实现的同步机制叫做互斥锁机制，它所获得的锁叫做互斥锁。每个对象都有一个 monitor (锁标记)，当线程拥有这个锁标记时才能访问这个资源，没有锁标记便进入锁池。任何一个对象，系统都会为其创建一个互斥锁，这个锁是为了分配给线程的，防止打断原子操作。每个对象的锁只能分配给一个线程，因此叫做互斥锁。

这里就使用同步机制获取互斥锁的情况，进行几点说明：

0. 同步关键字只能用于方法、代码块、final字段以及类。这些方法或块既可以是静态的也可以是非静态的。
1. 如果同一个方法内同时有两个或更多线程，则每个线程有自己的局部变量拷贝。（JVM主存到线程工作内存的拷贝）
2. 类的每个实例都有自己的对象级别锁。当一个线程访问实例对象中的 synchronized 同步代码块或同步方法时，该线程便获取了该实例的对象级别锁，其他线程这时如果要访问 synchronized 同步代码块或同步方法，便需要阻塞等待，直到前面的线程从同步代码块或方法中退出，释放掉了该对象级别锁。
3. 访问同一个类的不同实例对象中的同步代码块，不存在阻塞等待获取对象锁的问题，因为它们获取的是各自实例的对象级别锁，相互之间没有影响。
4. 持有一个对象级别锁不会阻止该线程被交换出来，也不会阻塞其他线程访问同一实例对象中的非 synchronized 代码。当一个线程 A 持有一个**对象级别锁（即进入了 synchronized 修饰的代码块或方法中）**时，线程也有可能被交换出去，此时线程 B 有可能获取执行该对象中代码的时间，但它只能执行非同步代码（没有用 synchronized 修饰），当执行到同步代码时，便会被阻塞，此时可能线程规划器又让 A 线程运行，A 线程继续持有对象级别锁，当 A 线程退出同步代码时（即释放了对象级别锁），如果 B 线程此时再运行，便会获得该对象级别锁，从而执行 synchronized 中的代码。
5. 持有对象级别锁的线程会让其他线程阻塞在所有的 synchronized 代码外。例如，在一个类中有三个synchronized 方法 a，b，c，当线程 A 正在执行一个实例对象 M 中的方法 a 时，它便获得了该对象级别锁，那么其他的线程在执行同一实例对象（即对象 M）中的代码时，便会在所有的 synchronized 方法处阻塞，即在方法 a，b，c 处都要被阻塞，等线程 A 释放掉对象级别锁时，其他的线程才可以去执行方法 a，b 或者 c 中的代码，从而获得该对象级别锁。
6. 使用 synchronized（obj）同步语句块，可以获取指定对象上的对象级别锁。obj 为对象的引用，如果获取了 obj 对象上的对象级别锁，在并发访问 obj 对象时时，便会在其 synchronized 代码处阻塞等待，直到获取到该 obj对象的对象级别锁。当 obj 为 this 时，便是获取当前对象的对象级别锁。
7. **类级别锁**被特定类的所有实例共享，它用于控制对 static 成员变量以及 static 方法的并发访问。具体用法与对象级别锁相似。
8. 互斥是实现同步的一种手段，临界区、互斥量和信号量都是主要的互斥实现方式。synchronized 关键字经过编译后，会在同步块的前后分别形成 monitorenter 和 monitorexit 这两个字节码指令。根据虚拟机规范的要求，在执行 monitorenter 指令时，首先要尝试获取对象的锁，如果获得了锁，把锁的计数器加 1，相应地，在执行 monitorexit 指令时会将锁计数器减 1，当计数器为 0 时，锁便被释放了。由于 synchronized 同步块对同一个线程是可重入的，因此一个线程可以多次获得同一个对象的互斥锁，同样，要释放相应次数的该互斥锁，才能最终释放掉该锁。

* 个人总结来说：
	* 同步块有同步代码块和同步方法之分：synchronized（A）{}这种是同步代码块，void synchronized get(){}这种是同步方法。
	* 锁有对象级别锁、类级别锁之分，加锁就是将对象级别锁、类级别锁的引用数加1，引用数为0才可以给其他线程使用。线程退出同步块会将引用数减1，注意内置锁是可重入的，因此可能多次加锁，然后逆序解锁。（**内置锁是可重入的**:对象级别锁、类级别锁一旦被一个线程用于，这个线程可以多次对其加锁，引用数不断变大）。
	* 线程在访问同步块的时候必须看该同步块所要用到的锁是不是引用数为0，为0才能访问，不为0只能阻塞等待变成0。如果线程要访问的不是同步块或者要访问的是其他加锁数为0的对象等等情况，那是可以访问的。
	* 同步代码块和同步方法根据是不是static、锁定的对象设置，对应到对象级别锁、类级别锁之分，具体分类如下。

```
// 对象级别锁
public class DemoClass{	
    // 普通的synchronized方法
    public synchronized void demoMethod(){}
	
    // 同步代码块的部分会加锁，this锁定的是对象实例本身
    public void demoMethod(){
        synchronized (this)
        {
            //other thread safe code
        }
    }
	
    // 同步代码块也可以锁定 final 的对象，不是static，所以实例对象都会有该lock
    private final Object lock = new Object();
    public void demoMethod(){
        synchronized (lock)
        {
            //other thread safe code
        }
    }
}


// 类级别锁
public class DemoClass{
    // static的synchronized方法就是获取类级别的锁了
    public synchronized static void demoMethod(){}

    // 同步代码块直接锁定该类的类级别锁
    public void demoMethod(){
        synchronized (DemoClass.class)
        {
            //other thread safe code
        }
    }
	
    // 同步代码块直接锁定类常量获得类级别锁，只需要加锁对象是类的final static对象。
    private final static Object lock = new Object();
    public void demoMethod(){
        synchronized (lock)
        {
            //other thread safe code
        }
    }
```

### 死锁
* 死锁条件：资源互斥使用、不可抢占、请求和保持、循环等待。
* java避免死锁方法（实现线程安全的方法）：
	* 互斥同步（悲观锁）：总认为必须进行加锁才能实现线程安全。临界区、互斥量、信号量。常用互斥量有synchronized同步块实现的对象级别锁和类级别锁，以及Reentrant可重入锁提供的Lock/Condition方法。
	* 非阻塞同步（乐观锁）：先进行操作，争用失败则重试补偿。硬件指令集保证一条硬件指令完成多次操作。常见指令：swap、fetch-andincrement、test-and-set、CAS、load-linked/store-conditional。
	* 无同步方法：可重入代码、线程本地存储。







