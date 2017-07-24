---
title: Question
comments: true
date: 2017-03-01 13:58:04
updated: 2017-03-01 13:58:04
categories: Algorithm
tags:
- Question
---

**说明：**常见问题。
<!-- more -->

---
* 参考文章：
	* [各种算法](http://blog.csdn.net/lyhvoyage/article/details/22926265)
	* 《剑指offer》
	* LeetCode


## 编程与算法
---
* 《设计模式》
	* 4位作者（全拼Gang of four，四人帮？）提出的面向对象的设计原则。
	* 核心观点：对接口编程而不是对实现编程；优先使用对象组合而不是使用继承。
* 设计模式四个方面：
	* 创建型模式：工厂模式、抽象工厂、单例、创造者、原型 （5）
	* 结构型模式：适配器、桥接、过滤器、组合、装饰器、外观、享元、代理 （8）
	* 行为型模式：责任链、命令、解释器、迭代器、中介者、备忘录、观察者、状态、空对象、策略、模板、访问者 （12）
	* J2EE模式：MVC模式、业务代表、组合实体、数据访问对象dao、前端控制器、拦截过滤器、服务定位器、传输对象 （8）
* 设计模式6大原则：
	* 开闭原则：对拓展开放，对修改关闭。拓展时不能修改原有代码以实现热拔插。所以常用结构和抽象类进行抽象化。
	* 里氏代换原则LSP：任何基类可以出现的地方，子类一定可以出现。LSP 是继承复用的基石，只有当派生类可以替换掉基类，且软件单位的功能不受到影响时，基类才能真正被复用，而派生类也能够在基类的基础上增加新的行为。lsp是关闭原则的抽象化的具体步骤规范。
	* 依赖倒转原则：针对接口编程，依赖于抽象而不依赖于具体。是关闭原则的基础。
	* 接口隔离原则：使用多个隔离的接口，降低类之间的耦合性。
	* 最少知道原则：一个实体应尽量少地与其他实体发生相互作用，提高相对独立性。
	* 合成复用原则：尽量使用合成、聚合的方式，而不是使用继承。


* 对一个链表进行操作，（剑指offer）
	* 如何查找中间的结点且时间O阶为O(n)？ 
		* 使用两个指针指向头部，第一个+1，第二个+2，先到尾部的时候第一个就到中间了
	* 如何输出1，8,2,7,3,6的顺序？ 
		* 使用上面的两个指针，到第二个到尾部之后，1号向尾部，2号向头部输出
	* 如何判断链表是否存在环？ 
		* 使用两个指针，1号加1，二号加2，一直循环，如果出现二者重合说明存在环，比如最后两个相互指向的情况。
	* 如何输出环出现的入口结点？ 
		* 上面两个指针出现p1==p2则找到在环中的相汇点。
		* 当p1==p2时，p2所经过节点数为2x,p1所经过节点数为x,设环中有n个节点,p2比p1多走一圈有2x=n+x; n=x;可以看出p1实际走了一个环的步数，再让p2指向链表头部，p1位置不变，p1,p2每次走一步直到p1==p2; 此时p1指向环的入口。

## 单例模式

* 只支持单线程的方法：类的一个static getInstance（）
* 支持多线程但加锁消耗太多的方法：
* 支持多线程并改进加锁消耗的方法：
* 静态方法自动加载的方法：private static T instance = new T()  ...getInstance() {return instance;}
* 静态内部类掉用时才创建的方法：class T { class Y{public static T instance = new T()} public static getInstance() {return Y.instance;}


## 深搜广搜
---
* 计算hit转cog要多少步，每次只能变1个，只能用hot，dot，dog，lot，log，示例hit，hot，dot，dog，cog是5步
* 树中，出现各个路径或者遍历就应该考虑用栈
	* 如：求各个路径上的和、遍历整个树。
* 用队列实现栈、用栈实现队列问题？
	* 两个队列实现栈：栈的push就是一个队列正常队尾入队，栈的pop就是将所有数据从头出并从另一个队尾进且不添加最后一个，然后将另一个队尾的数据再全部出队并入队到原队列中。
	* 两个栈实现队列：队列的入队就是一个栈的正常入栈，队列的出队就是栈全部pop并push到栈B，栈Bpop出一个数据就是出队，然后将栈B再全部pop并push到原栈。通过画一下栈和队列的出入方向，写一下abc示例一下就清楚了。


## 未分类
---
* 浮点型数据进行比较是否为0的时候，不能用 a==0 进行判断！使用Math.abs(a)<1e-6 之类的小数进行控制。
* 求n个连续的x(1-9)构成的数字能被y(int)整除，其中x，y是给定的，求最小的n，比如求n个1能被3整除，输出n=3表示111能被3整除。如果是9个1被9整除就会int溢出。
	* 两个数都对m取模，余数相同的话，那么这两个数字同余。思路就是，每次取模之后，不用temp*10+x对m取模，而是用temp上次对m取模得到的余数乘10+x 然后对m取模。。。反正就是两个数对m取模得到的数相同的话，那么这两个数等价。要把所有的数字映射到1-m之间？？
* 真假村子：A村人说假话，B村人说真话，怎么问一个人才知道自己是不是在A村
	* 不和你同一个村子的人说这是A村：假设自己是在A，如果他是A的，那么他会说不是，如果他是B的他会说不是（假话），同理自己不在A他们会说是。本质是一个异或问题。
* 坐标计算一个点是否在一堆点中间？
	* Cn3解法：查找所有三角形，判断是不是在三角形内部
		* 三角形角度判断法：三角形与该点连线的三个夹角都不能大于180，用cos、sin求解，问题是如何表示正确表示三个角，而不是第三个角是前两个角的和。
		* 三角形面积判断法：该点与三个顶点相连构成的三个小三角形面积与外三角形面积相比，如果原面积与三个小面积之差绝对值接近于0则在内部。
	* Cn2解法：将该点相对移动到原点，连接所有线段，必经过所有4个象限才能围住，问题是如何判断一条线段经过哪几个象限，如不同象限11,12,13,14的四个线段经过哪几个象限
* 24点问题：四个数如8833,7744
	* 选取两个数处理得到一个数，将该数和其余两个数进行递归处理，每次处理两个数。既然是递归，就应该能想到用栈，因为递归本身就栈实现的，那么就可以全入栈，不断出栈处理。

## 动态规划 Dynamic Programming
---

* 分治、贪心、动态规划、递推：
	* 每个阶段只有**一个状态**-> **递推**；
	* 每个阶段的**最优状态**都是由**上一个阶段**的最优状态得到的-> **贪心**； 
	* 每个阶段的**最优状态**是由**之前所有阶段**的状态的组合得到的-> **搜索**；
	* 每个阶段的**最优状态**可以从之前某个阶段的某个或某些状态直接得到而**不管**之前这个状态是如何得到的-> **动态规划**。 （动态规划）

* 其实动态规划中的最优状态的说法容易产生误导，以为只需要计算最优状态就好，LIS问题确实如此，转移时只用到了每个阶段“选”的状态。但实际上有的问题往往需要对每个阶段的所有状态都算出一个最优值，然后根据这些最优值再来找最优状态。比如背包问题就需要对前i个包（阶段）容量为j时（状态）计算出最大价值。然后在最后一个阶段中的所有状态种找到最优值。
* 贪心
	* 贪心具有局部最优特点，贪心策略是由上一步的最优解推导下一步的最优解,而上一步之前的最优解则不作保留。
* 搜索：
	* 深度搜索和广度搜索。 
* 动态规划
	* 全局最优解中一定包含某个局部最优解,但不一定包含前一个局部最优解,因此需要记录之前的所有最优解
	* 动态规划的关键是状态转移方程,即如何由以求出的局部最优解来推导全局最优解
	* 边界条件：即最简单的,可以直接得出的局部最优解

* 几者关系[参考文章](http://blog.csdn.net/zccz14/article/details/51288079)
	* 搜索(Searching)，动态规划(DP, Dynamic Programming)，贪心算法(GA, Greedy Algorithm)，至于什么回溯法（Backtracking）只能是搜索的方向问题。
	* 从范畴上来看：贪心 < DP < 搜索。 所有的贪心算法问题都能用DP求解，DP又能用搜索搞定，反之不成立。

### 背包问题
---
* 问题描述：用一个承受重量m的背包装货物，每个货物不同的重量weight和价值value，求解可以装货的最大价值。这里我们讨论0-1背包问题。
* 问题解析：背包问题属于NP问题，也是典型的动态规划问题。每个货物至多装一个就是0-1背包。每个货物可以装count(i)个就是有界背包。每个货物可以装无限多个就是完全背包问题，无界背包问题。
* 状态定义与状态转移方程：
	* 状态定义为dp[i][j]，表示前i个货物总重不超过j的情况下所含有的最大价值。
	* 状态转移过程为：前i-1个已经确定装不装之后，当前货物装或者不装两种情况使用j所能对应的最大价值。
	* 状态转移方程为 dp[i][j] = max(dp[i-1][j] ,value[i] + dp[i-1][j-weight[i]] )。
* 每决定当前这个装不装就是两个转移路径，对应着两个价值。

```bash
	/**
	 * 使用动态规划求解背包问题
	 * 
	 * @param m 要求的容量是m
	 * @param w weight
	 * @param v value
	 */
	public static void solution(int m, int[] w, int[] v) {
		System.out.println(Arrays.toString(w));
		System.out.println(Arrays.toString(v));
		int n = w.length;
		int[][] dp = new int[n + 1][m + 1];
		// 初始化，数组元素相当于对象的成员变量，所以默认会自动初始化
		// 进行dp推算
		for (int i = 1; i <= n; i++) {
			for (int j = 1; j <= m; j++) {
				if (j >= w[i-1]) { // 如果能够装下当前这个，就是转移方程：max（添加、非添加时对应的状态）
					dp[i][j] = Math.max(dp[i - 1][j], dp[i - 1][j - w[i - 1]] + v[i-1]);
				}else{
					// 装不当前这个，就是前i-1的value
					dp[i][j] = dp[i-1][j]; 
				}
			}
		}
		System.out.println(dp[n][m]);

	}

```

### 最长上升子序列
* 给定一个数列，长度为N，求这个数列的最长上升（递增）子数列（LIS）的长度.以1 7 2 8 3 4为例。这个数列的最长递增子数列是 1 2 3 4，长度为4；次长的长度为3， 包括 1 7 8; 1 2 3 等.
* 动态规划：状态定义+转移方程。设Fk为：以数列中第k项结尾的最长递增子序列（LIS）的长度。状态转移方程：Fk=max(Fi+1 where Ai<Ak)，就是说k的前面所有的数为结尾时，如果和k能组成LIS就比较最大值。



##  拼多多笔试 20170721
* 给定M乘N的矩阵，每行都是多个0和多个1(0全部在前面)，要求用时间复杂度O(M+N)、空间复杂度O(1)的算法，输出1个数最多的行号(可能多个)？  
	* 关键要求出每行中1最多几个，然后用O(M)遍历各行输出。怎么求这个个数呢？如果每行统计要O(MN)。
	* 要求O(M+N)，想到典型的**对角线问题**：横竖二维间隔使用（自己命名的）。
	* 本题具体可以：从第一行右侧向左遇到0记录下标，并直接跳到对应的下一行的位置，如果是0则向下，遇1则向左，如此反复，就可以记录下每行的1最多有k个(O(M+N)的时间)。然后用O(M)扫遍各行是不是 `a[m][k]>0` 即可(O(M)小于O(M+N))。  
	* 从左上向右下进行对角线解法有什么问题？ 如果第一行1比第二行少还要在第二行向左退。产生原因还是因为统计的是1的个数不是0的个数。
* 判断一个表达式的括号用法是不是合法：只关注()、[]、{}的判断，{}里面可以是{}、[]、()，[]里面只能是()，()里面不能再有括号？  
	* 用一个栈，对6种括号分别进行判断及出入栈操作，最终栈为空则合法。
* 求一个房间的面积：在(0,0)开始前后左右可以走动，每个格子的面积为1，求整个房间的面积？  
	* 目标只需遍历所有结点即可，关键在于不重复。格子设计为Node，Node含有x, y坐标（用于在全局列表中判断是否访问过），还有4个相邻的Node。写个函数对一个节点进行操作：分别向前后左右访问，可以访问就判定是否加入全局列表（全局列表根据Node的x，y判定是否访问过），递归访问所有的node。最终得到整个房间的全局列表，列表大小就是面积。
* n个线段的并的总长度问题：n个线段，每个都是（起点值，终点值），问在数轴上覆盖的总长度(就是说线段之间相同的部分不能重复算入总长)？
	* 典型的**多线段合并问题**：基于多个线段求一个目标（自己命名的）。 多线段合并问题的核心思想：为各个线段的元素添加有用信息(比如是不是起止点，属于哪条线段等)，然后合并到数轴上，然后扫描数轴，求解目标。 数轴常用一个公用的集合、数组等。
	*  多线段合并问题使用画图的方式最能直观解决，然后就是考虑各个点该添加什么样的信息（比如起止点标志），然后求解目标。
	* 本题具体可以：
* n个线段的交的总长度问题：n个线段求所有线段都包括的区间的长度。
	* 多线段合并问题：各个线段的起止点都加到一个公用列表中，加的时候需要为点添加信息（这里用改点属于哪几个线段构成一个集合信息），然后扫描数轴列表，找出属于那些属于所有线段的点，最大和最小点之间就是所求的交的长度。
* n个整数数组，求一个最短区间[s,t]，使得各个数组都至少有一个元素值是在这个区间中的。比如`[1,3,5], [0,2,8], [3,7,10], [-100,10]` 求出[2,10].
	* 类似于**多线段合并问题**，主要思路也就是画图，考虑结点的携带信息，通过合并到一个数轴，扫描求解目标区间。
	* 思路：一上来先用多线段求交的方法求出交区间`[3,5]`（参见上一题），但是求交只能保证结果被各个区间覆盖，但没法保证结果包含数组的点。这里的`[3,5]`就不包括` [0,2,8], [-1,100]` 里面的点，所以还需要向外扩展区间。拓展的方向怎么选择呢？那就涉及到了贪心。
	* 个人解法：在多线段求交的基础上，需要添加更多信息。一个节点需要携带（所属线段的集合，所在数组的集合）。“所属线段”是为了求交，没有交集是谈不上[s,t]区间的。“所在数组的集合”是为了进行交集扩展，确定扩展后的区间包含了各个数组的点。在交集`[3,5]`的基础上，再数轴上左右拓展：先统计[3,5]中的点的所在区数组的集合为（0,2）（也就是第一个和第三个数组，目标是（0,1,2,3））。然后向左右各拓展一个点，属于新的数组的点才有用：3左边的2是有用的，属于（1），5右边7没用，8属于（1）有用。所以对2和8进行贪心：拓展到2增加了3-2=1，拓展到8增加了8-5=3，那就选2。现在结果变成了[2,5]，涵盖的区间有（0,1,2）。接着向左右拓展，刚才拓展到2和8，现在拓展到-100（1无用）和10，比较发现10只需拓展10-5=5，涵盖区间变成（0,1,2,3），所以结果就是[2,10].

* 线程和进程的区别？
	* CPU太快，只有寄存器稍微能追上他的脚步，RAM和其他总线设备太慢。CPU通过分时调度轮流执行各个任务，轮流过程：加载程序A的上下文，然后开始执行A，完成或者CPU分时结束时保存A的上下文；调入程序B的程序上下文，执行B，完成或分时结束时保存程序B的上下文。
	* 进程就是包括上下文切换的程序执行时间总和 = CPU加载上下文+CPU执行+CPU保存上下文。 进程的颗粒度太大，每次都有上下的调入，保存，调出。
	* 线程是共享了进程的上下文环境的更为细小的CPU时间段。比如一个程序A有多个分支和多个程序段，那么执行时就可能变成：程序A得到CPU => CPU加载上下文，开始执行程序A的a小段，然后执行A的b小段，然后再执行A的c小段，最后CPU保存A的上下文。这里a，b，c的执行是共享了A的上下文，CPU在执行的时候没有进行上下文切换的。这里的a，b，c就是线程，也就是说线程是共享了进程的上下文环境的更为细小的CPU时间段。
	* 进程和线程都是一个时间段的描述，是CPU工作时间段的描述，不过是颗粒大小不同。
	* 进程有独立的地址空间，进程崩溃后，在保护模式下不会对其它进程产生影响，而线程只是一个进程中的不同执行路径。线程有自己的堆栈和局部变量，但线程之间没有单独的地址空间，一个线程死掉就等于整个进程死掉，所以多进程的程序要比多线程的程序健壮，但在进程切换时，耗费资源较大，效率要差一些。但对于一些要求同时进行并且又要共享某些变量的并发操作，只能用线程，不能用进程。
* 抢占式进程调度和非抢占式进程调度的区别？ 
	* 非抢占式和抢占式进程调度都属于最高优先级进程调度，他们总是调度就绪队列中优先级最高的进程。
	* 非抢占式进程调度算法：当就绪队列中某进程的最高优先级高于正在处理器中运行的进程的最高优先级时，并不会让正在运行的进程退出处理器，而是将高优先数的排在就绪队列的首部。
	* 抢占式进程调度算法：拥有高优先数的进程会抢占处理器，让正在处理的进程处于就绪队列。
* 进程间通讯方式有哪些？线程间通讯方式有哪些？ 
	* 进程间通信方式：
		1. 无名管道( pipe )：管道是一种半双工的通信方式，数据只能单向流动，而且只能在具有亲缘关系的进程间使用。进程的亲缘关系通常是指父子进程关系。
		2. 高级管道(popen)：将另一个程序当做一个新的进程在当前程序进程中启动，则它算是当前程序的子进程，这种方式我们成为高级管道方式。
		3. 有名管道 (named pipe) ： 有名管道也是半双工的通信方式，但是它允许无亲缘关系进程间的通信。
		4. 消息队列( message queue ) ： 消息队列是由消息的链表，存放在内核中并由消息队列标识符标识。消息队列克服了信号传递信息少、管道只能承载无格式字节流以及缓冲区大小受限等缺点。
		5. 信号量( semophore ) ： 信号量是一个计数器，可以用来控制多个进程对共享资源的访问。它常作为一种锁机制，防止某进程正在访问共享资源时，其他进程也访问该资源。因此，主要作为进程间以及同一进程内不同线程之间的同步手段。
		6. 信号 ( signal ) ： 信号是一种比较复杂的通信方式，用于通知接收进程某个事件已经发生。
		7. 共享内存( shared memory ) ：共享内存就是映射一段能被其他进程所访问的内存，这段共享内存由一个进程创建，但多个进程都可以访问。共享内存是最快的 IPC 方式，它是针对其他进程间通信方式运行效率低而专门设计的。它往往与其他通信机制，如信号量，配合使用，来实现进程间的同步和通信。
		8. 套接字( socket ) ： 套解口也是一种进程间通信机制，与其他通信机制不同的是，它可用于不同机器间的进程通信。
	* 线程间通信方式：
		1. 锁机制
			* 互斥锁：提供了以排它方式阻止数据结构被并发修改的方法。
			* 读写锁：允许多个线程同时读共享数据，而对写操作互斥。
			* 条件变量：可以以原子的方式阻塞进程，直到某个特定条件为真为止。对条件测试是在互斥锁的保护下进行的。条件变量始终与互斥锁一起使用。
		2. 信号量机制：包括无名线程信号量与有名线程信号量
		3. 信号机制：类似于进程间的信号处理，发送方通知接收方某个事情的发生。
	* 线程间通信的主要目的是用于线程同步，所以没有像进程通信中用于数据交换的通信机制。

* 如何实现一个线程安全的hash map？ 


## Java 源码

### 结构

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
	* jdk8中源码如下，Collections.synchronizedMap 返回一个将map封装之后的同步类，该类内部使用synchronized (mutex)方法保证线程安全。


```
Map<String, String> synchronizedHashMap = Collections.synchronizedMap (new HashMap<String, String>());
// jdk8 源码如下
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

### 线程安全

* synchronized方法,是一种悲观锁.在进入之前需要获得锁,确保独享当前对象,然后做相应的修改/读取.
