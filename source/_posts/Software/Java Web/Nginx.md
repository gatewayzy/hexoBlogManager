---
title: Nginx
comments: true
date: 2017-01-12 13:58:14
updated: 2017-01-12 13:58:56
categories: Softwares
tags:
- Java web
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
### Nginx安装与启动
---
* Ubuntu安装Nginx： `sudo apt-get install nginx`
* 运行状态控制：`sudo service nginx restart|stop|start|status|…`
* 查看端口： `netstat –ntlp |grep nginx` ， 或者`ps -aux`


### Nginx配置
---
* 修改配置文件：`sudo vi /etc/nginx/nginx.conf`， 配置文件含有events、http、mail等配置模块。
	* 通用配置模块：nginx服务的processor、pid等配置。
	* events：连接数设置等。
	* http：用于http服务。http下面一般包括：
		* upstream：负载均衡模块，可以有很多upstream。每个upstream有一个别名，内部默认使用ip_hash算法从多个server中提供负载均衡服务。
		* 可以设置多个虚拟主机server，对于每一个server：
			* server必须监听一个Nginx服务器的端口，通常是http的80、https的443.
			* server可以绑定多个域名、ip，用空格分开。
			* server可以代理多个location。location可以指向 `http://upstream` 或者 `http://ip:port`。location的配置见下文。
		* include：添加配置文件，如果server很多，为每个server写一个配置文件，放到对应目录下，添加到sites-enabled文件。

### location 的配置与使用 
参考文章[nginx 正则表达式匹配入门篇](http://blog.csdn.net/a519640026/article/details/9138487)

#### location 匹配命令
* ` ~ `   表示执行一个正则匹配，区分大小写
* ` ~* `  表示执行一个正则匹配，不区分大小写
* ` ^~ `  表示普通字符匹配，如果该选项匹配，只匹配该选项，不匹配别的选项，一般用来匹配目录
* ` = `   进行普通字符精确匹配
* ` @ `   定义一个命名的 location，使用在内部定向时，例如 error_page, try_files
* `^`	匹配字符串的开始位置
* `$`	匹配结束位置
* `\`	转义字符。可以转. * ?等
* `*`	代表任意字符，`.` `*` `?` 与常用正则匹配相似


#### location匹配优先级:
* =前缀的指令严格匹配这个查询。如果找到，停止搜索。
* 所有剩下的常规字符串，最长的匹配。如果这个匹配使用^〜前缀，搜索停止。
* 正则表达式，在配置文件中定义的顺序。
* 如果第3条规则产生匹配的话，结果被使用。否则，使用第2条规则的结果。（类似于：匹配到了就使用，匹配不到就使用最长匹配）


#### location高级用法
* if指令
* proxy_redirect
* proxy_pass
* rewrite
* ……



### Nginx配置文件示例

```
#### nginx配置文件 /etc/nginx/nginx.conf
user www-data;
worker_processes 4;
pid /run/nginx.pid;

events {
	worker_connections 768;
	# multi_accept on;
}

http {
	#### http 里面可以设置upstream负载均衡，默认用ip_hash算法进行负载均衡
	upstream stream_hexo_blog{
		ip_hash;
		server 10.174.226.188:4000;
		server 127.0.0.1:8082;
	}

	#### server 1，由于监听80，可访问bebetter.site:80/blog的形式
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

		location ~ ^/blog {  # 地址重定向，参考nginx的location匹配说明
			proxy_pass http://stream_hexo_blog;   # 使用upstream负载均衡
			proxy_set_header    Host $host;
	        proxy_set_header    X-Real-IP $remote_addr;
        	proxy_set_header    X-Forwarded-For $proxy_add_x_forwarded_for;
		}
	}

	#### server 2，由于监听81，可访问bebetter.site:81/assets的形式
	server {
            listen 81;
            proxy_cache_valid  200  20m;
            proxy_max_temp_file_size   0k;
            server_name bebetter.site;

            location ~ ^(/|/assets) {
                   proxy_pass http://10.15.62.118:4000;
                   proxy_set_header    Host $host;
                   proxy_set_header    X-Real-IP  $remote_addr;
                   proxy_set_header    X-Forwarded-For $proxy_add_x_forwarded_for;
            }
            access_log  off;
            error_log   off;
     }
	
	#### 如果配置文件太长，或者有多个server，可以使用下面的include分开配置
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




### 问题与解决
---
#### Nginx配置多个虚拟主机
* 需求描述：有多个ip:port需要使用一个nginx进行代理。比如说想用域名mydomain.com代理几个nodejs工程10.10.10.10:4000、10.10.10.22:8000。
* 解决方法：为nginx创建多个server，监听不同的端口（80、81等），每个server的/proxy为对应的ip:port，可参考nginx配置示例，这样访问nginx的不同server端口即可。缺点在于浏览器访问端口不是80端口了。



