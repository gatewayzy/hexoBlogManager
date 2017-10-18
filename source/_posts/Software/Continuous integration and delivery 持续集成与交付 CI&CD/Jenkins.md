---
title: Jenkins
comments: true
date: 2017-01-12 13:58:10
updated: 2017-01-12 13:58:56
categories: Softwares
tags:
- Continuous integration
---

**说明：**Jenkins原名Hudson，是一种可拓展性很强的持续化集成与发布工具。
<!-- more -->


---
参考文章：

## 简介
---
* Jenkins的前身是Hudson，是一个基于java开发的持续集成工具，用于监控程序重复性工作，如：持续的软件版本发布/测试项目，监控外部调用执行的工作。
* Jenkins特性：
	* 易于安装-只要把hudson.war部署到servlet容器，不需要数据库支持。
	* 易于配置-所有配置都是通过其提供的web界面实现。
	* 集成RSS/E-mail/IM-通过RSS发布构建结果或当构建失败时通过e-mail实时通知。
	* 生成JUnit/TestNG测试报告。
	* 分布式构建支持-Hudson能够让多台计算机一起构建/测试。
	* 文件识别- Hudson能够跟踪哪次构建生成哪些jar，哪次构建使用哪个版本的jar等。
	* 插件支持-Hudson可以通过插件扩展，你可以开发适合自己团队使用的工具。

## Hudson安装启动
---
* 下载hudson.war包，配置好jdk环境变量。其默认工作空间在user/.hudson下，可设置环境变量HUDSON_HOME指定路径。
* 注意：hudson路径中不能有特殊字符，测试发现连空格都不能有。

### Java –jar启动hudson

* 切换到jenkins.war存放的目录，输入如下命令：$ java -jar jenkins.war，然后在浏览器中输入localhost:8080即可。

### 第二种方法是用tomcat等打开

* 将jenkins.war文件放入tomcat下的webapps目录下，启动startup.sh来发布jenkins。访问`http://localhost:8080/hudson/`。可以把该tomcat进行设置成开机启动的服务从而将Jenkins设为系统服务。
* 为了防止Jenkins运行端口与所要发布项目发生端口冲突，应该把Jenkins的servlet容器端口修改。

## 构建项目环境
---
* 在“系统管理”面板中配置构建项目需要的各种软件。具体步骤包括：
	* 选择要安装的插件。
	* 配置jdk、svn、smtp等参数。
	* 新建项目，配置好svn等，选择build trigger为poll SCM是版本更新即构建。
* Hudson的项目存放在HUDSON_HOME的jobs/项目/workspace。


## 问题与解决
---
### Executing Maven 出错

* 问题描述：命令行查看构建构成提示错误为：
Executing Maven:  -B -f /var/lib/jenkins/jobs/Omri/workspace/pom.xml –install… …
java.lang.reflect.InvocationTargetException
Caused by: java.lang.NoClassDefFoundError: org/apache/maven/cli/MavenLoggerManager
    at org.jvnet.hudson.maven3.launcher.Maven3Launcher.main(Maven3Launcher.java:73)
* 出错原因：这是由于hudson版本和maven版本不兼容导致
* 解决方法：可以更换稳定的maven，如使用3.0.5版本构建即可。





