---
title: Spring
comments: true
date: 2017-01-12 13:58:19
updated: 2017-01-12 13:58:56
categories: Softwares
tags:
- Spring
---


**说明：**Spring配置使用。
<!-- more -->

---
## Spring简介
Struts是web框架（jsp/action/actionfrom），spring也提供了springMVC。
Hibernate是ORM（object relation mapping）框架，处于持久层。
Spring属于容器框架，用于配置bean，维护bean之间的关系。
 
---
## Spring概念
Bean可以是java中的javabean/service/action/数据源/dao等等
IOC（控制反转，inverse of control）：将创建对象bean和维护对象的关系的权利从程序中转移动spring容器（applicationContext.xml），程序本身不再维护。
DI（dependency injection，依赖注入）：di和IOC实际相同，但开发者认为DI更能体现依赖关系，bean之间的关系用ref关系配置。

## IOC容器
### IOC相关java基础
* 类加载器ClassLoader与类加载机制
	* RootClassLoader、ExtClassLoader、AppClassLoader
	* Class AAClazz = Thread.currentThread().getContextClassLoader().loadClass("com.tcm.AA"); // 使用当前ClassLoader加载用户类
* 反射机制java.lang.reflect
	* class获取构造器Constructor  AA a=AAClazz.getConstructor(null).newInstance(Object... initArgs); //反射获取对应方法签名的构造器，并实例化一个对象
	* class获取方法Method  AAClazz.getDeclaredMethods()获取所有方法，如果是getMethods只能获取public方法，可以getMethod获取非private方法，然后使用setAccessible(true)取消java访问检查，然后才能method.invoke(a,"新的颜色")
		*  Method setColor=AAClazz.getMethod("setColor",String.class); // 获取方法名是setcolor，参数是String的方法
		*  setcolor.invoke(a,"新的颜色");  // Method最重要的就是invoke方法，传入对象和参数
	* class获取成员变量Field AAClazz.getDeclaredFields()获取所有成员变量，getFields()获取public的成员变量，使用field.setAccessible(true)取消java访问检查
		*  Field color = AAClazz.getField("color");
		*  color.set(a,"成员变量的新颜色"); // 设置对象成员变量
	* 反射机制还提供package包反射、annotatedElement注解类反射等等。 

### 资源访问
* 资源加载
	* `classpath：com/module*.xml` classpath只会加载第一个module*.xml
	* `classpath*：com/module*.xml` classpath*会加载所有的`module*.xml`
	* file:/home/  使用相对路径或绝对路径加载文件
	* http:// 
	* ftp://
* 使用Resource接口的实现类解析资源
	* AbstractResource抽象类继承Resource接口
	* ByteArrayResource、ClassPathResource、FileSystemResource以及InputStreamResource、ServletContextResource、URLResource等类实现AbstractResource类。

### BeanFactory 与 ApplicationContext
* IOC的功能任务
	* Spring通过xml文件描述bean与bean之间的依赖关系，利用java反射功能实例化bean，建立bean之间的依赖关系。除了实例化和依赖注入，IOC还提供了bean实例缓存、生命周期管理、bean实例代理、事件发布、资源装载等高级服务。
* IOC容器：BeanFactory是Spring最核心的接口，通常称为IOC容器。ApplicationContext建立在BeanFactory之上，通常称为应用上下文，由于ApplicationContext是对BeanFactory的高层封装，所以ApplicationContext也称为spring容器。
	* BeanFactory，bean工厂是Spring最基础、最核心的接口，提供了各种IOC的配置机制，对应IOC的任务。利用反射功能，BeanFactory可以管理不同类型的java对象。BeanFactory是面向spring框架本身，属于spring底层支撑。
	* ApplicationContext 应用上下文/spring容器，对BeanFactory进行了封装，提供了更多面向应用的服务，面向的是业务开发者，ApplicationContext可以满足绝大部分的业务开发需求。

#### BeanFactory 本文简写为bf
* BeanFactory接口最主要就是getBean(String beanName)方法。其他接口继承BeanFactory接口并进行功能拓展，包括的接口有：
	*  ListableBeanFactory： 接口含有查看bean个数、获取bean配置名、是否包含某个bean等方法
	*  HierarchicalBeanFactory： 父子级联IOC容器的接口，子容器获取父容器等方法
	*  ConfigurableBeanFactory： 一个重要的bean定制接口，有类加载器、属性编辑、容器初始化的后置处理器等方法
	*  AutowireCapableBeanFactory： 有bean按照名字匹配、类型匹配等自动装配的方法
	*  SingletonBeanFactory： 注册单实例Bean的方法
	*  BeanDefinitionRegistry： 提供手工注册bean的方法。spring所有的bean都通过一个BeanDefinition对象表示和配置。
