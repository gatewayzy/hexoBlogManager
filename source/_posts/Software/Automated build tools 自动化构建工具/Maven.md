---
title: Maven
comments: true
date: 2017-01-12 13:58:13
updated: 2017-01-12 13:58:56
categories: Softwares
tags:
- Automated build tools
---

**说明：**Maven自动化构建工具简单灵活，提供缺省设置和高级项目管理等功能，比Ant要简单很多。
<!-- more -->


---
参考文章：

##  Make、ant、maven等工具比较
---
项目构建工具用的较多的是：ant、maven、hudson、gradle。

* Make
	* Make属于早期项目构建工具，主要用于类Unix系统（Linux、mac os、 FreeBSD等）衍生的GNU make、BSD make以及windows的Microsoft nmake。Make由makefile脚本驱动，语法包括target、prerequisite、command等。
	* Make功能强大，与系统命令结合。语法死板，难以跨平台。
* Ant
	* Another Neat Tool，基于java，配置构建脚本build.xml的target、depends、task等。由apache开发。
	* 跨平台，但是还是重复编译构建等命令，依赖管理要手工或者用Ivy管理。Ant与make都是面向过程的。
* Maven
	* Maven基于面向项目模型（project object model，pom），具有大量插件和强大的中央仓库，也是Apache开发。
	* Maven的“简单和依靠约定”提供现成可调用的goal，生命周期简单。但是牺牲灵活性，依赖的版本冲突问题难以解决，目标goal定制化困难，难以写出复杂的构建脚本。
* Gradle
	* DSL（Domain Specific Languages，领域特定语言)通常的想法是设计一套能够解决特定领域问题的语言。在构建这方面，DSL的一个成功案例就是Gradle。
	* Grandle结合ant与maven优点，灵活而易用。基于groovy语言，目前支持java、groovy、Scala。

## Maven基础
---
Maven主要作用：项目构建、依赖管理、项目信息管理（如生命周期）。
### Maven生命周期管理
---
maven有3个独立的生命周期：clean、default、site。

* Clean：清理，主要包括pre-clean、clean、post-clean。
* Default：构建的所有步骤，含validate、initialize、generate-resources、test、package、install、deploy等等阶段。
* Site：建立和发布站点，含pre-site、site、post-site、site-deploy阶段。
maven命令会调用各个独立生命周期的相应阶段，如mvn clean，mvn test，mvn clean install，mvn clean deploy site-deploy等等组合命令。

### Maven jar包
---
#### Maven坐标

Maven为每一个jar包提供了坐标管理，保证能够定位、区分每一个jar包，方便管理jar包之间的依赖关系。
Maven坐标由3部分组成：`<groupId>、<artifactId>、<version>`。
本文简写：G：groupId		A：artifactId		V：version

#### 依赖管理

在POM 4中，<dependency>中还引入了<scope>，它主要管理依赖的部署。目前<scope>可以使用5个值：

* compile，缺省值，适用于所有阶段，会随着项目一起发布。
* provided，类似compile，期望JDK、容器或使用者会提供这个依赖。如servlet.jar。
* runtime，只在运行时使用，如JDBC驱动，适用运行和测试阶段。
* test，只在测试时使用，用于编译和运行测试代码。不会随项目发布。
* system，类似provided，需显式提供包含依赖的jar，Maven不会在Repository中查找它。

#### 依赖排除

如<optional>true</optional>，进行项目间依赖传递。
如exclusions进行依赖过滤。

```
<dependency>    
  <groupId>org.apache.hbase</groupId>
  <artifactId>hbase</artifactId>
  <version>0.94.17</version> 
  <exclusions>  
     <exclusion>	 
       <groupId>commons-logging</groupId>		
       <artifactId>commons-logging</artifactId>  
     </exclusion>  
  </exclusions>  
</dependency>
```

### Maven仓库解析
---
#### 仓库解析

