---
title: Nginx
comments: true
date: 2017-01-12 13:58:14
updated: 2017-01-12 13:58:56
categories: Softwares
tags:
- Nginx
---

**说明：**Nginx配置使用。
<!-- more -->


参考文章：[Nginx配置文件详细说明](http://www.cnblogs.com/xiaogangqq123/archive/2011/03/02/1969006.html)

## 简介
---
* 俄罗斯人开发的高性能服务器，支持负载均衡（Load Banlance）、反向代理、IMAP/POP3/SMTP。
* 反向代理（Reverse Proxy）方式是指用代理服务器来接受internet上的连接请求，然后将请求转发给内部网络上的服务器，并将从服务器上得到的结果返回给internet上请求连接的客户端，此时代理服务器对外就表现为一个反向代理服务器。


## Nginx使用
---
### Nginx安装配置
---
* Linux安装：sudo apt-get install nginx
* 配置文件目录：/etc/nginx/nginx.conf。
* 配置文件有events、http、mail等配置模块。
* http配置中可以设置多个虚拟主机，每个主机server可以监听多个域名、ip，并对server设置反向代理的location。其中，location可以指向 `http://upstream` 或者 `http://ip:port`，如果使用upstream可以配置多个反向代理server以实现负载均衡。

```
user www-data;
worker_processes 4;
pid /run/nginx.pid;

events {
	worker_connections 768;
	# multi_accept on;
}

http {

	##
	# Virtual Host Configs
	# load banlance
	##
	upstream ecs {
		ip_hash;
		server 10.174.226.188:4000;
	}

	upstream hexo_blog{
		ip_hash;
		server 10.174.226.188:4000;
		#server 127.0.0.1:8082;
	}

	# server config
	server {
		listen 80;
		server_name 121.42.200.178 10.174.226.188 www.bebetter.site bebetter.site;
	
		location / {
        	        proxy_redirect    off;
			proxy_set_header    Host $host;
	                proxy_set_header    X-Real-IP $remote_addr;
        	        proxy_set_header    X-Forwarded-For $proxy_add_x_forwarded_for;
			proxy_pass   http://127.0.0.1:4000;
		}

		location ~ ^/blog {
			proxy_pass http://hexo_blog;
			proxy_set_header    Host $host;
	                proxy_set_header    X-Real-IP $remote_addr;
        	        proxy_set_header    X-Forwarded-For $proxy_add_x_forwarded_for;
		}
	}

	include /etc/nginx/conf.d/*.conf;
	include /etc/nginx/sites-enabled/*;
}


#mail {
#	# See sample authentication script at:
#	# http://wiki.nginx.org/ImapAuthenticateWithApachePhpScript
# 
#	# auth_http localhost/auth.php;
#	# pop3_capabilities "TOP" "USER";
#	# imap_capabilities "IMAP4rev1" "UIDPLUS";
# 
#	server {
#		listen     localhost:110;
#		protocol   pop3;
#		proxy      on;
#	}
# 
#	server {
#		listen     localhost:143;
#		protocol   imap;
#		proxy      on;
#	}
#}
```

### Nginx命令
---
* 运行状态控制：sudo service nginx restart|stop|start|status|…
* 查看端口：netstat –ntlp |grep nginx， 或者ps -aux
* 修改配置文件：sudo vi /etc/nginx/nginx.conf


### Nginx配置多个虚拟主机
---
* 参考文章
	* [Virtual Hosts on nginx](https://gist.github.com/soheilhy/8b94347ff8336d971ad0)
	* [Nginx反向代理后端多个Tomcat、Nginx+PHP服务器](http://7424593.blog.51cto.com/7414593/1762432)
* 需求描述：有多个ip:port需要使用一个nginx进行代理。比如说想用域名mydomain.com代理几个nodejs工程10.10.10.10:4000、10.10.10.22:8000.
* 解决方法