* XmlBeanFactory：通过继承这些接口以及一些抽象类，实现了BeanFactory的类继承体系。最常用的BeanFactory的实现类是XmlBeanFactory。
	*  Resource res = new FileSystemResource(filePath); // 资源加载 BeanFactory bf = new XmlBeanFactory(res); // 容器初始化 Car a=bf.getBean("carA",Car.class); //获取bean
	*  XmlBeanFactory通过Resource加载spring配置信息，并启动IOC容器，然后利用BeanFactory进行getBean。
	*  BeanFactory启动IOC容器时并不会初始化配置文件中定义的Bean，当第一次调用该bean时才会初始化该bean。（启动IOC时，虽然各个实例不会初始化，但是还是要进行类加载检验的，类是否存在、字段是否存在等检验，spring要求必需提供一种日志框架，常见是类路径下提供Log4J配置文件即可）
	*  如果是singleton单实例的bean，首次调用后被初始化，并缓存到一个HashMap(beanName,bean实例)中，第二次调用直接使用缓存的实例。

#### ApplicationContext 本文简写为cxt
* ApplicationContext和BeanFactory的一个重大区别：初始化容器时，bf不会初始化bean实例，调用bean才会相应初始化；ctx会在初始化时，实例化所有单实例的bean，所以初始化时间稍长。
* ApplicationContext接口类的继承体系：cxt接口继承了bf体系的HierarchicalBF和ListableBF接口，也继承了功能拓展的一些接口：
	* ApplicationEventPulisher：容器可以发布应用上下文事件的接口，提供容器启动、关闭等功能。一个bean通过实现ApplicationListener接口监听容器事件；所有的监听器利用抽象类AbstractApplicationContext统一通知。
	* MessegeSource： 支持i18n国际化消息访问接口
	* ResourcePatternResolver： 提供配置加载的接口
	* LifeCycle： 提供start和stop方法，主要同于控制异步处理。
* ctx通过继承这些接口，再派生出众多抽象方法和接口，如CoufigurableApplicationContext接口拓展了ctx接口，提供了refresh和close方法，用于提供ctx的重启(清除缓存，重新装载)和关闭。
	*  ctx最主要的实现类有FileSystemXmlApplicationContext和ClassPathXmlApplicationContext，二者区别在于配置文件的加载路径。
		*  ApplicationContext ctx=new FileSystemApplicationContext(new String[]{"com/tcm/beans.xml","beans2.xml"}); //自动合并多个配置文件 Car a=ctx.getBean("carA",Car.class); //获取bean
	*  ctx为注解类的配置方式提供AnnotationConfigApplicationContext实现类，相比xml配置方式更加灵活。和ctx一样初始化所有bean。
		*  @Configuration public class Beans{@Bean(name="car") public Car getCar(){Car car=new Car();car.setcolor("1");return car;}}
		*  ApplicationContext ctx=new AnnotationConfigApplicationContext(Beans.class); Car car=ctx.getBean("car",Car.class);
	
	*  ctx为web项目提供了WebApplicationContext实现类(记为wctx)。wctx必须依赖于web项目的上下文即ServletContext(记为sc)：wctx存储为servletContext的属性，wctx使用getServletContext获取其引用。
		*  wctx初始化：wctx依赖于ServletContext，所以需要先初始化ServletContext。初始化ServletContext的方法可以使用自启动的servlet或者使用servletContextListener。对应地，spring封装了启动ServletContext的服务为：ContextLoaderServlet、ContextLoaderServlet。二者都可以启动web上下文，并通过web的<context-param>的contextConfigLocation设置spring配置文件，自动把wctx设置到sc的属性中。低版本的servlet规范可能不支持servletContextListener，只能使用自启动的servlet和对应的ContextLoaderServlet。
		*  由于spring的ctx需要先配置日志，所以需要在wctx之前装配log，实现方式类似于wctx：配置<context-param>的log4就、ConfigLocation指定日志配置，使用自启动的Log4jConfigServlet或者使用listener：Log4jConfigLoader加载log。
		*  如果使用的是注解类的bean配置，需要配置<context-param>指定contextClass为AnnotationConfigWebApplicationContext类，指定contextConfigLocation为@Configuration的bean类。

