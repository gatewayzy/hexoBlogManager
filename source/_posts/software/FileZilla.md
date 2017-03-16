---
title: FileZilla
comments: true
date: 2017-01-12 13:58:06
updated: 2017-01-12 13:58:56
categories: Softwares
tags:
- FileZilla
---

**说明：**FileZilla配置，FileZilla的使用。
<!-- more -->


运行环境：Windows

## FileZilla简介
* FileZilla是基于C语言的免费开源FTP软件，分为客户端和服务器端。

## 安装与启动
---
### Client客户端
---
* 安装FileZilla FTP Client客户端，访问FTP站点即可

### Server服务器
---
* 安装FileZilla server服务器端即可。
	* FileZilla Server.exe用于安装和启动FTP服务，推荐设置自启动。
	* FileZilla Server Interface.exe用于连接FTP服务器，并对服务器进行管理和设置，如用户和目录。默认链接127.0.0.1不需要密码。不需要设置自启动。
 
## 配置与用户管理
---
### 客户端配置
---
* 站点管理器中存储FTP站点信息和设置。这些设置包括用户名、密码、字符集等等。
* Ctrl s  打开站点管理器快捷键。

### 服务器端配置
---
* 配置一个ftp服务器的步骤为：启动FileZilla Server启动FTP服务，启动FileZilla Server Interface连接FTP服务器，设置用户名、密码、group和相应的访问路径和权限。
* 注意事项：设置防火墙规则。服务器配置在FileZilla Server.xml中，包括服务器角色配置信息、访问控制信息、密码等。连接本地ip的时候，使用127.0.0.1比较好，ip的话可能连不上。
 
## 问题与解决
---
### Server提示文件layout.xml无法加载
---
* 版本描述文件缺损，重新安装或者直接拷一份别人的放进去。

### Client设置允许保存密码
* 是否允许保存密码的选项在设置->界面->行为（不要保存密码）。
