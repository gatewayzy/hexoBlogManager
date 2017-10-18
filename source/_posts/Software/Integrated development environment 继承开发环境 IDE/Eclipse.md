---
title: Eclipse
comments: true
date: 2017-01-12 13:58:09
updated: 2017-01-12 13:58:56
categories: Softwares
tags:
- IDE
---

**说明：**Eclipse也是常用的Java集成开发环境软件，也支持Python等拓展。
<!-- more -->


### 简介

* Eclipse是IBM发起的，后成立软件联盟eclipse联盟，迁到eclipse开源社区。有支持java，c++，php等语言的版本，拓展性好，开源免费。
* 从2006年起，Eclipse基金会每年都会安排同步发布（simultaneous release）。同步发布主要在6月进行，并且会在接下来的9月及2月释放SR1及SR2版本。
* 2014年及之后的版本分别代号Luna、Mars、Neon等。


### 快捷键

|快捷键	|功能	|
|:---:	|:---:	|
|Ctrl +shift +L  	|查看所有快捷键	|
|Ctrl h 	|模糊查找文件		|
|ctrl shift h 	|查找类	|
|Ctrl shift r 	|查找文件	|
|Ctrl f 	|查找字符串	|
|ctrl shift G 	|查找被引用	|
|Ctrl G	|查看声明	|
|Ctrl shift enter 	|上方插入一行		|
|ctrl 1 	|快速fix	|
|alt . 	|智能提示	|
|F11	|运行（run）	|
|ctrl F11	|重新运行	|

### 导入导出项目

* 使用import/export将项目代码进行导入导出
* 如将web项目导出为web的war文件。

### 修改自动补全

#### 设置所有字符都进行智能提示

* 默认只有.才能触发智能补全提示，可以如下设置让所有字符都进行提示
	1.	“window”→“Preferences”
	2.	选择“java”，展开，“Editor”，选择“Content Assist”。
	3.	选择“Content Assist”，然后看到右边，右边的“Auto-Activation”下面的“Auto Activation triggers for java”这个选项。其实就是指触发代码提示的就是“.”这个符号。
	4.	“Auto Activation triggers for java”这个选项，在“.”后加abcdefghijklmnopqrstuvwxyz字母，方便后面的查找修改。然后“apply”，点击“OK”。

#### 取消空格、=补全，设置tab选取

* 默认情况下空格=会选取智能提示的第一项，这是不方便的，尤其是提示状况下输入空格也许只是想输入空格。方法是修改插件源码。
	1. 首先，打开Eclipse，打开window->show view，选择Plug-ins，再找到org.eclipse.jface.text，右键单击，选择importas > Source Project，导入完成后，在你的workspace就可以看到这个project了。
	2. 如果看到的工程中没有src目录，而是对应的jar包，说明没有导入插件源码。解决方法：要么换一个eclipse如Eclipse RCP版本，要么使用下面这种方式。
	3. 在1中右键的时候选择import as>Project from a Repository，选择finish，等待下载完成即可。这种方式就是从远程仓库中下载插件源码。
	4. 现在可以修改src中源码了。在org.eclipse.jface.text.contentassist.CompletionProposalPopup#verifyKey()方法中，进行如下修改两处。
		1. 第一处：if (contains(triggers, key)) { if (key!=0x20 && key!='=' && key!=';' && contains(triggers, key)) {  /**取消空格、=、；的自动选取*/
		2. 第二处：case '/t': e.doit= false; fProposalShell.setFocus(); /**将fP...setFocus()这一句换为insertSelectedProposalWithMask(e.stateMask);实现/t （tab键）选取内容*/ return false;
	5. 保存，将工程export为Deployable plugins and fragments，可以存到桌面，将里面的jar文件替换eclipse中plugins的旧jar。

### 软件preference与项目propertie设置
---

* Window ->preference 是对当前整个工作空间的设置，类似全局设置；
* 项目右键的properties是对当前项目的设置，类似局部设置；

#### 修改文件validation配置
* 取消校验的方法
	* 整个工作空间中取消校验：Window ->preference -> validation，勾选设置。
	* 当前项目取消校验，不过要允许使用项目设置

#### Java设置

*  Window ->preference ->Java ->installed jre中添加本地jdk版本（eclipse运行在jre上，而非jdk，默认自带jre）；
* Java ->compiler中设置编译时默认使用的jdk版本；

#### 项目propertie设置

* Project facets 中设置java版本（列出了所有服务的版本，以方便修改）。
* 添加jar文件夹
	* 在项目设置中，Libraries->Add Library->User Library，添加一个lib名称，再对其Add JARs 添加文件夹下的所有jar包即可。这种方式对比直接将jar包右键添加到path的好处在于可以有一级目录，不会直接显示一堆jar包列表。

### Eclipse插件管理

* Help —>Eclipse Marketplace。
* 在查找插件界面的最后有链接，打开后为插件浏览界面。
* 可以安装m2e，runjettyrun等等插件，或者按功能搜索、下载排行、下载推荐等安装。安装方法是将install标志拖到当前项目空间中，或者使用下载插件压缩包再本地安装的方式。Eclipse和MyEclipse都有一些插件支持hibernate、mybatis、struts等架构的开发。
* 在installed中查看已安装插件，并可以进行更改或卸载插件。

### 问题与解决
---
#### Project facet Dynamic Web Module to 2.5

* 问题描述：无法将网站项目改成2.5
* 解决方法：可以修改项目文件夹中.setting中的文件：org.eclipse.wst.common.project.facet.core，将网站版本改为2.5，重启项目。

#### utf-8解决乱码

* 解决办法1：修改工作空间的编码格式：Window->Preferences->General->Workspace->Text file Encoding
* 解决办法2：修改一类文件的编码格式：Window-->Preferences-->General-->Content Types-->把需要的文件类型指定编码如GBK

#### 添加xml自动补全

* 参考资料：[给Eclipse中hibernate.cfg.xml配置文件加提示](http://jingyan.baidu.com/article/95c9d20db53e29ec4e7561d8.html)
* 打开windows—preference—xml—catalog—add(user specified..)，location选择对应的dtd样式文件(从jar包中找)，key type选URI，key填dtd文件的http地址。重启xml即可。注意dtd文件应该放在不被删除的地方，如放在eclipse程序文件下。

#### 项目运行未找到application.xml

* 问题描述：将application.xml放在src目录下，配置`<context-param>的contextConfigLocation为classpath:applicationContext.xml，但是运行提示找不到该xml。
* 可能原因：该xml未被加入build path中。
* 解决方法：设置项目properties的java build path，在source中添加src目录，并确认included是All（删除其约束条件就是All）。


