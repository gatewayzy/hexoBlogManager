---
title: JFinal
comments: true
date: 2017-01-12 13:58:10
updated: 2017-01-12 13:58:56
categories: Softwares
tags:
- Java web
---

**说明：**JFinal配置使用。
<!-- more -->

---
参考文章：

## JFianl简介
JFinal是基于Java语言的极速WEB+ORM框架,其核心设计目标是开发迅速、代码量少、学习简单、功能强大、轻量级、易扩展、Restful。

## 配置
### web.xml
必须启用JFinalFilter进行项目路由。

```
<web-app>
<!-- 静态文件基路径 -->
	<context-param>
		<param-name>resources</param-name>
		<param-value>/templateJFinalBSApp</param-value>
	</context-param>
<filter>
    <filter-name>jfinal</filter-name>
    <filter-class>com.jfinal.core.JFinalFilter</filter-class>  //jfinal内部类
    <init-param>
      <param-name>configClass</param-name>
      <param-value>config.TemplateConfig</param-value>  //自己的config类
    </init-param>
  </filter>
  <filter-mapping>
    <filter-name>jfinal</filter-name>
    <url-pattern>/*</url-pattern>  //拦截的url
  </filter-mapping>
</web-app>
```
### config
---
config类中进行ORM映射、常量设置、路由设置、拦截器设置等。

```
public class TemplateConfig extends JFinalConfig{

	@Override
	/*常量值*/
	public void configConstant(Constants me) {
		// TODO Auto-generated method stub
		//这些都可以使用JFinal自动生成模板，再参考JFianl手册
		me.setDevMode(true);//选择开发模式，输出每次请求报告
		me.setEncoding("UTF-8");//项目编码
		me.setViewType(ViewType.JSP);//选择默认视图类型为jsp
	}

	/*访问路由*/
	@Override
	public void configRoute(Routes me) {
		// TODO Auto-generated method stub
		me.add("/", IndexController.class);
		me.add("/index", IndexController.class);
		me.add("/classes",ClassesController.class);
		me.add("/student", StudentController.class);
	}

	@Override
	/*插件*/
	public void configPlugin(Plugins me) {
		// TODO Auto-generated method stub
		/*读取配置文件，默认路径为 src/main/resources*/
		loadPropertyFile("mysqlTempl.txt");
		/*启用C3p0数据库连接池插件，也可以使用DruidPlugin插件*/
		C3p0Plugin cp = new C3p0Plugin(getProperty("url"), getProperty("user"), getProperty("password"));
		me.add(cp);
		/*启用ActiveRecord数据库访问插件*/
		ActiveRecordPlugin arp = new ActiveRecordPlugin(cp);
		me.add(arp);
		/*配置数据库访问*/
		arp.addMapping("student", "stuID",Student.class);
		arp.addMapping("classes","claID", Classes.class);
	}

	@Override
	public void configInterceptor(Interceptors me) {
		// TODO Auto-generated method stub
		/*全局拦截器*/
		
	}

	@Override
	public void configHandler(Handlers me) {
		// TODO Auto-generated method stub
		me.add(new ContextPathHandler("basePath"));
	}

}
```

### Controller
---
Controller主要进行业务控制。

```
public class ClassesController extends Controller{
public void index(){
		this.render("/index.html");
	}
	
	public void login() {
		setAttr("notice", "test");
		renderText("IndexController.login()");
	}
	
public void test() {  // 数据分页
		Map<String, List> map =new LinkedHashMap<String, List>();
		int pageNumber = 1;
		int pageSize = 2;
		int totalpage;
		int totalRow;
		if(getParaToInt("pageNumber") != null){
			pageNumber = getParaToInt("pageNumber");
		}
		Page<Classes> classes = Classes.dao.paginate(pageNumber, pageSize, "select * ", "from classes where claPeople >?",20);
		totalpage = classes.getTotalPage();
		totalRow = classes.getTotalRow();
		Classes list = classes.getList().get(0);
		map.put("list", classes.getList());
		renderJson(map);  // 直接返回给前台的ajax请求
	}
	
	@ActionKey("/actionKey")
	public void lo() {
		this.renderText("通过@ActionKey访问此方法");
		forwardAction("/classes/query");
		/*forwardAction只会后台处理，前台的URL不变
		 * redirect前台后台都会变，即前台重新请求*/
	}
}
```