#### 父子容器
* HierarchicalBeanFactory接口提供父子容器的层级关联。为容器添加子容器可以实现特殊的功能。
	* 子容器可以访问父容器的bean，但是父容器不能访问子容器的bean。
	* 容器中的bean的id必须唯一，但是子容器可以拥有和父容器一样的bean。
	* 比如springMVC中，业务层和持久层属于父容器，展现层属于子容器，展现层可以引用业务层和持久层的bean，反过来就不行。

### Bean 生命周期
* web容器中servlet有自己的生命周期，spring容器中bean也有生命周期。
* bean的生命周期可以从bean的实例化和作用范围两个方面探讨。对于BF和ctx我们分开讨论。

#### bf中的bean的生命周期



### ctx中的bean的生命周期



---
## 简单Spring项目
### 最小配置包
Spring包括aop、beans、core、jdbc、web等等.
写日志包commons-logging.jar.

### 创建spring核心文件applicationContext.xml
一般放置在src目录下，在maven中放到src/java/main下，此目录默认为classpath。Struts有核心struts-config.xml，hibernate有核心hibernate.cfg.xml。
applicationContext.xml路径设置方法：
1. Spring配置文件在WEB-INF根目录下，可以不进行配置，因为spring会默认去加载，如果一定要配置呢，可以这样
contextConfigLocation为WEB-INF/applicationContext.xml
2. Spring配置文件在WEB-INF下的某个文件夹下，比如config下，可以这样配置
contextConfigLocation为WEB-INF/config/applicationContext.xml
3. Spring配置文件在src根目录下面，可以这样配置
contextConfigLocation为WEB-INF/classes/applicationContext.xml
或者contextConfigLocation为classpath:applicationContext.xml
4. Spring配置文件在src下的某个包里，比如com.config，可以这样配置
contextConfigLocation为WEB-INF/classes/com/config/applicationContext.xml
或者contextConfigLocation为classpath:com/config/applicationContext.xml

### 配置bean
Bean中以id为唯一值，在java中根据id获取。可以理解成将java程序在xml加载过程中进行实例化。依赖注入使用ref依赖bean的id表示。

### Java反射
Spring的xml在加载的时候利用反射机制，和实际运行java代码相似，然后将实例化对象放入内存，但是必须要给目标的对象设置setter和getter方法，spring才能实现。

### spring代码demo
S1.SayByeService.java

```
package com.service;
public class SayByeService {
	private String name;
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
		public void sayBye() {
		System.out.println("Bye~ " + name);
	}
}
```

S2.UserService.java 引用了SayByeService

```
package com.service;
public class UserService {
	private String name;
	private SayByeService sayByeService;
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
		public SayByeService getSayByeService() {
		return sayByeService;
	}
	public void setSayByeService(SayByeService sayByeService) {
		this.sayByeService = sayByeService;
	}
	public void sayHello() {
		System.out.println("Hello! " + name);
		sayByeService.sayBye();
	}
}
```

S3. applicationContext.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:mvc="http://www.springframework.org/schema/mvc" xmlns:p="http://www.springframework.org/schema/p"
	xmlns:cache="http://www.springframework.org/schema/cache" xmlns:aop="http://www.springframework.org/schema/aop"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:context="http://www.springframework.org/schema/context"
	xmlns:util="http://www.springframework.org/schema/util" xmlns:task="http://www.springframework.org/schema/task"
	xmlns:oxm="http://www.springframework.org/schema/oxm" xmlns:tx="http://www.springframework.org/schema/tx"
	xmlns:beans="http://www.springframework.org/schema/beans"
	xsi:schemaLocation="http://www.springframework.org/schema/mvc