* 依赖范围scope为system的jar将从本地文件系统查找依赖。
* 其他jar会在确定版本后先从本地仓库查找依赖，本地没有则访问远程仓库。
* 如果是SNAPSHOT版本，则检查所有远程相应的maven-metadata.xml（在G/A/下），与本地对应的元数据合并后，确定最新的快照版本。
* 如果是RELEASE或LATEST版本，也检查所有远程相应的maven-metadata.xml，确定版本。由于该xml中含有release（最新发行版）、versions（所有版本，包括快照版等）、lastUpdated（最新时间戳版本）的版本，不同xml记录的内容不容，综合后可能导致latest经常变化且不稳定，release版本会更新到最新的发行版，所以不建议用latest。
* Maven有一个超级pom文件，超级pom对maven仓库、结构等进行配置，所有的pom都根据超级pom的规范进行配置描述。maven3的超级pom位置为`maven/lib/maven-model-builder-*.jar` 中的 `org/***/model/pom-4.0.0.xml`。（maven3使用pom4文件描述格式）

#### 远程仓库版本过滤

通过配置远程仓库进行版本过滤。如`<repository>`中设置`<releases>`为true，`<snapshots>`为false，`<layout>`default为maven2和maven3的文件布局而非maven1。在`<releases>`下设置以下值：`<updatePolicy>`为daily，`<checksumPolicy>`为ignore，这表示使用这个远程repo时使用release版本，不使用快照版，版本每天更新，不进行和校验。

### Maven插件
---
Maven核心了抽象生命周期，执行由插件完成，二者绑定在一起。

#### 插件介绍

* 插件的goal：一个插件可以根据项目依赖完成不同的plugin goal，表示为插件:目标，如依赖插件dependency的一些目标组合为dependency：tree，dependency：list等等。
* 插件的下载：虽然存放方式和依赖一样的GAV，但是如果本地没有插件的话，是不会自动去远程仓库下载的。
* 插件的前缀：插件的前缀可以省略，如dependency：analyze完整的是mvn org.apache.plugins:maven-dependency-plugin:2.1:tree，也就是说完整的是G:A:V:goal。其中插件目标省略时自动添加Apache或Codehaus的插件groupId以及用户自定义的插件groupId，而dependency是该插件的前缀，起简称作用，版本默认使用最新版本，依maven版本存在稳定版与快照版差别。
* 插件命令与参数：mvn help:descripe –Dplugin=compiler –Ddetail就是用maven-help-plugin帮助插件查看其它插件（如compiler）信息，describe是goal，后面参数都要用java的参数表示方式-D添加，-Ddetail表示详细信息。mvn install –Dmaven.test.skip=true表示生成项目代码并且跳过测试用例。

#### 常用插件

* maven-compiler-plugin进行项目项目编译。maven-war-plugin支持项目war打包。
* Jetty-maven-plugin支持IDE中自动编译，由于是非官方插件注意添加repo库，需设置扫描间隔，否则不会自动编译。
* maven-jetty-plugin支持maven 命令行运行jetty发布项目。使用方法是运行mvn jetty:run –Djetty.port=8081 使用jetty发布当前目录下的项目，并设置端口为8081。
* cargon-maven2-plugin插件支持自动化部署到web容器中，包括本地部署和远端部署。

```
	<build>
		<finalName>tcm</finalName>
		<plugins>
			<plugin>
				<groupId>org.mortbay.jetty</groupId>
				<artifactId>maven-jetty-plugin</artifactId>
				<version>6.1.10</version>
				<configuration>
					<scanIntervalSeconds>10</scanIntervalSeconds>
					<webAppConfig>
						<contextPath>/test</contextPath>
					</webAppConfig>
				</configuration>
			</plugin>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-compiler-plugin</artifactId>
				<version>3.1</version>
				<configuration>
					<source>1.6</source>
					<target>1.6</target>
					<encoding>UTF-8</encoding>
				</configuration>
			</plugin>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-war-plugin</artifactId>
				<configuration>
					<webXml>${webXmlPath}</webXml>
				</configuration>
			</plugin>
		</plugins>
	</build>
```

一般情况下jar包都可以使用pom.xml来配置管理，但也有一些时候，我们项目中使用了一个内部jar文件，但是这个文件我们又没有开放到maven库中。为了方便，可以将jar放在WEB-INF/lib/中，本地运行的时候将jar添加到build path中，打包运行的时候用下面的配置。

