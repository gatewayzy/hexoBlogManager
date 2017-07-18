---
title: Servlet
comments: true
date: 2017-01-12 13:58:19
updated: 2017-01-12 13:58:56
categories: Softwares
tags:
- Servlet
---

**说明：**Servlet配置使用。
<!-- more -->


---
参考文章：

## Servlet简介
---
* Servlet是server let，服务器端小程序，相应的是applet客户端小程序，applet已经被flash等取代，运行在客户端，如浏览器中。Servlet运行在服务器端，如tomcat中，是实现了servlet规范的特殊java类。Java代码都是由web服务器调用的。
* web.xml中的元素执行顺序为：listener监听器>>filter过滤器>>struts拦截器>>servlet。
* 所以是listener加载，再filter加载，再struts拦截器加载，再load-on-startup的servlet加载，web启动完成。正常关闭的时候，销毁的顺序正好是反过来的，先加载的后销毁。

## Servlet生命周期
---
* 第一次访问相应的url后，web.xml反射机制找到servlet并init加载到内存，并调用相应的service服务。init只会调用一次，也就是servlet是单例的，线程不安全。
* 第二次及以后的访问直接调用servlet的service服务。
* 当web工程reload或stop或关机都会调用destroy，清除内存中的servlet。

## Servlet实现
---
* Servlet实现由3中方法：
	* 实现Servlet接口；
	* 继承GenericServlet类；
	* 继承HttpServlet类。

### 实现Servlet接口

实现javax.servlet的几个主要方法: init, getServletConfig, service, getServletInfo, destroy

* 编写Java

```
package servlet;

import java.io.IOException;
import javax.servlet.Servlet;
import javax.servlet.ServletConfig;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;

/**
 * implements Servlet to realize a servlet
 * @author dell
 *
 */
public class MyFirstServlet implements Servlet{
	
	/** 该函数用于初始化servlet，就是把servlet类加载到内存中，该函数之后执行一次
	 * @param config
	 */
	public void init(ServletConfig config) throws ServletException{
		
	}
	
	/**获取servletConfig对象
	 * @return
	 */
	public ServletConfig getServletConfig() {
		return null;
	}
	
	/**该函数是服务函数，实现业务逻辑，每次服务都会被调用
	 */
	public void service(ServletRequest req,ServletResponse res) throws ServletException,IOException{
		System.out.println("hello...");
		res.getWriter().println("hello!");
	}
	
	/**获取servlet的配置信息
	 * @return
	 */
	public String getServletInfo() {
		return null;
	}
	
	/**
	 * 销毁该servlet，从内存中清除，只调用一次
	 */
	public void destroy() {
		
	}

}
```

* 部署servlet（Web.xml）

```
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi=http://www.w3.org/2001/XMLSchema-instance xmlns=http://java.sun.com/xml/ns/javaee xsi:schemaLocation=http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd id="WebApp_ID" version="3.0">
	<display-name>tools</display-name>

	<servlet>
		<servlet-name>nameMyFirstServlet</servlet-name>
		<servlet-class>servlet.MyFirstServlet</servlet-class>
	</servlet>
	<servlet-mapping>
		<servlet-name>nameMyFirstServlet</servlet-name>
		<url-pattern>/gotoMyFirstServlet</url-pattern>
	</servlet-mapping>
</web-app>
```

* 发布项目并访问：发布项目，访问`http://127.0.0.1:8080/javaTools/gotoMyFirstServlet`

### 继承GenericServlet类

继承重写service方法，重写service

* 编写java

```
package servlet;
import java.io.IOException;
import java.util.Date;
import javax.servlet.GenericServlet;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;

public class MyGenericServlet extends GenericServlet {
	@Override
	public void service(ServletRequest request, ServletResponse response) throws ServletException, IOException {
		response.getWriter().println("hello GenericServlet:" + new Date());
	}
}
```

* 部署servlet（web.xml）

```
<servlet>
		<servlet-name>nameMyGenericServlet</servlet-name>
		<servlet-class>servlet.MyGenericServlet</servlet-class>
	</servlet>
	<servlet-mapping>
		<servlet-name>nameMyGenericServlet</servlet-name>
		<url-pattern>/gotoMyGenericServlet</url-pattern>
	</servlet-mapping>
```

