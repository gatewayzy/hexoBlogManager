---
title: Text Editors
comments: true
mathjax: true
date: 2017-01-12 13:58:20
updated: 2017-01-12 13:58:56
categories: Softwares
tags:
- editor
---

**说明：**Text Editors配置使用。包括vi、sublime text等。
<!-- more -->

参考文章：


## Visual Studio Code
---
###  简介
---
* 微软开发的开源编辑器，支持linux、windows、macos，有强大的插件扩展。

### 安装使用
---
* 安装vs code。配置文件分为系统和用户，在文件、设置、首选项中。
* 安装插件
	* markdown+math 面板安装或者插件商店安装，ctrl shift . 预览markdown文件
	* python 插件同样安装，设置用户配置指定python："python.pythonPath": "C:\\Program Files\\Anaconda3\\python"，书写一个.py文件即可自动提示。

### 快捷键
---
* ctrl shift p 快捷调出命令面板
* ctrl shift b 资源管理器显示隐藏
* ctrl shift . 预览markdown文件，不支持同时滚动
* ctrl+k v     预览markdown文件，支持源码点击，预览跳转 



## Atom
---
### 简介
---
* github开发的开源编辑器，支持linux、windows、macos，功能强大。
* atom与vscode比较类似，都是非常棒的软件。


## Sublime Text
---
### Sublime Text简介
---
* Sublime Text是一款强大的文本编辑器，支持Windows、Linux、Mac OS X等操作系统，支持多种语法高亮和补全，所有插件均通过package control进行下载。
* 最近sublime text的维护更新越来越慢了，还是更喜欢vscode或者atom。

### Package control插件
---
* 安装Package control： 打开console（可用快捷键ctrl `），运行下面的代码即可

```
import urllib.request,os; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); open(os.path.join(ipp, pf), 'wb').write(urllib.request.urlopen( 'http://sublime.wbond.net/' + pf.replace(' ','%20')).read())
```

* 使用Package control： 打开Package control会话框（可用快捷键ctrl shift p），输入install package命令打开repository搜索界面，查找相应的package名称进行安装即可。

### Markdown Preview插件
---
Sublime自带对markdown语法的支持，但是需要预览插件。除了sublime text，还可以使用专业的MarkdownPad软件进行markdown写作。

#### 安装markdown preview
---
打开package control，进行install package，搜索markdown preview，进行安装。使用的话应该进行下面的快捷键设置，如设置f6（对应F6）。

#### 定义预览快捷键
---
* 点击 Preferences --> 选择 Key Bindings User，在数组内添加：
`{ "keys": ["alt+m"], "command": "markdown_preview", "args": {"target": "browser", "parser":"markdown"} }`
* 保存后，直接输入快捷键：Alt + M 就可以直接在浏览器中预览生成的HTML文件了，注意应设置html文件默认打开方式是浏览器。
* 支持表格：工具 》选项 》 Markdown 》Markdown处理器 改为 “Markdown(扩展)”即可。(不确定该插件是否支持)

#### 编辑markdown与生成预览
---
* Ctrl n新建一个text，保存为.md。按ctrl shift p调出package control选取ssm(set syntax: Markdown)，即使用markdown语法辅助。
* 编写完成即可进行preview，可以在package control中选中Markdown Preview: preview in browser，也可是使用自定义的快捷键如alt m在浏览器中打开。
* Window下可以用MarkdownPad，支持实时预览，并设置extra版、改样式等。

#### markdown语法
* markdown好处在于较为通用的文本样式，如多级列表、多级标题等非常简单，有github等网站的大力支持。
* 缺点在于表格书写麻烦、图片只能以链接引入（所以图片只能先上传到公网如免费的图片服务网站[imgur](http://imgur.com/)）
* 部分语法代码示例如下。 

```
Heading
=======
Sub-heading
----------- 
## 0
_italic_，**bold**, `monospace here`.
	Block
I
---
  * apples
  * oranges
  * pears
  II
---
  1. apples
  2. oranges
  3. pears
  4. 如果这里
  III  
---
A [link](http://example.com).

|表格前一行必须为空行|地址|账号密码|
|:----|:---:|---:|
|居左|默认居中|居右|

部分md支持<br>强制换行和表格中换行
```

### 其他插件
* Convert2UTF8：支持文件转码utf-8、gbk。
* Python：sublime text一般内置Python支持，只需系统中安装Python并添加到path中，在st中编写.py，按ctrl b或者package control中选择build with python。如print (“hello”)。

### 问题与解决
---
#### 禁止检查更新
---
打开Submine Text，找到Preferences -> Settings-User，在最后的花括号（“｝”）前添加 `"update_check":false` 。条目之间用,分开。

## VI
---
### vi模式
---

* vi两种基本模式：
	* 指令模式:将按键作为指令
	* 输入模式:将按键当作插入的字符
	* 指令模式进入输入模式的指令:a/A:新增  i/I:插入  o/O:新开一行开始输入
	* 输入模式进入指令模式的指令:按Esc键
* vi的visual模式[vim的visual模式](http://maxomnis.iteye.com/blog/2007928)
	* 三种visual模式
		* 用v命令进入的字符可视化模式。文本选择是以字符为单位的。
		* 用V命令(shift v)进入的行可视化模式。文本选择是以行为单位的。
		* 用 ctrl v进入块可视化模式。可以选择一个矩形内的文本。
	* 选择文本使用数字 + h/j/k/l（←↓↑→），包括光标所在位置。如v模式下用6h就是选中当前到左边6个字符。
	* d是删除选中内容，x是剪切，p是粘贴，esc万能退出。
	* 删除全文：shift v进入行可视化并默认选中全文，用d删除

### vi使用
---
* 进入vi：vi hello.c
* 退出vi：在指令模式下输入:q,:q!,:wq或:x(注意这个‘:’)，其中:wq和:x是存盘退出，:q是直接退出,如果文件已有新的变化,vi会提示保存。如果不想保存改变后的文件,就需要用:q!命令
* 命令模式的使用：
	* 跳到文件头：gg或者输入:1并回车
	* 跳到文件尾：:$ 或 G （大写g，当前若大小写锁定直接按g，未锁定则按shift+g）
	* vi或者less中查询abc可在初始指令模式下输入/abc，查找下一个用n

## MarkdownPad
---
* MarkdownPad算是Windows下比较方便的md文本编辑器了。

### 安装使用
####安装

	* 安装markdownpad
	* 为支持实时预览可能需要安装 awesomium_v1.6.6_sdk_win

#### 通用设置
* 支持表格显示: 勾选工具--选项--处理器，选中markdown(extra)以支持表格显示
* 更换渲染的css方式：勾选工具--选项--Stylesheet更换css样式
* 公式书写
	* 工具→选项→高级→HTML Head编辑器，在打开的窗口里输入下面的内容，然后保存关闭
	* 可能预览区没能正确的显示公式，在网页里预览就正常了，如果还不行应该确定是否无法访问国外服务器上的js，建议开启全局代理。
	* `$$x=\frac{-b\pm\sqrt{b^2-4ac}}{2a}$$` $$x=\frac{-b\pm\sqrt{b^2-4ac}}{2a}$$
	* `$$\alpha (x)=\sum_{m=0}^{m<2^2} \alpha_2m +\mu$$` $$\alpha (x)=\sum_{m=0}^{m<2^2} \alpha_2m +\mu$$

```
<script type="text/javascript"
   src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>
```