```
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.1</version>
    <configuration>
        <source>1.7</source>
        <target>1.7</target>
        <encoding>UTF-8</encoding>
        <compilerArgs> 
            <arg>-verbose</arg>
            <arg>-Xlint:unchecked</arg>
            <arg>-Xlint:deprecation</arg>
            <arg>-bootclasspath</arg>
            <arg>${env.JAVA_HOME}/jre/lib/rt.jar</arg>
            <arg>-extdirs</arg> 
            <arg>${project.basedir}/src/main/webapp/WEB-INF/lib</arg>
        </compilerArgs> 
    </configuration>
</plugin>
```

其中${project.basedir}一定要写，不然会出现“在windows”下可以正常编译，在Linux服务器上就“有可能”出现编译找不到jar包的错误。

### Maven运行内存设置
---
一般java默认分配内存不够maven build使用，就可以设置MAVEN_OPTS选项进行修改，防止内存溢出。可尝试以下方法。

#### 修改项目的maven build内存

* Eclipse中项目进行maven build内存不足时，可以在项目上右击 -> Run As -> Run Configurations -> Maven Build中为jre的VM arguments中输入配置如：-Xms128m -Xmx512m。
* Eclipse中如果是jetty运行内存不足，也可以类似的为项目右键run as jetty的配置设置arguments为-Xms128m –Xmx512m表示给jetty分配更大的内存。

#### 修改maven的内存配置

* 找到文件%M2_HOME%\bin\mvn.bat，在启动Maven的脚本中找到注释：@REM set MAVEN_OPTS=-Xdebug -Xnoagent -Djava.compiler=NONE...它的意思是你可以设置一些Maven参数，在注释下面加入一行：set MAVEN_OPTS= -Xms128m -Xmx512m

#### 添加系统环境变量

* Windows下就是添加系统环境变量变量名是MAVEN_OPTS，值为 -Xmx1024m。
* Linux下添加就是编辑文件 /etc/profile添加MAVEN_OPTS=-Xmx512m或者运行export JAVA_HOME MAVEN_HOME MAVEN_OPTS JAVA_BIN PATH CLASSPATH。

### Maven项目的版本规定
---
Maven项目版本号，版本之主干、分支、标签。
GPG（GnuPG）是PGP（Pretty Good Privacy）的一个免费实现。常用于email加解密以及提供签名。可用于构件签名，增强安全性。在`<build>`中添加并设置maven-gpg-plugin。

### 习惯优于配置
---
* Maven使用习惯优于配置的方法。自动设置默认的文件结构，classpath等设置，这些在超级pom中进行了约定。
* Java Resources （包在Classpath中）：src/main/java（源代码），src/main/resource（资源配置），src/test/java（测试代码）。
Target（以web项目为例）：src/main/webapp（对应/）。

### 聚合与继承
---
* 聚合是将多个模块用一个pom packaging的pom进行整合<modules>。、
* 集成是多个模块继承`<parent>`，且可以在父类中用`<dependencyManagement>`和`<pluginManagement>`对子类的依赖和插件进行管理，子类要设置`<relativePath>`。
* 一般一个pom既是聚合pom又是父pom。

### 灵活构建：属性、profile、资源过滤三大特性
---
组合使用：将变化的配置设置成maven属性，然后为资源目录开启资源过滤，并在profile中设置相应的值并激活。

#### 属性

1.	内置属性：`${basedir}`项目根目录，`${version}`项目版本；
2.	Pom属性：pom文件中对应元素的值。${project.name}是`<project><name>`元素的值，${project.build.sorceDirectory}等等。
3.	自定义属性：pom中的`<properties>`
4.	Settings属性：settings.表示使用settings.xml中的值，如${settings.localRepository}。
5.	Java系统属性：使用mvn help:system可以查看java平台及环境变量属性，java平台属性也可以用maven引用。如${user.home}。
6.	环境变量属性：如${evn.JAVA_HOME}，即以evn.开头。

#### 资源过滤

