---
title: Latex
comments: true
mathjax: true
date: 2017-01-12 13:58:20
updated: 2017-01-12 13:58:56
categories: Softwares
tags:
- Text editors
---

**说明：**Latex是一种常用的Tex语法体系，通过编译文本生成pdf文件，用于书写高规范高要求的paper、book。
<!-- more -->

参考文章：


### 简介
---
* Tex是一种文本描述语言，支持高质量的打印需求。
* Tex有很多具体的语言版本，以Latex等为主流。
* LaTex语法主要用miktex软件，ctex软件基于miktex添加了对其他tex语法支持和中文支持。


###  安装配置
---
* 安装CTEX
	* 使用* WinEdt进行文件编写，编译，预览pdf等操作，WinEdt7已经设置免注册，免注册方法参考[latex WinEdt 6.0 破解方法](http://blog.sina.com.cn/s/blog_64827e4c0100u2a9.html)
	* 注意：安装CTEX可能会导致系统环境变量中的path被CTEX覆盖，应该先将path备份！
* 工程
	* 在project中新建pro，打开该pro，确保在project--project manager中是当前pro 
	* 工程打开文件，进行build tree，设置为main file方便打开主文件
* 新建文件
	* 新建doc保存时保存为:UTF-8选项
	* 编译文件更换为XeLaTex，对中文支持比较好，点击XeLaTex按钮进行编译，点击pdf预览即可。
* 修改默认pdf浏览器
	* 在options--execution modes--pdfviewer--选择auto-detect并更换为对应的pdf浏览程序，如acrobat.exe


### 文件控制
---
* 编写源码，经过编译生成pdf可以看到生成的文件，示例如下。

```
  \documentclass{article}
  \begin{document}
  My first \LaTeX~document.
  My first \TeX~document.
  \end{document}

```

### 页面布局
---


### 公式符号
---
* 参考文章
	* [LaTeX数学公式的符号表示](http://blog.csdn.net/ws_20100/article/details/49159291)

#### 示例

* 如果没有效果，应该确实是否是网络无法访问cdn.mathjax.org对应的js，建议开启全局代理模式以访问国外服务器。
	* $ 表示行内公式：`$E=mc^2$`  $E=mc^2$
	* $$ 表示整行公式：`$$\sum_{i=1}^n a_i=0$$` $$\sum_{i=1}^n a_i=0$$
	* `$$f(x_1,x_2,\ldots,x_n) = x_1^2 + x_2^2 + \cdots + x_n^2$$` $$f(x_1,x_2,\ldots,x_n) = x_1^2 + x_2^2 + \cdots + x_n^2$$
	* `$$\alpha (x)=\sum_{m=0}^{m<2^2} \alpha_2m +\mu$$` $$\alpha (x)=\sum_{m=0}^{m<2^2} \alpha_2m +\mu$$
	* `$$dudx|x=0$$` $$dudx|x=0$$
	* `$$f(x,y,z)=3y2z(3+7x+51+y2)$$` $$f(x,y,z)=3y2z(3+7x+51+y2)$$
	* `$$\mbox{对任意的$x>0$}, \mbox{有 }f(x)>0. $$`  $$\mbox{对任意的$x>0$}, \mbox{有 }f(x)>0. $$
	* `$$\LaTeX \ast\TeX$$` $$\LaTeX \ast\TeX$$
	* `$$x=\frac{-b\pm\sqrt{b^2-4ac}}{2a}$$` $$x=\frac{-b\pm\sqrt[5]{b^2-4ac}}{2a}$$