http://www.springframework.org/schema/mvc/spring-mvc-3.1.xsd
http://www.springframework.org/schema/beans
http://www.springframework.org/schema/beans/spring-beans-3.1.xsd
http://www.springframework.org/schema/context
http://www.springframework.org/schema/context/spring-context-3.1.xsd
http://www.springframework.org/schema/task 
http://www.springframework.org/schema/task/spring-task-3.1.xsd
http://www.springframework.org/schema/cache 
http://www.springframework.org/schema/cache/spring-cache-3.1.xsd
http://www.springframework.org/schema/oxm 
http://www.springframework.org/schema/oxm/spring-oxm-3.1.xsd
http://www.springframework.org/schema/tx 
http://www.springframework.org/schema/tx/spring-tx-3.1.xsd
http://www.springframework.org/schema/aop
http://www.springframework.org/schema/aop/spring-aop-3.1.xsd
http://www.springframework.org/schema/util
http://www.springframework.org/schema/util/spring-util-3.1.xsd">
	<!-- 配置spring bean -->
	<!-- id 用于被获取，不可重复 -->
	<beans:bean id="userService" class="com.service.UserService">
		<!-- name 和 value体现出了注入 -->
		<beans:property name="name">
			<beans:value>使用spring输出的zyy</beans:value>
		</beans:property>
		<!-- ref 体现依赖注入 -->
		<beans:property name="sayByeService" ref="sayByeServiceBean"></beans:property>
	</beans:bean>
	<beans:bean id="sayByeServiceBean" class="com.service.SayByeService">
		<beans:property name="name" value="使用spring输出的小明"></beans:property>
	</beans:bean>
</beans>
```

S4.test.java

```
package com.test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import com.service.SayByeService;
import com.service.UserService;
public class test {
	public static void main(String[] args) {
		// 传统方法调用
		UserService userService = new UserService();
		userService.setName("传统方法的zyy");
		SayByeService sayByeService_0 = new SayByeService();
		sayByeService_0.setName("传统方法的zyy_小明");
		userService.setSayByeService(sayByeService_0);
		userService.sayHello();
		System.out.println("1 is over!");
		// 传统方法调用
		SayByeService sayByeService = new SayByeService();
		sayByeService.setName("传统方法的小明");
		//sayByeService;
		sayByeService.sayBye();
		System.out.println("2 is over!");
		// spring的方法实例化
		ApplicationContext ac = new ClassPathXmlApplicationContext("applicationContext.xml");
		// 获取sayByeService
		SayByeService sayByeService2 = (SayByeService) ac.getBean("sayByeServiceBean");
		sayByeService2.sayBye();
		System.out.println("3 is over!");
		UserService userService2 = (UserService) ac.getBean("userService");
		userService2.sayHello();
		System.out.println("4 is over!");
	}
}
```

---
## DI配合接口编程
Web层和业务层之间通过接口进行访问，实现解耦，将接口使用spring进行管理。

S1.TestMain.java

```
// 使用spring实现接口在beans.xml中进行转换
		ApplicationContext ac = new ClassPathXmlApplicationContext("com_2/beans.xml");
		// 选择激活beans.xml中不同的changeLetter即可实现不同的接口实现，实现spring对业务控制
		ChangeLetter changeLetter =  (ChangeLetter) ac.getBean("changeLetter");
		changeLetter.change();
		System.out.println("3. spring的不同实现方法");
```

S2.beans.xml

```
<!-- 两个 bean 的 id 不能相同，通过激活对接口 ChangeLetter 的不同的实现类，可以达到不同的业务逻辑 -->
	<beans:bean id="changeLetter" class="com_2.service.LowerLetter">
		<beans:property name="string" value="Struts 3.0 "></beans:property>
	</beans:bean>
	<!-- 
	<beans:bean id="changeLetter" class="com_2.service.UpperLetter">
		<beans:property name="string" value="Struts 3.0 "></beans:property>
	</beans:bean>
	  -->
```

S3.ChangeLetter.java接口和两个实现类LowerLetter.java、UpperLetter.java。



---
## Spring MVC

### Service层

service一般分为interace与implement，代码结构一般是：
* interface中：
	* baseinterface //放通用的方法，用public Object findByKey(Class class，Serializabled key)等通用类型较好
	* aaainterface extends baseinterface //关于aaa的方法，继承了baseinterface的方法
	* bbbinterface extends baseinterface

* implement中：
	* baseimpl implements baseinterface //将接口方法实现为abstract，让aaaimpl，bbbimpl继承此类时实现了baseinterface的接口
	* aaaimpl extends baseimpl implements aaainterface //实现aaainterface并且继承了实现了baseinterface的baseimpl方法
	* bbbimpl extends baseimpl implements aaainterface

* 通过使用接口与继承，可以提升代码重用性，降低耦合：
	* interface中aaa、bbb继承了base的基础接口方法，不用写很多个接口中的方法；
	* implement中aaa、bbb需要实现base接口及父接口中的方法，只需继承baseimpl的实现就可以实现base接口中的方法。
* 调用service的时候使用接口，以支持动态加载：
	* aaainterface ain = new aImpl();
	* Aaa = ain.findByKey(Aaa.class,10);

### Controller控制器

#### 页面请求

使用@RequestMapping进行路由，可以路由到方法级别，也可以路由到类的级别。但是如果写完整的页面路径会比较繁琐，可以使用InternalResourceViewResolver类设置默认的路径补全。

```
	<!-- spring的xml中设置jsp解析路径，自动补全前后缀 -->
    <bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
		<property name="prefix" value="/WEB-INF/views/" />
		<property name="suffix" value=".jsp" />
	</bean>