* Maven默认只有pom文件中的属性才会进行解析，如果是其他资源文件并不会将${name}当做属性进行赋值处理。
* 这时需要使用maven-resources-plugin，它默认并不开启资源过滤功能，而只是将main和test文件复制到相应地目录下。如果要对resources目录进行过滤，则需要在超级pom中对`<resource>`添加`<filtering>true</filtering>`。其它resource也只需这一句filtering开启。

#### Profile

* Profile可以放在pom.xml、用户的settings.xml、全局的settings.xml中，只是在有效范围上的差别。

#### 激活方式：

*	命令行激活：如mvn install –Pdevx, -Pdevy 支持同时激活多个配置。例如下面在打包的时候使用mvn clean package -Pplatform -Dmaven.test.skip=true，就是激活id为platform的profile并且跳过测试。

```
<profiles>
		<profile>
			<id>dev</id>
			<properties>
				<webXmlPath>src/main/webapp/WEB-INF/web.xml</webXmlPath>
			</properties>
		</profile>
		<profile>
			<id>platform</id>
			<properties>
				<webXmlPath>src/main/webapp/WEB-INF/config/webs/web_platform.xml</webXmlPath>
			</properties>
		</profile>
	</profiles>
```

*	Settings文件中显示激活：`<activeProfiles>`中配置。
*	Profiles定义时默认激活：`<activation><activeByDefault>`为true。
*	Profiles定义时系统属性激活、操作系统环境激活、文件是否存在激活。

## Maven 使用
---
### Maven配置

* 下载apache-maven后，配置conf中的setting.xml，修改本地仓库目录如下，因为默认会放到用户的home目录下的.m2。
* `<localRepository>D:/Project/m2/repository</localRepository>`
* 其中还可以设置maven的代理、远端仓库、仓库镜像、profiles等。如设置maven全局镜像如下。

```
	<mirrors> 
        <id>tcm</id>  
        <name>tcm repo</name>  
        <url>http://10.15.82.93:8081/nexus/content/groups/public/</url>  
        <mirrorOf>*</mirrorOf>
	</mirrors>
```

#### Eclipse中使用maven

* Eclipse如果没有maven支持则需要先安装m2eclipse插件，在maven模块中关联上自己下载的maven（不推荐使用内置的maven），然后在user setting中更新上面设置好的setting.xml。
* m2eclipse插件的地址是`http://m2eclipse.sonatype.org/sites/m2e`，不要使用不稳定的`http://download.eclipse.org/technology/ m2e/releases`。 
* Eclipse可以对maven项目进行maven clean、update maven project等操作。
* 使用eclipse运行maven项目：可以run as maven install等生成target，找到目录下的war包，拷贝到tomcat的webapps中，启动猫可以自动解压部署。可以安装runjettyrun插件进行项目run as jetty。

#### Maven命令行管理项目

将maven目录添加到系统环境变量M2_HOME，并在path中添加%M2_HOME%\bin。
在cmd到项目目录中进行mvn install、mvn clean、mvn jetty:run等操作，注意命令行方式需要在项目pom的build中配置相应的插件坐标，如compiler、jetty等插件。

#### eclipse建立Maven项目

建立maven项目可以使用模板，如webapp模板、quickstart模板。导入maven项目时，如果下载依赖只有pom和sha1文件没有jar包，可能是被墙了，可以复制别人的repo仓库中的jar，或者建立nexus私服等方式共享jar。

### 常用的repo仓库
---
* 一些repo镜像地址如下，部分网站可能无法正常访问，一些特殊的jar需要网上搜索相应的repo网址。
	* https://repo.maven.apache.org/maven2/（central仓库）
	* http://uk.maven.org/maven2/（推荐）
	* http://repository.sonatype.org/content/groups/public/
	* http://repository.jboss.org/nexus/content/groups/public/
	* http://maven.oschina.net/content/groups/public/

```
	<repositories>
		<repository>
			<id>uk</id>
			<name>uk-repo</name>
			<url>http://uk.maven.org/maven2/</url>
		</repository>
		<repository>
			<id>xss-html-filter releases</id>
			<name>xss-html-filter Releases Repository</name>
			<url>http://xss-html-filter.sf.net/releases/</url>
		</repository>
		<repository>
			<id>mvn-repo</id>
			<url>http://maven.ansj.org/</url>
		</repository>
	</repositories>
```

