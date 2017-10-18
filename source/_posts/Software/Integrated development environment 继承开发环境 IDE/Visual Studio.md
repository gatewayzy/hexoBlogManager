---
title: Visual Studio
comments: true
date: 2017-01-12 13:58:09
updated: 2017-01-12 13:58:56
categories: Softwares
tags:
- IDE
---

**说明：**Visual Studio是Microsoft环境开发的一个针对Windows的IDE工具集，用于C++、C#、.NET等开发的最佳工具。
<!-- more -->



###  简介

* Visual Studio是Microsoft开发的一个针对Windows的IDE工具集。

###  支持语言
---
* vs支持包括C类语言、Basic类语言、Java类语言、FoxPro等其他语言。
	* C类语言包括visual C++、 visual c#
	* Basic类语言包括visual basic
	* Java类语言包括visual j++，后因java版权问题取消Java类语言。
	* 其他语言支持visual F#等。
* vs从vs6.0（vs98）之后推出.Net框架。
* .NET的通用语言框架机制（Common Language Runtime, CLR），其目的是在同一个项目中支持不同的语言所开发的组件。所有CLR支持的代码都会被解释成为CLR可执行的机器代码然后运行。


###  安装卸载
---
* 安装：运行exe。
* 卸载：进入安装文件夹中，运行cmd：可安装文件 /uninstall /force

### 快捷键

* Ctrl shift b  构建解决方案
* Ctrl F5  运行解决方案
* F5  调试解决方案
* Ctrl L  删除一行
* Alt 上下键  上下移动本行
* Ctrl enter  上方插入新行
* Ctrl shift enter  下方插入新行
* Ctrl + K，Ctrl + C  注释选定行
* Ctrl + K，Ctrl + U  取消选定行的注释
* Ctrl + K，Ctrl + D  正确对齐所有代码

### 工程开发

#### 建立C project

* 新建一个vc++项目，可以选择空项目。
* 在source file中添加所有cpp，只能一个有main。所有函数的声明放到一个a.h文件中，放到header file中。Source中的每个文件都include “a.h”。
* 之后进行ctrl shift b生成solution，使用ctrl F5运行即可。

#### 建立Django工程

* 参考文章：[使用Visual Studio 2015开发Python Django](http://blog.csdn.net/u010019717/article/details/51756150)