* 发布项目并访问：发布web项目并访问/webapp/gotoMyGenericServlet

### 继承HttpServlet

* 编写java

```
package servlet;
import java.io.IOException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
/**
 * 继承HttpServlet，发布项目，访问相应的html直接post
 */
public class MyHttpServlet extends HttpServlet {
	private static final long serialVersionUID = 8020940240259361429L;

	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws IOException {
		response.getWriter().println("doGet...");
	}

	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws IOException {
		response.getWriter().println("doPost: userName = " + request.getParameter("userName"));
	}
}
```

* 部署servlet

```
	<!-- initParam -->
	<context-param>
		<param-name>base</param-name>
		<param-value>/javaTools</param-value>
	</context-param>
<servlet>
		<servlet-name>nameMyHttpServlet</servlet-name>
		<servlet-class>servlet.MyHttpServlet</servlet-class>
	</servlet>
	<servlet-mapping>
		<servlet-name>nameMyHttpServlet</servlet-name>
		<url-pattern>/gotoMyHttpServlet</url-pattern>
	</servlet-mapping>
```

* 发布并访问：发布webapp并访问httpServlet.jsp

```
	<form action="${initParam.base}/gotoMyHttpServlet" method="post">
		用户名：<input type="text" name="userName"> 
		<input type="submit" value="submit">
	</form>
```

* servlet初始化参数
	* 单个servlet的初始化参数可以在<servlet><init-param></servlet>中配置，获取的方法可以是 doGet(){String name = this. getInitParameter(“name”)}

## Servlet 发布细节
---
### Servlet通配符

* 一个servlet可以通过<servlet-mapping>映射到多个url。可以使用通配符，是非法的请求都定位到指定的入口。
* 通配符只有两种形式：
	1. 使用`/*`的方式，比如`/asd/*`、`/123/*`等
	2. 使用`*.拓展名`的方式，比如`*.do`、`*.jsp`、`*.html`，注意拓展名并不代表真是jsp。

### Servlet匹配优先级
 
规则：谁最像(匹配长度越多越优先)，就选中谁；`*.`拓展名优先级最低。

### Servlet同步

* 由于servlet是单例的，同样的请求使用的servlet是同一个（不同的方法），所以存在同步问题，如果servlet存在全局变量，应考虑同步问题。
* 简单的处理方法是synchronized(this) { Thread.sleep(10*1000); }

### load-on-startup

* 工程发布的时候自动调用servlet，启用init方法，在init中进行定时、初始化等业务操作。
* Load-on-startup的servlet可以和JFinal、springMVC等框架可以共同使用的，但是框架中一般都会有实现项目启动就运行的机制，不必使用servlet。

```
<servlet>
		<servlet-name>nameMyFirstServlet</servlet-name>
		<servlet-class>servlet.MyServlet</servlet-class>
		<!-- 工程启动的时候调用此servlet，值为调用顺序 -->
		<load-on-startup>1</load-on-startup>
</servlet>
```
 
## Servlet构建web
---
* 原生servlet构建web的不便之处在于要配置大量的servlet，但是一般有自动生成servlet-mapping配置的插件，减少servlet的个数可以将多种业务放在一个servlet中完成，通过不同的参数区分业务。
* 用web.xml中的servlet-mapping进行路由，一个servlet可以包含多个方法，通过前台请求参数调用不同的方法。使用request.getSeesion.setAttribute()将对象或属性存到服务器中，再使用request.getSession.getAttribute()获取存放的对象。因为session是服务器全局的，所以可以全局使用。从而可以实现一个servlet可以处理多种业务的方法。

## Cookie
---
Cookie是服务器写到浏览器的明文，默认会话级别，浏览器关闭即删除。一个浏览器最多300cookie，一个站点最多20cookie，每个最大40k。

### 添加cookie

```
Cookie cookie = new Cookie(“name”,val);
cookie.setMaxAge(60*60);//s
response.addCookie(cookie);
```

### 读取

