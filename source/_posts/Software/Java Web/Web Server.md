---
title: Web Server
comments: true
date: 2017-01-12 13:58:23
updated: 2017-01-12 13:58:56
categories: Softwares
tags:
- Java web
---

**说明：**Web Server配置使用，包括Apache、Tomcat、Jetty等。
<!-- more -->


##  Apache
---
* 简介
	* 对静态资源的服务效率非常高，但动态资源效率不高。

Ubuntu下安装使用如下。Windows下更加简单。
* 安装
	* 安装apache：sudo apt-get install apache
* 配置
	* 配置文件：/etc/apache2/apache2.conf
	* 配置文件中引用了端口文件、目录文件等，可修改相应的端口、网站文件路径等。
* 
服务命令：service apache2 start|stop|restart|status|…


##  Jetty
---
### Jetty介绍
---
* Jetty是eclipse基金会开发，和apache的tomcat二者是目前最为流行的开源的web server / servlet容器，都遵循java servlet规范。
* 相比之下，jetty比tomcat要轻量级，功能少，可通过插件和拓展，耗费资源比tomcat少。所以企业级应用一般用tomcat，小型应用用jetty方便些。

### 安装
---
* 到jetty官网下载相应版本、位数即可。一般都是非安装文件的插件包形式，需要在IDE中设置相应的jetty路径。

### 主要端口号
---
* http端口号默认是8080，一般在/etc/jetty-http.xml中设置。如`
<Set name="port"><Property name="jetty.http.port" deprecated="jetty.port" default="8080" /></Set>`。 如果没有的话，尝试查找其他相似的xml文件。
* Maven运行时修改端口号示例：mvn jetty:run -Djetty.port=8081


