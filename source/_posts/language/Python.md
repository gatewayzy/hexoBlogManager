---
title: Python
comments: true
date: 2017-01-10 13:58:16
updated: 2017-01-10 13:58:56
categories: Language
tags:
- Python
- 编程语言
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
	* 

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
* 数据类型：4种数：整数、长整数、浮点数、复数
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

### 基本数据类型
---
* 



## pycharm使用
---
### 快捷键
---
* ctrl alt shift L 格式化代码
* ctrl F5 重新运行项目
* alt insert 新建
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