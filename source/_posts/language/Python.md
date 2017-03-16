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
	* 推荐先装anaconda，再装pycharm。
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
* 略。

## Anaconda使用
---
###  设置国内镜像
---
* 如果需要安装很多packages，你会发现conda下载的速度经常很慢，因为Anaconda.org的服务器在国外。所幸的是，清华TUNA镜像源有Anaconda仓库的镜像，我们将其加入conda的配置即可。
* 执行完上述命令后，会生成~/.condarc(Linux/Mac)或C:UsersUSER_NAME.condarc文件，记录着我们对conda的配置，直接手动创建、编辑该文件是相同的效果。


```

# 添加Anaconda的TUNA镜像
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
# TUNA的help中镜像地址加有引号，需要去掉
 
# 设置搜索时显示通道地址
conda config --set show_channel_urls yes

```

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
* 解决方法：下载tf的whl文件，使用pip install的方式进行安装。