```
response.setContentType("text/html;charset=utf-8");
PrintWriter out = response.getWriter();
Cookie[] cookies = request.getCookies();
boolean flag = false;
for (Cookie cookie : cookies) {
	if (cookie.getName().equals("Acookie")) {
		out.println("cookie found，上次登录时间是" + cookie.getValue());
		flag = true;
		break;
	}
}
if (!flag) {
	out.println("没有发现上次登录cookie");
}
```

### 删除

```
cookie.setMaxAge(0)；
response.addCookie(cookie);// 必须写这一句让浏览器执行cookie的销毁动作
```
### cookie添加中文

* cookie不支持存储中文编码，需要后台进行编码，前台解码。
* request正常存放正文，显示可以用jsp直接${userName}。cookie后台添加时使用new Cookie("username",URLEncoder.encode(username,"UTF-8"));，前台js使用alert(decodeURI($.cookie('username'))，因为decodeURI是采用UTF-8来编码的。

### Cookie常用场景

* 常用于：上次登录时间、浏览商品记录、用户登录的用户名和密码(加密后)。
	* 上次登录时间：用户登录时，将时间作为一个cookie值添加到response，注意cookie保存时间`setMaxAge(60*60*24*10)//10天`，0是为了删除，负数是设置为会话级别(关闭浏览器则删除)，cookie默认是会话级别。
	* 记住用户登录的用户名：用户登录时将用户名添加到cookie，在登录框中用户名默认值设置为读取的cookie值。
	* 浏览商品记录：浏览信息写入cookie，在特定页面读取cookie，并获取信息展示到界面上。

## Session
---
### Session基础

* Session属于服务器端，服务器为每个浏览器在内存中存储独有的session。

```
HttpSession session = Request.getSession();//如果有该浏览器对应的session则返回session，没有则为其新建。//如果设置getSession(false)则没有时不新建，返回null
session.setAttribute(“”,””);//添加属性
session.remove(“”);//删除属性
```

* 注意session.setAttribute是服务器中的session的属性，request.getAttribute、request.setAttribute用于与用户进行参数交互。
* 可以存放多个属性，或者对象，重名则覆盖，默认30min，修改工程web.xml配置中servlet-config(min)，存在时间是发呆时间(间隔时间)，一旦使用就重新计时生命周期。或者session.setInterval(second)。或者修改tomcat中的servlet-config修改tomcat中的所有生命时间。
* 关闭项目，reload项目，时间到期，session.invalidate()会使session失效。
* Session有sessionId，服务器为每个浏览器创建一个session，将其id传到JSESSIONID给浏览器的cookie，可以自己添加cookie进行使用。

### Session常用场景

* Session经常用于图片验证、购物对象列表放到session中。但是session放在服务器内存，不能过大使用，否则压力大。
	* 图片验证：产生的随机数放到session中，并将随机数生成图片给页面(注意session中的随机数覆盖，随机图片设置禁止缓存)，验证的时候先验证用户的输入是否与session中的随机数相等。
	* 购物车：将购物车中的数据放到session域中。可以结合hashmap、linkedList存放物品，以进行商品分组、顺序保存。

### Session原理

* 因为session对应每个浏览器的原理是：将session对应的JSESSIONID作为一个cookie传递到浏览器中，session.getId()。而网站的cookie都是自动全部都随着请求发送到服务器的，所以request可以自动根据JSESSIONID进行识别。
* 实现浏览器重启后，仍访问之前的session：实现方法就是将JSESSIONID写到一个cookie中。所以重启后JSESSIONID仍然存在。request可以自动根据JSESSIONID进行识别。Session有对应的path，不同路径下的session重名不覆盖。

#### 禁用cookie后获取会话的JSESSIONID

* Cookie是servlet之间共享session的关键，所以禁用cookie会导致问题。一种解决方法是url重写：

```
request.getSession();//必须访问一下session
String url = “/cart/buyBook?id=1”;
url = response.encodeURL(url)；//此时url将会添加JSESSIONID，变成“/cart/buyBook;jsessionid=454671487asdf8798?id=1”
```
* 将url写到页面上，用户访问该url，后台就可以获取到JSESSIONID。

## ServletContext
---
### servletContext基础

* servletContext在web应用启动的时候自动创建，在服务器中，可以被所有的客户端访问。当应用关闭、tomcat关闭、reload会销毁servletContext。
* servletContext的域对象包括了整个web应用，所以对所有的客户端都是统一的。

### servletContext使用

* 设置使用。ServletContext是对整个web项目的，所以不同的servlet获取到的servletContext是同一个，不同用户或者浏览器也是对应同一个servletContext。

```
public class CreatServletContext extends HttpServlet {
	private static final long serialVersionUID = -4965652427708598938L;
	public void doPost(HttpServletRequest request, HttpServletResponse response) throws IOException {
	}
	public void doGet(HttpServletRequest request, HttpServletResponse response) throws IOException {
		PrintWriter printWriter = response.getWriter();
		response.setContentType("text/html;charset=utf-8");

		/** 获取servletContext对象 */
		ServletContext servletContext = this.getServletContext();//推荐此方法
		// ServletContext servletContext2 = this.getServletConfig().getServletContext();
		/** 添加对象 */
		servletContext.setAttribute("users", new Date().toString());
		printWriter.println("已经将访问时间记录到servletContext中");
		/** 获取对象 */
		String date = (String) servletContext.getAttribute("users");
		printWriter.println("获取到对象：" + date);
		/** 删除对象 */
		servletContext.removeAttribute("users");
	}
}
```

### ServletContext功能

ServletContext功能包括：数据共享、初始化参数、servlet转发、读取资源文件。

#### 多个servlet通过它进行数据共享

因为servletContext对所有servlet是唯一的。

#### 获取web应用的初始化参数

* 所有的servlet都使用的初始化参数配置为<context-param>，获取的方法如下。

```
		response.setContentType("text/html;charset=utf-8");
		PrintWriter printWriter = response.getWriter();
		String name = this.getInitParameter("name");// 本servlet的
		String name1 = this.getServletConfig().getInitParameter("name");// 本servlet的
		String name2 = this.getServletContext().getInitParameter("name");// 全局的
		printWriter.println("servlet的初始化参数name=" + name + name1 + "<br/>servletContext的初始化参数name=" + name2 + "<br>");
```
* 在jsp中，可以使用initParam获取servletContext参数，减少参数修改的麻烦。
* Web.xml中设置一些参数，如下所示。

```
  <context-param>
    <param-name>base</param-name>
    <param-value>/tcm</param-value>
  </context-param>

  <!-- 静态文件基路径 -->
  <context-param>
    <param-name>resources</param-name>
    <param-value>http://zcy.ckcest.cn/zcystatic/tcm</param-value>
  </context-param>
```
* 在jsp中进行参数获取。base会映射为工程发布的`http://ip:port/tcm，resources`会映射成为`http://zcy.ckcest.cn/zcystatic/tcm`，在通过对不同的域名进行代理解析，就可以实现静态文件统一存储到一个服务器上，并使用擅长静态资源服务的apache作为服务器，实现服务与静态资源（css、js、图片等）分离，业务由tomcat等动态服务器提供。

```
<link rel="stylesheet" type="text/css"
	href="${initParam.resources}/resources/commonpages/css/nav_header.css"></link>
<a title="注册" href="${initParam.base}/user/registerpage">注册</a>
```

#### 实现servlet转发

```
this.getServletContext().getRequestDispatcher("/url").forward(request, response);
```

#### 读取资源文件

```
		// 放在webapp/webRoot目录下的读取
		InputStream inputStream = this.getServletContext().getResourceAsStream("/res/servlet/dbRoot.properties");// 以webapp为/
		Properties properties = new Properties();
		properties.load(inputStream);
		String path = this.getServletContext().getRealPath("res/servlet/dbRoot.properties");// 注意如果是tomcat发布会因打包拷贝改变真实路径
		printWriter.print("passwd1=" + properties.getProperty("passwd") + "<br>");
		printWriter.println("绝对路径为：" + path + "<br>");//获取真实路径后方便文件读取

		// 如果文件放在src目录下，应使用类加载器读取
		InputStream inputStream2 = this.getClass().getResourceAsStream("/servlet/db.properties");//以classpath包含的路径为根目录
		//InputStream inputStream2 = this.getClass().getClassLoader().getResourceAsStream("/servlet/db.properties");//默认使用class文件的根目录
		Properties properties2 = new Properties();
		properties2.load(inputStream2);
		printWriter.print("passwd2=" + properties2.getProperty("passwd") + "<br>");
```

### ServletContext Demo

ServletContext常用于：网站在线人数计数器、显示在线用户、简单的聊天系统。一般来说就是既要共享一些数据，但是数据量不大且不希望写入数据库的事情可以用它。

* 网站计数器
	* 将计数放入servletContext中，访问就+1。存在页面刷新就+1的问题，解决方法是用户判定：特定时间内用户唯一计数、用户无限期唯一计数等计数策略。
	* 防止页面刷新导致表单重复提交的简单解决方法是：response.sendRedirect(url);因此登录后的url已经改变，刷新不是提交表单的url。

```
	public void doGet(HttpServletRequest request, HttpServletResponse response) throws IOException, ServletException {
		response.setContentType("text/html;charset=utf-8");
		PrintWriter printWriter = response.getWriter();
		if (this.getServletContext().getAttribute("cnt") == null) {
			printWriter.println("你是第1个访客<br>");
			this.getServletContext().setAttribute("cnt", 1);
		} else {
			int cnt = (int) this.getServletContext().getAttribute("cnt");
			cnt++;
			this.getServletContext().setAttribute("cnt", cnt);
			printWriter.print("你是第" + cnt + "个访客");
		}

	}
```

* servletContext将内容写到文件
	* 方法是：写一个servlet设置为<load-on-startup>，在该servlet的init与destroy中分别为servletContext读取数据和保存数据，注意如果项目不是正常关闭（猫关闭、重启、项目关闭、重启），将会无法调用destroy。
	* 为了防止一些异常情况的退出，可以在init中启动线程，将数据定时写到文件中。
	* 数据文件最好放在项目发布路径中，使用servletContext获取realPath，再进行文件操作。

## Filter
---
### Filter基础

* 过滤器类似于特殊的servlet，常用于身份验证、ip过滤、字符编码、关键字过滤以及自定义过滤。属于链式处理，通过chain.doFilter()执行后面的过滤器或者servlet，但是要特别注意过滤链的执行顺序问题。

### 用法

* 实现servlet中的Filter接口。主要含3个方法体：
	* init()在项目启动的时候会将web.xml中所有的filter进行init。
	* doFilter()对请求和响应进行处理，可以用chain.doFilter(req,res)，执行完过滤链的内部，才能接着执行所在程序。
	* destroy()正常关闭项目时调用filter。
* Filter的链式特点表现在：越早调用的filter越最后退出。

### Filter Demo

#### Web.xml

```
	<!-- filter -->
	<!-- 编码过滤器 -->
	<filter>
		<filter-name>encodingFilter</filter-name>
		<filter-class>servlet.filter.EncodingFilter</filter-class>
		<init-param>
			<param-name>encoding</param-name>
			<param-value>utf-8</param-value>
		</init-param>
	</filter>
	<filter-mapping>
		<filter-name>encodingFilter</filter-name>
		<url-pattern>/*</url-pattern>
	</filter-mapping>
	<!-- 记录请求过滤器 -->
	<filter>
		<filter-name>logFilter</filter-name>
		<filter-class>servlet.filter.LogFilter</filter-class>
	</filter>
	<filter-mapping>
		<filter-name>logFilter</filter-name>
		<url-pattern>/*</url-pattern>
	</filter-mapping>
```

#### EncodingFilter

```
/**
 * 编码过滤器
 * 
 * @author dell
 *
 */
public class EncodingFilter implements Filter {
	private String encoding;
	private Map<String, String> params = new HashMap<>();

	@Override
	public void init(FilterConfig filterConfig) throws ServletException {
		// 在web.xml中被调用后读取参数等设置
		System.out.println("EncodingFilter init，读取相应资源");
		encoding = filterConfig.getInitParameter("encoding");
		System.out.println("encoding: "+encoding);
		for (Enumeration<?> e = filterConfig.getInitParameterNames(); e.hasMoreElements();) {
			String name = (String) e.nextElement();
			String value = filterConfig.getInitParameter(name);
			params.put(name, value);
		}
	}

	@Override
	public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
			throws IOException, ServletException {
		System.out.println("EncodingFilter doFilter before chain.doFilter");
		request.setCharacterEncoding(encoding);
		// 将请求进行链式处理，将根据web.xml的配置执行下一个filter。
		chain.doFilter(request, response);
		// 调用的filter可以再链式处理。链式处理完成后才会继续执行本程序。
		System.out.println("EncodingFilter doFilter after chain.doFilter");
	}

	@Override
	public void destroy() {
		// 项目正常关闭时调用
		System.out.println("EncodingFilter destroy，释放相应资源");
		encoding = null;
		params = null;
	}

}
```

#### LogFilter

```
public class LogFilter implements Filter{
	FilterConfig config;

	@Override
	public void init(FilterConfig filterConfig) throws ServletException {
		// TODO Auto-generated method stub
		System.out.println("LogFilter init");
		this.config = filterConfig;
	}

	@Override
	public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
			throws IOException, ServletException {
		// TODO Auto-generated method stub
		System.out.println("LogFilter doFilter before chain.doFilter");
		ServletContext servletContext = this.config.getServletContext();
		System.out.println("servlet就像特殊的servlet，也可读取servletContext，如：base为"+servletContext.getInitParameter("base"));
		HttpServletRequest httpServletRequest = (HttpServletRequest) request;
		System.out.println("拦截到用户请求url：" + httpServletRequest.getServletPath());
		chain.doFilter(request, response);
		System.out.println("LogFilter doFilter after chain.doFilter");
	}

	@Override
	public void destroy() {
		// TODO Auto-generated method stub
		System.out.println("LogFilter destroy");
	}

}
```

#### 运行demo

* 发布项目时，所有filter被init，输出为：
EncodingFilter init，读取相应资源
encoding: utf-8
LogFilter init
MyHttpServlet中设置初始编码为：utf-8
2016-07-13 16:55:07.920:INFO::Started SelectChannelConnector@0.0.0.0:8080

* 访问配置完整的一个servlet，输出为：
EncodingFilter doFilter before chain.doFilter
LogFilter doFilter before chain.doFilter
servlet就像特殊的servlet，也可读取servletContext，如：base为/javaTools
拦截到用户请求url：/CreateSession
LogFilter doFilter after chain.doFilter
EncodingFilter doFilter after chain.doFilter

* 正常关闭项目（如tomcat的shutdown），输出为：
LogFilter destroy
EncodingFilter destroy，释放相应资源

## Listener
---
### Listener基础

* Listener监听器类似于filter，在web发布时加载，web关闭时销毁。listenter和filter一样，也早于load-on-start的servlet。经常用于数据库配置、日志配置等配置检验。
* 使用方法是实现ServletContextListener接口，主要含2个方法：
	* contextInitialized(ServletContextEvent)初始化，项目发布时就会加载。
	* contextDestroyed(ServletContextEvent)销毁，正常关闭时调用。

### Listener Demo

* web.xml中：

```
	<!-- listener -->
	<listener>
		<listener-class>servlet.listener.TestDBListener</listener-class>
	</listener>
```

* TestDBListener

```
/**测试数据库连接等listener
 * @author dell
 *
 */
public class TestDBListener implements ServletContextListener{

	@Override
	public void contextInitialized(ServletContextEvent sce) {
		System.out.println("TestDBListener contextInitialized，测试数据库配置、日志记录的配置等");
		String base = sce.getServletContext().getInitParameter("base");
		System.out.println("listener也可以读取servletContext的值，如base："+base);
	}

	@Override
	public void contextDestroyed(ServletContextEvent sce) {
		System.out.println("TestDBListener contextDestroyed，项目正常关闭，释放资源等");
	}

}
```

## Servlet转发
---
### sendRedirect与forward

* response.sendRedirect()是重定向，response.forward()是转向。
* response.sendRedirect(“可以是`http://...`”)是让浏览器重新发起一个请求，可以跨网络域，耗费网络资源不应太多，地址栏是变化的。
* response.getRequestDispatcher(“/资源URI”).forward(request,response)是服务器将request转向，只能再同一个工程中，使用的request是一样的，不能跨域，可以无限转向，但是地址栏是不变的。