```

```
	@RequestMapping(value = "/kcpage", method = RequestMethod.GET) // 代码中设置对应的路value
	public String getConclusionsPage() {
		return "qaos/analysis/kc_page";
	}
```

#### ajax请求

使用@ResponseBody处理ajax请求，返回map、json类型数据。
```
	@RequestMapping(value = "/kcsearch", method = RequestMethod.GET)
	@ResponseBody
	public Map<String, Object> getConclusions(HttpServletRequest request,
			HttpServletResponse response, @RequestParam(value = "query", defaultValue = "") String name) {
		Map<String, Object> map = new HashMap<String, Object>();
		map.put("object", qakConclusionService.queryKConclusion(name));
		return map; // return new Gson().toJson(map) 
	}
```

#### ModelAndView

```
ModelAndView中添加返回页面和参数，return modelAndView。
	@RequestMapping(value = { "/", "/index", "/toIndex" })
	public ModelAndView index(HttpServletRequest request, HttpServletResponse response) {
		ModelAndView modelAndView = new ModelAndView();
		List<MenuPo> rootList = MenuServiceImpl.queryRoot();
		modelAndView.setViewName("index/index");
		modelAndView.addObject("rootList", rootList);
		return modelAndView;
	}
```

#### ModelMap
ModelMap可以传参数，并且不需要return该map就可自动传递到前台，同时使用return返回界面。

```
@RequestMapping(value = { "", "/", "home" }, method = RequestMethod.GET)
    public String home(ModelMap modelMap, HttpServletRequest request,
            HttpServletResponse response) throws IOException {
        JSONArray yearArray = adrAnalysisService.getYearStatistic();
        JSONObject freqObject = adrAnalysisService.getKeyFreq();
        modelMap.put("adrList", freqObject.get("adr"));
        modelMap.put("drugList", freqObject.get("med"));
        modelMap.put("yearAnalysisResult",
                JSONFormatter.JSON2AdrYearAnalysisHighchart(yearArray));
        return "adrhome";
    }
```

---
## Interceptor拦截器

拦截器按照配置文件中出现的先后顺序进行拦截；拦截全部请求时使用"/**"；拦截应只针对controller. 

### 配置拦截器拦截规则
```
	<mvc:interceptors>
		<!-- 直接使用全局拦截器。开发环境下,伪登录 -->
		<bean class="com.tcm.web.interceptor.user.FakeSessionInterceptor">
			<property name="uid" value="1"></property>
		</bean>
		<bean class="com.tcm.web.interceptor.user.UserAuthInterceptor" />
		<mvc:interceptor>
			<mvc:mapping path="/search/**" />
			<bean class="com.tcm.web.interceptor.security.AntiCrawlerInterceptor" />
		</mvc:interceptor>
		<mvc:interceptor>
			<!-- 使用局部拦截器。不会拦截/user/1/,会拦截/user/1/file -->
			<mvc:mapping path="/user/?*/?**/**" />
			<mvc:exclude-mapping path="/user/?*/user_logout" /> 
			<bean class="com.tcm.web.interceptor.user.UserInfoInterceptor" />
		</mvc:interceptor>
		
	</mvc:interceptors>
```

### 拦截器代码

拦截器实现HandlerInterceptor接口，常在preHandle中进行逻辑处理，返回boolean，如果是true则拦截器放行，否则不能访问设置的路由方法。

```
public class UserInfoInterceptor implements HandlerInterceptor {

	@Override
	public void afterCompletion(HttpServletRequest arg0,
			HttpServletResponse arg1, Object arg2, Exception arg3)
			throws Exception {
	}

	@Override
	public void postHandle(HttpServletRequest arg0, HttpServletResponse arg1,
			Object arg2, ModelAndView arg3) throws Exception {
	}