#### Db与Record
---

```
Record newUser = new Record().set("user_name", userName)；
Db.save("user_count_info", newUser);
Long ii = newUser.get("id");//必须使用id这一字段，与表的主键名无关
int ID = ii.intValue(); //获取插入的数据的主键，需要注意是否长度溢出
```

#### Model与dao
---

```
User user = User.dao.findFirst("SELECT * FROM `user_count_info` WHERE id = ? ", id);
```

### Model
---
Model中常常设置全局静态实例化dao对象进行数据库ORM操作。

```
public class Classes extends Model<Classes>{
	
	/*获取更多方法，请查阅JFinal的Model类介绍*/
	public static final Classes dao = new Classes();
	/*dao对象是全局共享的，只能用于数据库查询（查改删），
	 * 不能用于数据承载（增），数据承载需要使用new Classes().set("name","James").save()*/

	/**
	// 创建name属性为James,age属性为25的User对象并添加到数据库
	new User().set("name", "James").set("age", 25).save();
	// 删除id值为25的User
	User.dao.deleteById(25);
	// 查询id值为25的User将其name属性改为James并更新到数据库
	User.dao.findById(25).set("name", "James").update();
	// 查询id值为25的user, 且仅仅取name与age两个字段的值
	User user = User.dao.findById(25, "name, age");
	// 获取user的name属性
	String userName = user.getStr("name");
	// 获取user的age属性
	Integer userAge = user.getInt("age");
	// 查询所有年龄大于18岁的user
	List<User> users = User.dao.find("select * from user where age>18");
	// 分页查询年龄大于18的user,当前页号为1,每页10个user
	Page<User> userPage = User.dao.paginate(1, 10, "select * ", "from user where age > ?", 18);
	 * */
	
}
```

### jsp
---

```
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
<%@ taglib prefix="c"  uri="http://java.sun.com/jsp/jstl/core" %>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<script type="text/javascript" language="javascript" src="${initParam.resources}/static/js/jquery.min.js"></script>
<script type="text/javascript" language="javascript" src="${initParam.resources}/static/js/jquery-2.1.1.min.js"></script>
<script type="text/javascript" language="javascript" src="${initParam.resources}/static/js/jquery.pagination.js"></script>
<script type="text/javascript">
$(document).ready(function () { 
	var maxentries = 7;			//总数据条数
	pageselectCallback(0);
	$("#panel").pagination(maxentries, {	//此方法一次性加载，分页切换无刷新与延迟
	    num_edge_entries: 2,	//边缘页数
	    num_display_entries: 4,	//主体页数
	    callback: pageselectCallback,//回调函数
	    items_per_page:1,		//每页显示1项
	    prev_text:"上一页",
	    next_text:"下一页",
	    ellipse_text:".."		//省略的页数用什么文字表示
	    
	});

function pageselectCallback(page) { 
    var result = ""; 
    $.ajax({ 
        type: "post", 
        dataType: "json", 
        url: "classes/test", //请求的url 
        data: { "pageNumber": parseInt(page + 1) }, 
        success: function (data) {
        	//alert(data);
        	var content = "";
        	$.each(data.list,function(i,ite){
        		var tmp="";
        		tmp +="&nbsp;"+ite.claID;
        		tmp +="&nbsp;"+ite.claName;
        		tmp +="&nbsp;"+ite.claCharger;
        		tmp +="&nbsp;"+ite.claPeople;
        		tmp +="<br>";
        		result += tmp;
        	})
        	$("#result").html(result);
        },
        error:function(textStatus){
        	alert("error!");
        	console.info(msg);
        }
  
});
} 
}); 
</script>
<title>test</title>
</head>
<body>
	<table border="1">
		<tr>
			<td>claID</td>
			<td>班级名称</td>
			<td>负责人: ${notice}</td>
			<td>班级人数</td>
		</tr>
	</table>
	<div id="result"></div>
	<div id="panel"></div>

	<%@ include file='./common.jsp' %>
</body>
</html>
```
