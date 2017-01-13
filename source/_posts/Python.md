---
title: Python
comments: true
date: 2017-01-12 13:58:56
updated: 2017-01-12 13:58:56
categories: 编程语言
tags:
- Python
---

**说明：**Python的使用。
<!-- more -->

## Python介绍
---
python在科学计算等方面非常重要。

## 安装
---

### Windows下安装使用

* 编辑器

下载并安装，如果不在path中，就添加path值。
打开cmd，运行python，进入python运行状态。如`print("hello")`。

* IDE
推荐先装anaconda，再装pycharm。这样可以直接不用独立安装Python，并且anaconda自带的Python会提供pip管理，也可以为pycharm提供pip管理。

* 参考文章：
	* [Anaconda使用总结](http://python.jobbole.com/86236/)
	* [清华大学开源软件镜像站的Anaconda 镜像](https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/)
	* [非官方python扩展库 Unofficial Windows Binaries for Python Extension Packages](http://www.lfd.uci.edu/~gohlke/pythonlibs/)

### 安装python插件
---
* 使用pip管理
	* pip list列出所有包，pip search aaa模糊查找一个包，pip install | uninstall aaa安装卸载等。
* 安装wheel包
	* 下载对应python版本和机器位数的wheel包，可以从镜像站点下载；
	* 运行pip install aaa.whl
## Anaconda使用
---


## pycharm使用
---

* 快捷键
	* ctrl alt shift L 格式化代码
	* ctrl F5 重新运行项目
	* alt insert 新建
	* 更多快捷键可查看idea的说明

* 错误解决：
	* init_dgesdd failed init 
		* 问题描述：运行numpy的高维度数据运算的时候，提示该错误或者是MemoryError
		* 解决方法：这是由于分配的内存不足，可以将pycharm的配置文件pycharm64.exe.vmoptions的参数调高，然后删除该python工程的idea工程描述文件，重新建立该工程即可。