##  Tomcat
--- 
* 参考文章：
	* [Tomcat的详解和Tomcat的面试问题](http://www.toutiao.com/i6387497067698192897/)

### Tomcat安装启动
* 启动与关闭猫
	* 启动可以运行startup.bat（win）或者startup.sh（linux），正常关闭时通过运行shutdown.bat/sh。
* 设置tomcat自启动
	* Windows本地服务下已存在tomcat服务的话，属性设置为自启动即可。
	* Windows下添加计划任务，设置开机后自启动startup.bat即可。
	
* 将Tomcat安装为服务
	* cmd进入bin，运行 `service.bat install`。如果失败尝试配置以下环境变量。
	1. 设置环境变量CATALINA_HOME：Tomcat路径
	2. 修改Path环境变量，在path中加入：%CATALINA_HOME%\bin;


### 发布项目
* 将项目打包后，将war包等类型包复制到tomcat的webapps目录下。Tomcat配置无误后，启动tomcat会自动解压war包以及webapps下其他的所有项目。（如果无法正常启动，应查看tomcat下logs中的日志排查问题。）
* 如果是普通web工程，只需在tomcat的webapps中添加项目文件夹，将项目WEB-INF一级的文件拷贝到文件夹中，启动tomcat即可自动发布。

### Tomcat 界面管理与设置
* 设置tomcat的用户名密码，conf/tomcat-users.xml中设置user、role、passwd。

```
  <role rolename="tomcat"/>
  <role rolename="role1"/>
  <role rolename="manager"/> 
  <role rolename="manager-gui"/>
  <role rolename="admin"/>  
  <role rolename="admin-gui"/>  
  <role rolename="manager-script"/>  
  <role rolename="manager-jmx"/>  
  <role rolename="manager-status"/> 
  <user username="tomcat" password="tomcat" roles="tomcat"/>
  <user username="both" password="tomcat" roles="tomcat,role1"/>
  <user username="role1" password="tomcat" roles="role1"/>
  <user username="admin" password="admin" roles="tomcat,role1"/>
  <user username="tomcat" password="" roles="manager-gui"/>
  <user username="Tomcat" password="Tomcat" roles="admin-gui,admin,manager-gui,manager,manager-script,manager-jmx,manager-status"/>
```

* 前台进入manager app界面，使用“Tomcat”账户登录对工程进行管理：stop、start、reload等。前台还可以对tomcat的其他setting进行设置。

### Tomcat主要端口
* Tomcat的端口设置在conf/sever.xml中。一般每一个猫要设置不同的shutdown端口、http端口、AJP端口。
* SHUTDOWN 远程停服务端口，默认8005，用于命令行下通过端口关闭服务。
`<Server port="8005" shutdown="SHUTDOWN">`
* HTTP端口，默认8080
`<Connector port="8080" protocol="HTTP/1.1"  connectionTimeout="20000"
redirectPort="8443"  URIEncoding="UTF-8"/>`
其中URIEncoding="UTF-8"设置URI使用UTF-8编码。
* AJP端口，默认8009
`<Connector port="8009" protocol="AJP/1.3" redirectPort="8443" />`

### tomcat的体系结构
---
* tomcat提供service、engine、host、web(context)应用分层。
	* 一个tomcat server一般运行一个Catalina service。
	* service监听各种用户请求的connector协议，并通过Catalina内置的engine进行请求指派，路由到各个虚拟host主机。
	* host包含多个web应用（对应context配置），进行请求处理。
```
<Server port="8105" shutdown="SHUTDOWN">
<Service name="Catalina">
<Connector port="8180" protocol="HTTP/1.1" connectionTimeout="20000">
<Engine name="Catalina" defaultHost="localhost">
<Host name="localhost"  appBase="webapps" autoDeploy="true">
…
</Host>
<Host name="www.taobao.com" appBase=" d:\web1" autoDeploy="true">
<Context path=”/” docBase=”d:\web1”> (只需本机拥有该域名或修改hosts文件)
</Host>
</Engine>
</Service>
</Server>
```

### catalina.bat
* 编辑Catalina.bat，可以查看运行猫时使用的各种运行环境的配置，部分参数如下。
	* CATALINA_HOME	catalina目录
	* CATALINA_OPTS		catalina运行参数
	* CATALINA_TMPDIR	catalina临时目录
	* JAVA_HOME		jdk目录，默认是系统环境变量JAVA_HOME指定，使用debug模式时必要
	* JRE_HOME			jre目录，优先使用JRE_HOME值，若空则使用上面的JAVA_HOME
	* JAVA_OPTS			jre运行参数
	* JAVA_ENDORSED_DIRS	
	* JPDA_TRANSPORT
	* JPDA_ADDRESS
	* JPDA_SUSPEND
	* JPDA_OPTS
	* LOGGING_CONFIG	登录设置
	* LOGGING_MANAGER	日志管理
	* TITLE				运行窗口名
* 不同的tomcat可以按照需要配置不同jre配置、title等。
* 修改使用不同的jre
如要修改使用不同的jre，可以编辑tomcat/bin/setclasspath.bat在`rem Otherwise either JRE or JDK are fine`之前加上路径配置，如 `set JAVA_HOME = C:\....   jdk路径`、 `set JRE_HOME = C:\....\jre  Jre路径`

* 修改tomcat运行窗口名
如要修改tomcat运行窗口名，只要编辑catalina.bat，找到`if "%TITLE%" == "" set TITLE=Tomcat` 修改标题为 `TITLE=MyApp:8070`

### Tomcat集群
---
####  安装与配置
* 一般只需要设置shutdown、HTTP1.1、AJP端口不同就可以了，运行情况可以通过查看日志解决，包括程序输出日志Catalina.out，localhost-access.log等日志。
* 设置不同的3个端口，最好修改TITLE，进入相应目录进行批处理。

####  共享session
* 目前使用tomcat做session共享有下面几种方式：
	* 使用 filter 方法存储
		* 这种方法比较推荐，因为它的服务器使用范围比较多，不局限于 tomcat ，而且实现的原理比较简单容易控制。 可以使用 [memcached-session-filter](http://code.google.com/p/memcached-session-filter/)
	* 使用 tomcat session manager 方法存储
		* 这种方法服务器只能使用 tomcat ，网上有针对 memcached 和 redis 实现，直接配置就行了。
		* 如Memcached就是安装Memcached，再设置tomcat配置context.xml中启用模块的方式。但是注意需要添加相应的jar文件到tomcat的lib中。
		* redis也是类似的。准备好redis服务器，在tomcat的lib中添加支持的jar包，然后配置tomcat的context.xml。
	* 使用 terracotta 等服务器共享session，经常结合spring-session框架实现。

```
<!-- Memcached  session -->
<Manager
className="de.javakaffee.web.msm.MemcachedBackupSessionManager"  
memcachedNodes="n1:10.15.62.55:11211 n1:10.15.62.58:11211"  
sticky="false"  
sessionBackupAsync="false"  
lockingMode="uriPattern:/path1|/path2"  
requestUriIgnorePattern=".*\.(ico|png|gif|jpg|css|js)$"  
transcoderFactoryClass="de.javakaffee.web.msm.serializer.kryo.KryoTranscoderFactory"/>
```


```
<!-- redis session -->
<Context>
    <Valve className="com.orangefunction.tomcat.redissessions.RedisSessionHandlerValve" />
    <Manager className="com.orangefunction.tomcat.redissessions.RedisSessionManager"
           host="10.15.82.57"
           port="6379"
           password="password"
           database="0"
           maxInactiveInterval="60" />
</Context>
```

#### 脚本管理tomcat集群
---
* 批处理流程一般是：更新项目代码，编译成文件，停止tomcat，拷贝项目到webapps下，启动tomcat。
* Windows脚本
	* 注意Windows下使用call调用程序，如需等待call的程序执行完毕，可以用定时timeout 3 等待3秒。
	

```
echo stopping tomcats...
cd tomcat20123\bin\
call .\shutdown.bat
cd ..\..\tomcat20122\bin\
call .\shutdown.bat
cd ..\..\tomcat20121\bin\
call .\shutdown.bat
echo "deploy tomcats for tcm..."
call .\startup.bat
cd ..\..\tomcat20122\bin\
call .\startup.bat
cd ..\..\tomcat20123\bin\
call .\startup.bat
echo "tomcats have been startup."
```

```
echo  updating svn...
cd \Program Files\svn\tcm
svn update
echo mvn package..
call mvn clean package -Pplatform -Dmaven.test.skip=true
echo stopping tomcats...
cd ..\..\tomcats\tcm\bin 
call .\shutdown.bat
:waiting for shutdown
timeout 5
echo deleting old war...
del –f –s -q ..\webapps\tcm*
rd /s /q ..\webapps\tcm
echo copy new war...
copy ..\..\..\svn\tcm\target\tcm.war ..\webapps\
echo starting tomcat...
call .\startup.bat
echo tomcat restarted!
```

* Ubuntu脚本
	* Ubuntu下使用chmod +777 a.sh允许运行权限。
	* 如果出现`#!/bin/bash - no such file or directory`错误，查看文件头$ head -1 a.sh | od –c。错误头是：`0000000   #   !   /   b   i   n   /   b   a   s   h  \r  \n`。正确的是`0000000   #   !   /   b   i   n   /   b   a   s   h  \n`。处理方法可以是：直接touch一个文件，代码复制进去。
	* 启用防火墙的端口：sudo ufw allow|deny 8080|tcp|端口或协议
	* 关闭集群流程：查找端口占用的进程，关闭各个进程。

```
#!/bin/bash
PROJECT="usercenter"
cd ./svn/usercenter/
echo "going to update project: ${PROJECT}."
svn update
mvn clean package -Dmaven.test.skip=true
echo "mvn package done."
./../../sh/shutdown.sh
echo "tomcats have been shutdown."
rm  ../../1/webapps/${PROJECT}* -r
rm  ../../2/webapps/${PROJECT}* -r
rm  ../../3/webapps/${PROJECT}* -r
echo "going to copy ${PROJECT}.war to all webapps."
cp ./target/${PROJECT}.war   ../../1/webapps/
cp ./target/${PROJECT}.war   ../../2/webapps/
cp ./target/${PROJECT}.war   ../../3/webapps/
./../../sh/startup.sh
echo "tomcats have been startup."
```



```
echo "kill all tomcats"
ID=`lsof -i:8103 |tail -1 | awk '{print $2}'`
echo $ID
for id in $ID
do
  kill -9 $id
  echo "killed $id"
done
echo "#############################"
```

### tomcat 启用 https
参考文章 [tomcat7+jdk的keytool生成证书 配置https](http://www.cnblogs.com/sixiweb/p/3339698.html)
* window下https
	1. 确定tomcat使用的jdk。以使用系统环境变量中的jdk1.8为例。
	2. 用jdk生成keystore文件并导出证书
		1. %JAVA_HOME%\bin\keytool -genkey -alias sr -keyalg RSA  # 这是设置证书别名叫sr，别名任选不重复就行
		2. 输入信息，假设密码是changeit，填写要求的信息，国家代码用cn，正确按y，输入tomcat的秘钥口令也假设为changeit。
		3. 会在用户主目录生成.keystore文件，将其拷贝到tomcat的bin目录下。
		4. 在tomcat的bin目录下运行命令：keytool -selfcert -alias sr -keystore .keystore，输入之前的命令changeit
		5. 接着运行keytool -export -alias sr -keystore .keystore -storepass changeit -rfc -file sr.cer 会导出证书sr.cer
	4. tomcat启用https
		* 配置tomcat/conf/server.xml
		* 找到`<Connector port="8443"` 取消注释并改成`port="443" ... sslProtocol="TLS" keystoreFile="bin/.keystore" keystorePass="changeit" />`
		* 上面设置了https采用默认端口443，且配置http是证书。重启tomcat，访问https/127.0.0.1/ 
		* 正常的话，应该会提示https不安全，证书不安全，点击继续访问后可以访问页面。
	5. 用户安装证书
		* 将证书sr.cer发给使用者，让他们安装该证书（具体步骤同12306.cn网站证书的安装步骤）
		* 用户下载sr.cer双击运行，添加到“本地计算机”，“就所有证书都放入下列存储”，选择“受信任的根证书颁发机构”，一路确定即可。

* linux下配置方式与window下类似，只不过建议所有的keytool都使用绝对路径，因为locate keytool可以看到ubuntu有很多keytool。

* 错误问题
	* 访问https时提示“您的连接不是私密连接...此服务器无法证明其所在网域是...”，但是可以继续访问。
	* 由于证书是自己给自己颁发的，所以浏览器不相信证书颁发机构。原因是浏览器没有 CA 证书，只有 CA 证书，服务器才能够确定这个用户就是真实的访问请求（比如不是代理过来的）。
	* 如果我们的证书不是自己颁发，而是去靠谱的机构去申请的，就不会出现这样的问题，因为靠谱机构的证书会放到浏览器中，浏览器会各种安全检查。初次尝试的同学可以去 startssl.com 申请一个免费的证书，相关操作可以参考[startssl申请免费ssl证书的方法](http://www.tuicool.com/articles/VbeEfyz)，需要注册邮箱，域名验证，证书下载...

## 问题与解决
---
### tomcat提示Choose unique values for the webAppRootKey
* 问题描述：一个tomcat发布多个web项目提示错误Choose unique values for the 'webAppRootKey'
* 原因解析：Tomcat发布多个项目时抛的webAppRootKey错误,  原因是部署在同一容器中的Web项目，定义了相同的webAppRootKey或者都没有定义
* 解决方法：为后者指定或者为每个web都定义一个webAppRootKey

```
<context-param>
    <param-name>webAppRootKey</param-name>
    <param-value>app1.root</param-value>
</context-param>
```

### ubuntu tomcat提示isARPRequired()Z错误
* 解决方法：重新下载官网的新版的tomcat。注意新版需要修改设置：端口修改、中文支持URLEncoding=utf-8、系统没有中文乱码。