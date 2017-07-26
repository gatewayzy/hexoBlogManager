---
title: Python
comments: true
date: 2017-01-10 13:58:16
updated: 2017-01-10 13:58:56
categories: Language
tags:
- Python
---

**说明：**Python的使用。
<!-- more -->


参考文章：
* [清华大学开源软件镜像站的Anaconda 镜像](https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/)
* [非官方python库 Unofficial Windows Binaries for Python Extension Packages](http://www.lfd.uci.edu/~gohlke/pythonlibs/)

## Python介绍
---
* 属于解释型语言
* 常用包：scipy（science，科学计算）、numpy（number，数学运算）、sklearn（SciKitLearn，机器学习算法等）、深度学习数据流架构（Theano、TensorFlow）、深度学习架构(Keras、Caffe等)

## 安装
---
### 安装python
---

* 纯编辑器 不推荐
	* 下载并安装python，如果不在path中，就添加path值。打开cmd，运行python，进入python运行状态。如`print("hello")`。
	* 使用各种文本编辑器写好python代码文件，运行`python a.py`或者用其他方式调用python运行即可。

* IDE
	* 推荐先装anaconda，再装pycharm或者wingIDE。
	* 这样可以直接不用独立安装Python，并且anaconda自带的Python会提供pip管理，也可以为pycharm提供pip管理。
	* 如果anaconda安装的库中存在问题，只需pip安装特定的包即可。
* 安装pycharm for linux
	* 下载tar.gz，进行tar -zxvf，进入bin运行./pycharm.sh即可。
	*  设置Ubuntu下Pycharm的快捷启动方式
	  在Ubuntu下，每次都要找到 pycharm.sh所在的文件夹，执行./pycharm.sh，非常麻烦。最好能创建一个快捷方式。Ubuntu的快捷方式都放在/usr/share/applications，首先在该目录下创建一个Pycharm.desktop。运行 sudo gedit /usr/share/applications/Pycharm.desktop。然后输入以下内容，注意Exec和Icon需要找到正确的路径，即可在启动栏中找到。

```
[Desktop Entry]
Type=Application
Name=Pycharm
GenericName=Pycharm3
Comment=Pycharm3:The Python IDE
Exec="/XXX/pycharm-community-3.4.1/bin/pycharm.sh" %f
Icon=/XXX/pycharm-community-3.4.1/bin/pycharm.png
Terminal=pycharm
Categories=Pycharm;
```

* python版本
	* linux下面的python可能还有3.5、3.5m这种版本，表示：`--with-pydebug (flag: d)--with-pymalloc (flag: m)--with-wide-unicode (flag: u)`
	

### 安装python插件
---
* pip管理包
	* pip list  列出所有包，
	* pip search aaa  模糊查找一个包
	* pip install | uninstall aaa  安装|卸载
	* pip install numpy==1.11.3  指定安装版本
	* pip install aaa.whl  安装wheel文件包，可从镜像站点下载
	* pip install -upgrade tensorflow  更新软件包
	* pip uninstall tensorflow  卸载软件包

##  语法
---
### 基础语法
---
* python -v 查看系统环境变量的python的版本，相应的python可以用./python运行。
* pip install/list/search/install --upgrade /[package]等或者./pip使用相对路径下的pip。
* 编码：默认情况下，Python 3 源码文件以 UTF-8 编码，所有字符串都是 unicode 字符串，也可指定 `# -*- coding: utf-8 -*-`
* 标识符：大小写敏感、开头是字母或下划线、非开头可用字母数字和下划线
* 保留字：在keyword.kwlist中有列出，如'False','None','or'等等
* 注释：单行注释用#开头，多行注释用'''注释且支持换行，也可用三个双引号'''
* 行与缩进：必须相应对齐，因为没有{}，只要是对齐的，可缩进多个。句尾不要写分号，用分号的话，后面后面可以写另一句代码。
* 多行语句：用\连接起来多行，如a+\换行b，但是如果是[]、{}、()内部的多行，不需要使用\表示换行。
* 字符串：
	* py中单引号与双引号等价。
	* 三引号或三个双引号可以指定一个多行字符串，用于定义字符串或者当做注释。
	* 转移符是'\'
	* 自然字符串，用r或者R拼接，如r"此时\n不会换行而是显示为\n"
	* 处理Unicode字符串，用u或U拼接，如U"这是一个Unicode的字符串"
	* 字符串是不可变的，只会新建
	* 字符串按照字面意义进行级联成字符串
* 等待用户输入：n=input('输入n=');print(n)
* 输出语句：print默认是之后会换行，添加end参数，如print("string", end='随便')设置结尾不是\n而是指定的内容。
* 删除引用对象用del，如del a,b
* _常用于交互模式下上一个运算结果。

### 基本数据类型
---
* type(A)返回A的类型，不会认为子类是父类的类型，isinstance(A(),A)判断是否是实例，会认为子类是父类的类型。
* **python有6个标准的数据类型：Number数字、String字符串、List列表、Tuple元组、Set集合、Dictionary字典。**
* 变量定义不要指定类型，支持多变量统一定义和分别定义，如a=b=c=1;aa,bb,cc=1,0.3,"string"。变量的类型可通过赋值操作改变。
* 类型转换：使用int(s),float(s),tuple(s),set(s),str(s),complex(a,b),dict(s),chr(x),unichar(x)等等进行转换
* 遍历技巧：
	* 字典遍历可用 for k, v in dictA.items()
	* 序列遍历可用 for i, v in enumerate(listA)
	* 同时遍历多个序列 for q, a in zip(questions, answers)
	* 反向遍历一个序列 for i in reversed(listA)
	* 顺序遍历一个序列 for f in sorted(set(listA))

#### Number 数字类型包括4种：

* int整数：py3只有int整数，对应python2的long int。
* float浮点数：除法/总是返回浮点数
* bool：py2中没有bool，但是py3和py2一样都是用0和1表示False，True，可以和数字相加
* complex复数：用a+bj或者a+bJ表示，其中a，b都是float。支持complex(a,b)构造。
* `+-*/`，其中/是浮点除法，//才是整除，%取余，`**`是指数运算
* 常用函数
	* 数学函数 `abs() ceil exp floor log log10 max min pow sqrt round` 等等，在import math中都能找到math.round(0.55,1)，也有一些其他的封装库，如numpy。
	* 随机数函数 choice(seq)随机选取队列一个元素；random()随机生成一个[0,1)间实数；shuffle(list)将序列打乱；uniform(x,y)随机生成[x,y]间一个实数等等。
	* 三角函数 `sin() cos tan acos degrees radians`等等 print(math.cos(0.5* math.pi));print(math.cos(0))
	* 数学常量 math.pi math.e

#### String 字符串

* 使用单引号或双引号，使用\转义。a='string\n'
* 使用变量[头下标: 尾下标]进行截取，从左向右是0到最后，或者从最后向头部索引是-1,-2...。截取返回新字符串
* 注意python所有的[i:j]都是包括左边但是不包括右边的。
* 不可以进行str[0]='a'这种尝试改变字符串的操作。py没有char类型，每一个字符只能用字符串表示。
* +进行字符串拼接，使用str*2表示重复。
* 常用函数
	* 转义用\，如输出`\' \" \n \t \\`等
	* 字符串运算符支持`+ * [:] in  not in  r/R`等
	* 格式化输出，支持的格式有`%c %s %d %f %e`等等，并且支持%与字母之间的辅助指令`数字表示宽度、-表示左对齐、+是整数显示+、m.nb表示最小总宽度和小数点位数`等等。如`print("%%我叫%s今年%+d岁!" % ('小明', 10));print('%-8s %-8s %-8s' % ('name', 'id', 'salary'));print('%-8s %-+8d %5.2f' % ('aa', 10, 12.123));print('%-8s %-+8d %5.2f' % ('xxxx', -10, 58.91))` 就可以输出整数带+，宽度统一且都左对齐的格式。
	* python2中使用ASCII(char8)存string，python3使用16位的Unicode存储string
	* 常用str内置函数，包括`len() startwith join lower isspace max encode`等等。如`a='aa Bb Cc DD';print('%s %s %s'% (str.islower(a), str.lower(a), a.split(' ',2)))`

#### List 列表 []

* 使用[]和,进行定义，可包含不同类型，如a=[1, 2.0, 0.3j, "test"]，支持嵌套a=[[1,2],'a'] 
* 使用变量[头下标: 尾下标]进行截取，从左向右是0到最后，或者从最后向头部索引是-1,-2...截取返回新列表
* list元素可以修改，如str[0]='a'
* +进行列表连接，使用*进行重复
* 常用函数
	* 使用del list[i] 删除一个元素
	* 常用`len() max min list`和`list.append() extend index sort pop remove clear copy`等等。
* 特点：
	* 元素可变，可用作堆栈，可用作队列（但是基本是ArrayList，插入和删除不高效）
	* 列表推导式：方便地从序列创建列表，`b=[3*x for x in vec]` `[[x, x**2] for x in vec]`等构建复杂的列表
	* 嵌套列表解析：`a=[[row[i] for row in matrix] for i in range(4)]`等等

#### Tuple 元组 ()

* 使用()和,进行定义，可包含不同类型，如ta = ('a', 1,"STR",0.1J)
* 元组含有0或者1个元素时构建方法为empt = ();onet = ('a',)#需要逗号
* 使用变量[头下标: 尾下标]进行截取，从左向右是0到最后，或者从最后向头部索引是-1,-2...。截取返回新元组
* 元组与list的区别在于元组的元素不能修改，不能使用ta[0] = 0。但是tuple支持包含可变的对象，如list = [1,2];tc = (list, 'c'); print(tc)
* +进行列表连接，使用*进行重复
* string、list和tuple都属于sequence（序列）
* 常用函数
	* `len() max min tuple(seq)将列表转换为元组`等。

#### Set 集合 ({}) set()

* set无序不重复的序列，常用于检测关系或自动删除重复。
* set无序，不支持使用set[索引]的方式获取元素
* 使用({})和,定义如，或者用set()创建空set或者a=set(一个元素)，如sa = ({1, 'a', 3, 3, 3, 30})会自动删除重复元素。
* set的运算用|、-、&、^表示集合的并/减/交/异或
* 集合支持集合推导式：`a = {x for x in 'abracadabra' if x not in 'abc'}`

#### Dictionary 字典 {}

* 使用{}和,定义，空的字典是a={}，如
* dictionary是无序的对象集合，list是有序的对象集合，dictionary使用key:value进行存取
* 字典是无序的集合，不支持dictionary[索引位置]方式，支持dict[keyName]的方式。
* key必须是不可变类型，key唯一，可以是字符串、数字、元组等，不能是list。
* 可以用dict()构造函数创建dict，如db = dict([('name',1),(3,2)])
* 常用函数
	* 常用dict的函数`str() len type`等，常用的`a.clear keys pop get items`等函数。
* 特点
	* 字典支持字典推导式`a={x: x**2 for x in (2, 4, 6)}`


### 运算符
---
* 算数运算符 `=-*/、 //、 %、 ** `
* 比较运算符 `==、 !=、 >、 >=、 <=`
* 赋值运算符 `=、 +=、 -=、 *=、 /=、 %=、 //=`
* 位运算符 `&、 |、 ^异或、 ~取反、 <<左移、 >>右移 `
* 逻辑运算 `and、 or、 not `
* 成员运算 `in、 not in`
* 身份运算 `is、 is not`
* 运算优先级 略

###  控制语句
---
#### 条件控制
---
* if else elif，没有switch

#### 循环控制
---
* while else 无限循环可以使用ctrl c中断循环
* for else  支持遍历序列，如列表、字符串等
* range() 函数，支持 for i,item in range(start, end, step)等，范围包括start不包括end
* 使用continue、break控制循环
* pass 只用于占位，，没有任何实际影响

### 迭代器与生成器
---
* 迭代用于遍历集合元素，只能向前不会后退
	* 迭代器的基本方法：iter() 和 next() `a = [1,'a',1.j];it = iter(a);print (next(it))`
* 生成器是一个使用了yield函数的特殊的函数，用于返回迭代器，且只能用于迭代。
	* 每次运行到yield函数时，会暂停并保存当前所有的运行信息，返回yield值，下一次next()从当前位置继续运行 `def get(n): ... yield a;` `f=get(5);print(next(f))`

### 函数
---
* def 函数名 (参数列表): 函数体
	* 使用return返回给调用方，不指明return则return None。
	* 在函数定义中参数指明如end=1可以设置默认值，否则就是必须参数
	* 调用函数，如果不指明形参名称如end=1这种关键词参数，则需要对应参数位置。
	* 不定长参数：加*的参数名就是不定长参数，调用时会把所有未命名的参数（除去对应的位置参数）都放到一个对应不定长参数的元组中。
* python的参数都是引用传递，调用中改变时会影响调用方。
* 匿名函数：使用lambda而不用def，主体只包含一个语句，且只能访问调用方的参数值，如`sumF = lambda a, b, c: a + b + c;  print(sumF(1, 2, 3))`
* 变量作用域：函数内的局部变量，函数外的全局变量。不声明的话，内部变量会使用全局变量，但是函数内部的修改不会改变全局变量，除非内部使用时指定global varName

### 模块
---
* 模块引入
	* import sys进行模块引入，sys.argv显示命令行参数，sys.path显示python路径等等。如果是import a.b.c 就要使用a.b.c.xx比较长。这种形式除了最后一项都必须是包，最后一项可以是模块或者包，但不可以是类、函数或者变量。
	* from a.b import c,d # 引入指定模块以访问c、d，就可以使用c.xx。该形式可以引入子包（子模块）或者包里面定义的其他名称，如函数、类、变量等。引入的对象先会被当做包进行导入，如果没找到就会尝试作为模块进行导入，还找不到就抛出异常
	* from package1.p2 import * # 引入所有项目。如果包文件的`__init__.py`中存在`__all__ `列表变量，就将列表中所有名字作为包内容导入，所以需要手动更新该变量值。如`__all__ = ["echo", "surround", "reverse"]`表示只导入3个模块。如果没有该变量，就会默认导入p2里面的所有内容以及`__init__.py`中的初始化内容。这种引入方式使用不当可能会产生覆盖。
	* 推荐使用from package1.p2 import module1 as m1,module2 as m2 这种方式。from支持.和..相对路径，但是主模块永远是`__main__`。
* 不同模块的全局变量存放在不同空间符号表，之间不会相互影响
* 模块被引入时，默认会运行其主程序。所以需要注意，这些程序在被import时就会运行，常用于初始化操作等。如果不想在被import时就运行可以对模块设置相应的属性可以进行限定。
	* `_name_`属性，`if __name__ == '__main__'`可以限定只在自身运行时才会调用对应的代码，避免放在外部的话在被import时就运行。
* dir()函数可找到模块内定义的所有名称，如dir()得到当前模块的所有名称， dir(sys)获得sys模块内的名称。
* 每个模块都有内置的属性，如`print(__doc__)`就可以输出当前模块开头的'''注释说明'''。
* 标准模块
* 包管理使用‘点模块名称’
	* 包的目录包含 `__init__.py` 文件才会被认作是一个包，主要是为了避免一些滥俗的名字（比如叫做 string）不小心的影响搜索路径中的有效模块。最简单的情况，放一个空的 `__init__.py`就可以了。当然这个文件中也可以包含一些初始化代码或者为`__all__`变量赋值。

###  输入和输出
---
#### 输出格式美化
----
* 输出值的方式：表达式语句和 print() 函数，或者文件对象的 write()，如引用sys.stdout。
* str() 
	* str.format() 函数来格式化输出值，如`print('{}网址： "{}!"'.format('菜鸟教程', 'www.runoob.com'))`等等。
	* % 操作符也可以实现字符串格式化,但是属于旧式方法，最终会被 str.format() 替换
* repr() 
	* 可以转义特殊字符，如'\n'输出为'\n'而不换行
	* 提供rjust()、ljust、center和zfill等方法，分别进行空格填充以靠右左中、用0填充数字等。`print(repr(x*x*x).rjust(4))`

#### 读取键盘输入 
----
* a=input('请输入a=',a)

#### 读写文件
----
* open打开文件r，w，a，b等模式，对文件句柄进行操作
* 包括f.read() readline() readlines() f.write() f.close()等等，如`f = open("/tmp/foo.txt", "r");print(f.readline())`
* 注意关闭文件。


#### pickle模块
----
* pickle模块实现了基本的数据序列和反序列化。通过pickle模块的序列化操作我们能够将程序中运行的对象信息保存到文件中去，永久存储。通过pickle模块的反序列化操作，我们能够从文件中创建上一次程序保存的对象。
* pickle操作类文件对象，import pickle，注意关闭文件
	* `obj=...;file = open('data.pkl', 'wb');pickle.dump(obj, file, protocol)` protocol有0/-1等，为可选参数。
	*  `file = open('data.pkl', 'rb');x = pickle.load(file)` 可以引入pprint.pprint(x)

### 错误和异常
---
* try: except ExceptName: else: finally:
	* except 可以用except (RuntimeError, TypeError, NameError):或者多个except，但是至多一个会被执行。
	* else会在try没有异常时执行
	* 不管有没有异常，在最后都会执行finally的清理行为。
* raise 放在except里面将异常抛给上一级`except NameError:  raise`。
* 预定义的清理行为：无论系统是否成功，一旦不需要它就会自动清理。
	* `for line in open("myfile.txt"): print(line, end="")`
	* 关键词with可以保证文件之类的对象使用完后会自动被关闭或清理。`with open("myfile.txt") as f: for line in f:print(line, end="")`

###  面向对象
---
#### 类的定义

* python本身就是面向对象的。类。类变量：类中函数体之外的变量，实例化中公用。实例变量：实例化中定义的变量。数据成员：类变量或者实例变量等数据。方法重写：子类可以覆盖父类的方法override。
* 类定义：`class ClassName: 变量、方法等`。支持属性引用和实例化，
* 如果子类方法名与父类重名，会自动重写。如果子类中找不到方法名，会从父类中查找相应的方法。
* 继承支持多类继承`class Student(MyClass, Human):`，如果子类找不到方法，而且是多类继承，会根据该括号中顺序从左向右的父类中搜索。

#### 类的属性与方法

* 类的私有属性用两个下划线开头的属性，可保证在类外部无法直接通过a.name等方式获取
* 类的方法定义第一个参数是额外的用于指代实例本身，惯用self。如果是私有方法，将方法名也是以两个下划线开头，该方法只能在类的内部调用。
* 类的专有方法主要如下，也都是支持重写的。

```
__init__ : 构造函数，在生成对象时调用
__del__ : 析构函数，释放对象时使用
__repr__ : 打印，转换
__setitem__ : 按照索引赋值
__getitem__: 按照索引获取值
__len__: 获得长度
__cmp__: 比较运算
__call__: 函数调用
__add__: 加运算
__sub__: 减运算
__mul__: 乘运算
__div__: 除运算
__mod__: 求余运算
__pow__: 乘方
```

```
class MyClass():
    '''
    定义自己的类。类的方法需要第一个位置作为额外参数，惯例是叫self，指代类的实例
    '''
    country = 'china'
    __weight = '60kg'  # 双下划线开始的属性不能在类外部直接访问

    def __init__(self, name, age):
        # init是初始化方法（构造方法）
        self.name = name
        self.age = age

    def printSelf(self):
        print(self)
        print(self.__class__)

    def setNo(self, no):
        self.no = no

    def show(self):
        print("country:{} name:{} age:{} no:{} weight:{}".format(self.country, self.name, self.age, self.no,
                                                                 self.__weight))


class human():
    __age = 23
    sex = 'unknown'

    def __init__(self, sex):
        self.sex = sex

    def showHuman(self):
        print("age:{} sex:{}".format(self.__age, self.sex))


class student(MyClass, human):
    '''
    支持多继承，如果方法在子类没找到，而多个父类有同名方法，会依据括号中顺序从左向右搜索
    '''
    stuID = -1
    __score = -1000

    def __init__(self, stuId, name, age, sex, no):
        # self.stuID = stuId
        MyClass.__init__(self, name, age)
        MyClass.setNo(self, no)
        human.__init__(self, sex)

    def showStudent(self):
        print('stuID:{} name:{} age:{} sex:{}'.format(self.stuID, self.name, self.age, self.sex))
        self.__getscore()
        return self.name, self.age

    def __getscore(self):
        print('得分：%d' % self.__score)


def class_basic():
    tom = MyClass('tom', 10)
    tom.setNo(0)
    tom.show()
    tom.printSelf()
    # print(tom.__weight)
    jack = human('nan')
    jack.showHuman()

    will = student(100, 'will', 20, 'nv', 22)
    will.show()
    will.showHuman()
    will.showStudent()
    name, age = will.showStudent()
    print('return 返回的值为：{}, {}'.format(name, age))
    # will.__getscore()


def main_class():
    print('\n\n>>>>>> class >>>>>>')
    class_basic()
    print('<<<<<< class <<<<<<\n')

```


###  多线程
---
* python3常用两个线程模块；_thread模块属于兼容python2的thread模块；threading是推荐使用的模块。
* 兼容的_thread使用的是`_thread.start_new_thread ( function, args[, kwargs] )`
	* function 表示线程函数
	* args 表示传递给函数的线程，需要用tuple
	* kwargs 可选参数
* threading模块支持多种函数，包括start()、join()等。
	* threading模块实现多线程, 但是模块并没有提供暂停, 恢复和停止线程的方法, 一旦线程对象调用start方法后, 只能等到对应的方法函数运行完毕. 也就是说一旦start后, 线程就属于失控状态. 不过, 我们可以自己实现这些. 一般的方法就是循环地判断一个标志位, 一旦标志位到达到预定的值, 就退出循环. 这样就能做到退出线程了. 但暂停和恢复线程就有点难了, 我一直也不清除有什么好的方法, 直到我看到threading中Event对象的wait方法的描述时.
* 同步锁：使用 `threadLock = threading.Lock(); threadLock.acquire(); {代码块}threadLock.release()`

```
import _thread
import threading
import time

# 为线程定义一个函数
def print_time(threadName, delay, counter):
    count = 0
    while count < counter:
        time.sleep(delay)
        count += 1
        print("%s: %s : %d" % (threadName, time.ctime(time.time()), count))

def thread_basic():
    # 创建两个线程
    try:
        _thread.start_new_thread(print_time, ("Thread-1", 1, 5))
        _thread.start_new_thread(print_time, ("Thread-2", 2, 5))
    except:
        print("Error: 无法启动线程")
    # 如果不使用线程或者循环，而直接运行到程序退出的话会导致新线程退出
    time.sleep(20)  # 30秒，给子线程一些运行时间
    # while 1:
    #    pass

def main_thread():
    print('\n\n>>>>>> _thread >>>>>>')
    thread_basic()
    print('开始Threading模块 >>>>>>')
    threading_basic()
    print('<<<<<< _thread <<<<<<\n')

exitFlag = True

def threading_basic():
    pass
    global exitFlag  # 默认使用全局变量，但是内部修改不会影响全局变量，真想改变全局变量就用global
    print(exitFlag)
    time.sleep(3)
    thread3 = myThread('mythread-3', 3, 10)
    thread3.start()  # start方法运行线程的run()
    exitFlag = True
    thread4 = myThread('mythread-4', 4, 7)
    thread4.start()
    if exitFlag:
        print('全局变量已经修改，在外围将强制退出线程：{}但是不知道线程如何退出'.format(thread4.threadName))

class myThread(threading.Thread):
    def __init__(self, threadName, delay, counter):
        threading.Thread.__init__(self)
        self.threadName = threadName
        self.delay = delay
        self.counter = counter
    def run(self):
        print("开始线程：%s" % self.threadName)
        print_time(self.threadName, self.delay, self.counter)
        print("线程结束：{}".format(self.threadName))
```


###  标准库概览
---
* import os 操作系统接口
* glob 文件通配符
* sys 命令行参数
* re 字符串正则匹配，re.match、search、sub等等
* math 数学，random 生成随机数
* datetime 日期和时间
* 数据压缩 zlib gzip等等

### 其他模块
---
* json模块 import json
	* `json_str = json.dumps(data);data2 = json.loads(json_str)`
	* `with open('data.json', 'w') as f: json.dump(data, f)`
	* `with open('data.json', 'r') as f: data = json.load(f)`

## pycharm使用
---
### 快捷键
---
* ctrl alt shift L 格式化代码
* ctrl F5 重新运行项目
* alt insert 新建
* 方法内部第一行输入'''并回车  自动生成代码接口说明
* 选中多行，按tab是统一添加tab
* 选中多行按shift tab是统一减少tab
* 抽取函数是ctrl alt m
* 更多快捷键可查看idea的说明

## 错误与解决
---
### init_dgesdd failed init 
* 问题描述：运行numpy的高维度数据运算的时候，提示该错误或者是MemoryError
* 解决方法：这是由于分配的内存不足，可以将pycharm的配置文件pycharm64.exe.vmoptions的参数调高，然后删除该python工程的idea工程描述文件，重新建立该工程即可。

### tensorflow提示没有debug.lib
* 问题描述：运行程序时提示没有debug.lib模块
* 原因分析：由于使用了anaconda的python和pip，自动安装的tensorflow没有相应的模块
* 解决方法：下载tf在github上或者官网的whl文件，使用./pip install的方式进行安装。