---
title: Redis
comments: true
date: 2017-01-12 13:58:56
updated: 2017-01-12 13:58:56
categories: 软件配置
tags:
- Redis
---

**说明：**Redis配置，Redis的使用。
<!-- more -->


---
参考文章：[Redis的三种启动方式](http://www.tuicool.com/articles/aQbQ3u)

## 简介
* 内存数据库，cs架构，服务器将数据存到文件，运行时读入内容，采用键值对。

### redis与memcached的比较
* memcached仅支持key-value数据，且不能数据持久化，redis支持key-value、list、set、hash等结构，支持数据持久化，二者擅长点不同

##  server安装
---
### Linux下

* 安装：
	* 下载源码并解压
	* 使用源安装
* 源安装的服务命令：service redisd start | stop | restart...
* 启动server：
	* ./redis-server &，默认端口6379，&是为了后台运行，不使用配置文件（无密码等配置）
	* ./redis-server path/redis.conf & ，指定使用的配置文件
* 查看服务：
	* ps -ef |grep redis
	* ps -aux |grep redis
* 停止server：
	* redis-cli shutdown
	* redis-cli -h 127.0.0.1 -p 6379 shutdown
	* ps -aux |grep redis 然后运行： kill -9 pid


###  Windows下

* 下载压缩包进行解压。
* 作为服务：在目录下运行命令`redis-server --service-install redis.windows.conf` 注意修改相应的redis.config
* 卸载服务`redis-server --service-uninstall`

## server 配置
* 设置server密码：
	* 打开redis.conf配置文件，找到requirepass，然后修改requirepass一行为:requirepass yourpassword
	* 然后使用./redis-server path/redis.conf & ，指定使用的配置文件启动server
* 配置tomcat的session共享
	* 由于redis存在安全漏洞，应确保redis server启用密码。
	* tomcat的lib中添加相应的jar包，如tomcat-redis-session-manager-2.0.0.jar、commons-pool-1.6.jar、jedis-2.1.0.jar
	* 在tomcat的conf/context.xml中配置<Context>添加如下内容。

```
    <WatchedResource>WEB-INF/web.xml</WatchedResource>  
    <Valve className="com.orangefunction.tomcat.redissessions.RedisSessionHandlerValve" />
    <Manager className="com.orangefunction.tomcat.redissessions.RedisSessionManager"
           host="10.15.82.57"
           port="6379"
           password="password"
           database="0"
           maxInactiveInterval="60" />

```
## client使用
---
###  client 连接server
* 运行./redis-cli，默认连接本地的server端口
* 使用可视化管理工具RedisDesktopManager等

### 语法


### Jedis


##  问题与解决
---
### 密码设置
* 修改config文件，使用该配置启动server，上文已详述。