## 问题与解决
---
### 错误Dynamic Web Module 3.0 requires Java 1.6 or newer

* 这是由于你的 Maven 编译级别是 jdk1.5 或以下，而你导入了 jdk1.6 以上的依赖包。
* 可以修改项目的.classpath 文件，调整其中的JavaSE的版本。
* 可以指明项目编译的jdk版本设置：

```
<build>  
    <plugins>  
        <!-- define the project compile level -->  
        <plugin>  
            <groupId>org.apache.maven.plugins</groupId>  
            <artifactId>maven-compiler-plugin</artifactId>  
            <version>2.3.2</version>  
            <configuration>  
                <source>1.8</source>  
                <target>1.8</target>  
            </configuration>  
        </plugin>  
    </plugins>  
</build>
```

### 错误missing artifact：…jar

* 导入maven项目后提示错误：Missing artifact com.company.air:…:jar:1.0.1。
* 常见引起jar包丢失错误的原因总结：
	* 引入的包在所有的repo中不存在，要么找含有该jar的repo，要么手动下载并添加到build path；
	* 引入的jar包的pom中引用了一些jar包，但那些jar包在repo中不存在，要么手动下载并添加该jar，要么添加存在被引用的jar包的repo；
	* 下载jar包失败时引入的.lastUpdated文件阻止了jar包下载，应该全部删除此类型文件并update。
* 解决方法：
	* 可以尝试本地repo中删除.lastUpdated、-not-available等非jar包，强制更新项目依赖（update maven project中的force update snapshot/release），解决的核心就是保证相应的jar包能下载到，并且没有.lastUpdated文件。
	* 实在找不到相应的repo时，可以自建私服并上传该jar包，或者将jar包放到项目中链接。
	* 如果本地已经下载到了一些jar，但是还是提示一大堆的jar缺失，应该从第一个缺失的jar开始排查私服上是否有对应的jar文件（最好从第一个错误提示开始排查错误，因为之后的错误提示很可能是因为第一个错误引起的）。

### 错误Failed to read artifact descriptor for org.fnlp:fnlp-core:jar:2.1

* 这是由于jar包引入了其他依赖包，但是被依赖的包却下载不到。
* 解决方法：将本jar包复制到项目中链接，或者删除依赖，后者寻找被依赖的jar包。

### 缺少src/test/java等目录

* 在项目上右键选择properties，然后点击java build path，在Library下，选择JRE System Library进行编辑，选择workspace default jre就可以了。
* 还有一种方式是自己手动添加source folder，方法是新建->source folder->路径为src/test/resources。

## Maven私服Nexus
---
Sonatype-Nexus是Sonatype公司的提供建立maven私服进行仓库管理的软件，还有一些其他repo管理软件这里不做介绍。

### 安装启动nexus
---
#### 安装

* 解压nexus-2.11.3-01.zip文件夹到一个目录，sonatype-work.zip同样操作。
* 将nexus的bin路径添加到path中。
* 管理员身份运行 nexus install	、nexus start，成功的话nexus就会开机启动，并已经作为一个系统服务了。
* 可能遇到的错误信息：The nexus service was launched, but failed to start。
	* 出错原因：找不到java虚拟机或者版本不一致。
	* 解决方法：安装java即可解决。如果还有问题，可以通过指定nexus-2.1.1\bin\jsw\conf\wrapper.conf文件中的wrapper.java.command=D:\Program Files\Java\jdk1.7.0_05\bin\java.exe相应解决。

#### 启动nexus

* 启动可以通过查看系统服务，启动nexus服务。 Nexus默认端口是8081，在nexus/conf/nexus.properties中修改。
* 访问`http://127.0.0.1:8081/nexus/`。点击右上角Log In，使用用户名：admin，密码：admin123，可使用repo管理等功能。

### Nexus配置
---
#### Nexus使用代理上网

* 可以用可连接外网的服务器开启代理。如用ccproxy设置“设置”->高级。
* 可以在nexus主页中，左侧的administration->server中，设置http代理选项：Default HTTP Proxy Settings。