	/**
	 * 用户操作的权限管理; object: 下一个拦截器;
	 */
	@Override
	public boolean preHandle(HttpServletRequest request,
			HttpServletResponse response, Object object)
			throws ServletException, IOException {
		/* 获得用户session */
		UserSessionPo userSessionPo = (UserSessionPo) request.getSession()
				.getAttribute(UserConst.USER_SESSION_NAME);
		/* 未登录用户 */
		if (null == userSessionPo) {
			response.sendRedirect(request.getContextPath() + "/user/denypage");
			return false;
		}
		/* 放行 */
		return true;
	}
}
```

---
## Listener监听器

### ApplicationListener监听程序启动

通过实现ApplicationListener接口进行启动监听。

```
@Component
public class StartupListener implements ApplicationListener {
	@Resource
	private FeedbackLuceneServiceImpl FeedbackLuceneServiceImpl;

	// 工程发布的时候启用线程
	@Override
	public void onApplicationEvent(ApplicationEvent e) {
		/**
		 * 只在项目启动的时候,启动一个线程
		 */
		if (e instanceof ContextRefreshedEvent)
			new Thread(new LuceneFeedback()).start();
	}

	private class LuceneFeedback implements Runnable {
		@Override
		public void run() {
			try {
				System.out.println("测试生成反馈索引");
			} catch (Exception e) {
				e.printStackTrace();
			}
		}
	}
}
```

---
## @Scheduled定时任务

### 开启扫描注解
spring.xml多加下面的内容启用定时功能，代码使用@Component和@Schedule。

```
xmlns:task="http://www.springframework.org/schema/task"  

http://www.springframework.org/schema/task  
http://www.springframework.org/schema/task/spring-task-3.1.xsd  
```

```
<!-- 启用task扫描注解 -->
<task:annotation-driven />
```

### 任务代码

```
@Component
public class Test {
	@Resource
	private ConstsServiceImpl constsServiceImpl;
	
	@Scheduled(cron = "0/5 * * * * ?")//每隔5秒执行一次
	public void doit(){
		String logPath =  constsServiceImpl.getStringByName("visitor_log");
		String logName = "visitor111.log";
		try {
			FileReader fr = new FileReader(logPath + logName);
			BufferedReader br = new BufferedReader(fr);
			String s = br.readLine();
			while (s != null) {
				System.err.println(s);
				s = br.readLine();
			}
			br.close();
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
}
```

---
## @Aspect切面编程
### 开启切面注解
spring.xml多加下面的内容启用切面功能，代码使用@Component和@Aspect和切片@Around，@Before，@After等。

```
xmlns:aop="http://www.springframework.org/schema/aop"

http://www.springframework.org/schema/aop
http://www.springframework.org/schema/aop/spring-aop-3.1.xsd
```

```
	<!-- 强制使用CGLIB代理 -->
	<aop:aspectj-autoproxy proxy-target-class="true"/>
```

### 切片代码

```
@Component
@Aspect
public class LogAOP {

    @Before("execution(* com.sr.web.controller..*.*(..))")
    public void before(JoinPoint joinPoint) {
        Signature signature = joinPoint.getSignature(); // 签名信息
        System.out.println("请求参数：" + Arrays.toString(joinPoint.getArgs())); // 请求参数
        System.out.println("开始运行：" + signature.getName());
    }

    @After("execution(* com.sr.web.controller..*.*(..))")
    public void after(JoinPoint joinPoint) {
        Signature signature = joinPoint.getSignature(); // 签名信息
        System.out.println("请求参数：" + Arrays.toString(joinPoint.getArgs())); // 请求参数
        System.out.println("结束运行：" + signature.getName());
    }

    /**
     * 环绕通知需要携带ProceedingJoinPoint类型的参数
     * 环绕通知类似于动态代理的全过程：ProceedingJoinPoint类型的参数可以决定是否执行目标方法
     * 且环绕通知必须有返回值，返回值即为目标方法的返回值
     */
    @Around("execution(* com.sr.web.controller..*.*(..))")
    public Object around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
        Signature signature = proceedingJoinPoint.getSignature(); // 签名信息
        System.out.println("请求参数：" + Arrays.toString(proceedingJoinPoint.getArgs())); // 请求参数
        long start = System.currentTimeMillis();
        Object object = proceedingJoinPoint.proceed(); // 执行方法，必须将返回值return
        long costTime = System.currentTimeMillis() - start;
        System.out.println("方法运行时间：" + signature.getName() + " " + costTime);
        return object;
    }
}
```