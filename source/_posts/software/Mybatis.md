---
title: Mybatis
comments: true
date: 2017-01-12 13:58:13
updated: 2017-01-12 13:58:56
categories: Softwares
tags:
- Mybatis
---

**说明：**Mybatis配置使用。
<!-- more -->


---
参考文章：


## Mybatis介绍
---
* iBatis迁移到Google Code 旗下，改名为Mybatis 3.x。

## 使用
---
### 插入数据后的主键
---
* 设置useGeneratedKeys="true" keyProperty="feedbackId"，keyProperty如果是数据表中字段名，无法获取，应设置成对象的名称。

```
	<resultMap type="com.usercenter.pojo.FeedbackPo" id="feedbackMap">
		<id property="feedbackId" column="id" />
		<result property="errorUrl" column="error_url" />
		<result property="title" column="title" />
		<result property="content" column="content" />
		<result property="feedbackSource" column="feedback_source" />
		<result property="anonymous" column="anonymous" />
		<result property="userExtendId" column="user_extend_id" />
		<result property="ip" column="ip" />
		<result property="feedbackTime" column="feedback_time" />
		<result property="count" column="count" />
		<result property="subsysName" column="subsys_name" />
		<result property="sent" column="sent" />
		<result property="solved" column="solved" />
		<result property="solveDever" column="solve_dever" />
		<result property="solveTime" column="solve_time" />
		<result property="category" column="category" />
		<result property="solution" column="solution" />
		<result property="reply" column="reply" />
		<result property="comment" column="comment" />
	</resultMap>

	<insert id="insertOne" parameterType="java.util.HashMap"
		useGeneratedKeys="true" keyProperty="feedbackId" flushCache="true">
		insert into feedback (error_url,title,content,feedback_source
		<if test="anonymous !=null">,anonymous</if>
		<if test="userExtendId !=null">,user_extend_id</if>
		<if test="ip !=null">,ip</if>
		<if test="feedbackTime !=null">,feedback_time</if>
		<if test="count !=null">,count</if>
		<if test="subsysName !=null">,subsys_name</if>
		<if test="sent !=null">,sent</if>
		<if test="solved !=null">,solved</if>
		<if test="solveDever !=null">,solve_dever</if>
		<if test="solveTime !=null">,solve_time</if>
		<if test="category !=null">,category</if>
		<if test="solution !=null">,solution</if>
		<if test="reply !=null">,reply</if>
		<if test="comment !=null">,comment</if>
		)
		values(#{errorUrl},#{title},#{content},#{feedbackSource}
		<if test="anonymous !=null">,#{anonymous}</if>
		<if test="userExtendId !=null">,#{userExtendId}</if>
		<if test="ip !=null">,#{ip}</if>
		<if test="feedbackTime !=null">,#{feedbackTime}</if>
		<if test="count !=null">,#{count}</if>
		<if test="subsysName !=null">,#{subsysName}</if>
		<if test="sent !=null">,#{sent}</if>
		<if test="solved !=null">,#{solved}</if>
		<if test="solveDever !=null">,#{solveDever}</if>
		<if test="solveTime !=null">,#{solveTime}</if>
		<if test="category !=null">,#{category}</if>
		<if test="solution !=null">,#{solution}</if>
		<if test="reply !=null">,#{reply}</if>
		<if test="comment !=null">,#{comment}</if>
		)
	</insert>
```

### Xml使用foreach
---

```
    <select id="selectTopicsByIds" parameterType="list" resultType="com.tcm.dal.qaos.po.TopicPo">
    select  topic_id as topicId,
            topic_title as topicTitle,
            left(topic_time,16) as topicTime,
            reply_cnt as  replyCnt,
            support_cnt as supportCnt,
            oppose_cnt as opposeCnt,
            tags
    from qa_topic
    where topic_id in
	     <foreach item="item" index="index" collection="list" 
                 open="(" separator="," close=")"> 
                #{item} 
         </foreach> 
    </select>
```

## 使用spring管理Mybatis
---
* web.xml中配置spring拦截器

```
	<!-- 派遣器配置 -->
  	<servlet>
   		<servlet-name>dispatcher</servlet-name>
   	  	<servlet-class>
   	  	   org.springframework.web.servlet.DispatcherServlet
   	 	</servlet-class>
   	 	<init-param>
			<param-name>contextConfigLocation</param-name>
			<param-value>/WEB-INF/tcm-servlet.xml</param-value>
	  	</init-param>
   	  	<load-on-startup>1</load-on-startup>
  	</servlet>
 	<servlet-mapping>
  	    <servlet-name>dispatcher</servlet-name>
 	    <url-pattern>/</url-pattern>
	</servlet-mapping>
```

* /WEB-INF/tcm-servlet.xml中导入bean文件

```
	<!-- 导入bean 配置文件 -->
	<import resource="./config/beansContext.xml" />
```

* beansContext.xml中配置mybatis的bean

```
	<!-- 配置mybatis固定的写法 -->
	<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
		<property name="dataSource" ref="dataSource" />
		<property name="configLocation" value="/WEB-INF/mybatis/sqlMapConfig.xml" />
	</bean>
	<bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
		<constructor-arg index="0" ref="sqlSessionFactory"></constructor-arg>
	</bean>
	<!-- data OR mapping interface -->
	<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
		<property name="basePackage" value="com.tcm.module.properties" />
	</bean>
```

* /WEB-INF/mybatis/sqlMapConfig.xml中配置mapper文件目录和别名等

```
	<typeAliases>
		<!-- <typeAlias type="com.bingu.dal.AssociationRulePo" alias="AssociationRulePo" />  -->
		<typeAlias type="com.usercenter.pojo.VisitorsInfoPo" alias="VisitorsInfoPo" />
	</typeAliases>
	
	<mappers>
		<!-- 使用相对于类路径的资源引用,到web应用的resources/路径下对应的xml映射文件 -->
		<mapper resource="dao/ConstsDao.xml" />
		<mapper resource="dao/VisitorsInfoDao.xml" />
	</mappers>
```

* ConstsDao.xml中配置sql

```
<mapper namespace="dao.constsDao">
	<cache type="org.mybatis.caches.ehcache.EhcacheCache" />
	<resultMap type="constsPo" id="constsMap">
		<id property="id" column="id" />
		<result property="internetValue" column="internet_value" />
		<result property="intranetValue" column="intranet_value" />
		<result property="comment" column="comment" />
	</resultMap>

	<select id="selectByName" parameterType="String" resultMap="constsMap">
		select * from const
		where name = #{name}
	</select>
</mapper>
```

* dao代码通过命名空间和参数传递访问数据库

```
@Repository
public class ConstsDaoImpl implements ConstsDao{
	private SqlSession sqlSession;

	@Resource
	public void setSqlSession(SqlSession sqlSession) {
		this.sqlSession = sqlSession;
	}

	@Override
	public ConstsPo selectByName(String name) {
		return sqlSession.selectOne("dao.constsDao.selectByName", name);
	}

	
}
```

* service层使用dao进行业务封装

```
@Service
public class ConstsServiceImpl implements ConstsService {
	@Resource
	private ConstsDaoImpl ConstsDaoImpl;

	@Override
	public boolean isLinux() {
		Properties properties = System.getProperties();
		String os = properties.getProperty("os.name");
		if (os.startsWith("win") || os.startsWith("Win")) {
			return false;
		}
		return true;
	}
}
```

## 使用eclipse的mybatis插件
---
* 使用框架插件，在项目中添加mybatis。一般先建立maven项目，拥有完整的文件目录，如src/main/resources等，新建ORM框架，可能要配置包名为main，或者补全丢失的目录。