### 仓库简介

#### 类型仓库：proxy、hosted、group， 。
	
* proxy是远程仓库的代理。比如说在nexus中配置了一个central repository的proxy，当用户向这个proxy请求一个artifact，这个proxy就会先在本地查找，如果找不到的话，就会从远程仓库下载，然后返回给用户，相当于起到一个中转的作用。
* hosted是宿主仓库，用户可以把自己的一些构件，deploy到hosted中，也可以手工上传构件到hosted里。比如说oracle的驱动程序，ojdbc6.jar，在central repository是获取不到的，就需要手工上传到hosted里。
* group是仓库组，在maven里没有这个概念，是nexus特有的。目的是将上述多个仓库聚合，对用户暴露统一的地址，这样用户就不需要在pom中配置多个地址，只要统一配置group的地址就可以了。
* 还有virtual等类型。

#### 常用仓库

Central是maven默认仓库，一般会配置3个hosted repository，分别是3rd party、Snapshots、Releases，分别用来保存第三方jar（典型的比如ojdbc6.jar），项目组内部的快照、项目组内部的发布版。对于Deployment Policy这个选项，一般Snapshots会配置成允许，而Releases和3rd party会设置为禁止。Apache Snapshots，Codehaus Snapshots等仓库也可以按需添加。

#### 开启远程索引

* 点击左边Administration菜单下面的Repositories，找到右边仓库，在configuration下把Download Remote Indexes修改为true。在仓库上右键，选择Repari Index，这样Nexus就会去下载远程的索引文件。仓库对应的网址如下，可以浏览器访问仓库。
* `Central   http://repo1.maven.org/maven2/` 
* `Apache  https://repository.apache.org/content/repositories/snapshots/`
 
### 项目仓库配置、Maven仓库配置、nexus仓库的关系
---
* 项目仓库配置是maven项目的pom.xml中写的。
* Maven仓库配置是maven软件中settings.xml中写的。
* Nexus仓库配置好后，被maven项目引用或者是被maven软件引用。
* Eclipse中用m2e插件关联起maven软件和maven项目。Eclipse的maven repositories包括：local、global、project和custom的。
* Local对应settings.xml中的设置，global默认对应central仓库默认地址，project对应的是项目的pom文件的设置。Maven repo视图可以在window->show view->other->maven repo中打开。注意：没打开一个工程的时候，project repo可能是空的，eclipse加载项目时会有project repo。
 
### 建立私服仓库
---
#### 上传到第三方库

将类库复制到第三方repo对应的存储文件夹下，建立index即可。可以在browse storage中生成存储index。
 
#### 建立group仓库

建立一个group类型仓库，添加其他仓库，注意左边表示添加到group中。排列顺序按照先后影响仓库查找顺序。注意：本文的示例都是基于下面的group repo设置情况的。
 
### 引用私服仓库
---
#### 仓库请求流程

1.	从central repo下载很多必要的jar和pom，可能被墙而下载了很多.lastUpdate文件。
2.	从私服代理仓库下载jar和pom。Maven在私服中查找，如果私服有就下载到本地，没有时私服就尝试联网下载，下载到了就放到私服并给用户，私服被墙而下载到.lastUpdate文件时，也会传给本地，这就让maven认为所有网上都没有该jar，不会继续查找其他repo。
3.	从没被私服代理的仓库下载jar和pom。如果私服没有找到该jar的任何相关文件，就会从引入的其他仓库中下载（比如使用了一些比较小众的repo或jar包，而私服往往只有几个大的repo地址）。
4.	注意.lastUpdate文件被maven认为是jar包不可获取，所以不会再次尝试下载jar，所以每次update项目应该使用force update snapshot/release。

#### 仓库镜像

* 在maven的settingss.xml中添加对仓库的全局代理。
	* 这可能包括`<servers>`用户登陆密码、`<mirrors>`代理镜像设置、`<profiles>`配置文件设置（含`<repository>`仓库配置、`<pluginRepositories>`插件仓库配置）、`<activeProfiles>`配置文件激活设置。
	* nexus最简单的情况下（仓库不设置密码等情况，这与admin密码不同），将maven对central仓库的请求用nexus的public仓库代理的设置`<mirrorOf>zyy</mirrorOf>`，如果是*就是代理所有。其中的url是nexus中的public仓库的发布路径。
	
```
<mirrors>
   <mirror>  
        <id>tcm</id>  
        <name>tcm repo</name>  
        <url>http://10.15.82.93:8081/nexus/content/groups/public/</url>  
        <mirrorOf>*</mirrorOf>  
     </mirror> 
</mirrors>
```

#### 设置Maven项目的pom.xml

对项目pom进行配置，将repo的url直接对应nexus仓库的发布路径。注意：

* 仓库以id值作为区分，而不是name值。
* 相同的url（相差最后一个/的url其实网址是同一个，可以用浏览器打开试试）指向同一个repo。
* Repo的id除非url相同，否则不应同id，那会产生repo覆盖。Pom中id不应使用central，因为中央仓库默认id为central，这样容易产生混乱。当然及时pom中id为central也不会覆盖全局的central，因为中央仓库优先级最高。

```
	<repositories>
		<repository>
			<snapshots>
				<enabled>true</enabled>
			</snapshots>
			<id>tcm</id>
			<name>tcm Nexus Repository</name>
			<url>http://10.15.82.93:8081/nexus/content/groups/public/</url>
		</repository>
		<repository>
			<id>uk</id>
			<name>central</name>
			<url>http://uk.maven.org/maven2/</url>
		</repository>
		<repository>
			<id>xss-html-filter releases</id>
			<name>xss-html-filter Releases Repository</name>
			<url>http://xss-html-filter.sf.net/releases/</url>
		</repository>
		<repository>
			<id>mvn-repo</id>
			<url>http://maven.ansj.org/</url>
		</repository>
	</repositories>
```

### 部署构件到nexus发布jar包
---
* 部署的意义：为了将各种repo中都没有的jar包放到maven仓库中管理和下载，可以将该jar放到私服上。这个jar可能是比较小众化的，也可能是自己编写的。
* 整体设置推荐
	* 私服使用交换机网关连接外网，最好能翻墙。
	* 下载基本所有的远程仓库文件并添加到第三方库中。所以基本上保证私服含有所有需要的依赖，即使不存在的依赖也可以通过外网下载（翻墙）。
	* 进行构建发布配置。如果构件部署未设置，要发布的话使用文件添加方式。

#### 文件上传到私服

* 直接将jar文件(含pom文件等)复制到nexus仓库storage下对应的GAV，更新index即可。虽然简单，但是只适用于文件少，更新不频繁的操作，因为每次都要找jar相应的文件夹。比较粗暴的方式是上传整个本地仓库，与私服仓库文件夹进行覆盖，但是可能存在上传.lastUpdate文件等情况。
* 适用于私服没翻墙，但是想把一些从墙外下载的包放到私服上。

#### 页面上传到私服

* 直接用admin登录nexus管理界面，找到3rd方仓库，使用页面上传的方式。
* 适用于上传自定义的jar文件。
 
#### 使用maven deploy发布到私服

这种发布方式的缺点在于所有用户都需要配置自己的maven中的server用户信息，优点在于一旦配置之后，可以支持高频率自动化的发布工作。适用于发布项目构件。

* 设置项目pom将构件发布到snapshot。在私服将snapshot设置允许allow deployment，并在group repo中涵盖snapshot仓库，记录snapshot的routing地址。
* 配置项目pom.xml的`<distributionManagement>`。其中包括`<repository><snapshotRepository>` snapshot版本构件设置。
* 设置nexus用户权限。因为发布构建需要用户登录的权限。先管理员登录nexus，在security中的users配置，可以看到admin、anonymous、deployment三个用户，他们的权限privilege trace是不同的，具体权限略。本文选用deployment用户进行登录并发布。在deployment用户右键，设置密码为deployment。
* 在maven软件的settings.xml的`<servers>`中进行用户配置。这里面的id值对应的就是项目pom中设置的zyy-snapshots，填写好登录nexus的用户名和密码如下。
* 用cmd进入maven工程下，运行mvn deploy，进行发布。
 
