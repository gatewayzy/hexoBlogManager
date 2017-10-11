---
title: MacOS常用配置与软件
comments: true
date: 2017-01-12 13:58:15
updated: 2017-01-12 13:58:56
categories: OS
tags:
- OS
---

**说明：**MacOS常用配置与软件。
<!-- more -->


* 运行环境：MacOS Sierra 10.12.5

---
## 快捷键
|快捷键|说明
|:---:|:---:|
| alt command shutdown |锁定
| fn  delete | 向后删除
| cmd k | 连接服务器
| cmd delete |删除文件
| cmd q  |关闭当前软件
| cmd z/c/v/s... | 撤销／复制／粘贴／保存...
| cmd w / t / shift t  |safari的关闭/新建/打开刚才的页面，浏览器快捷键与Windows下相似，cmd代替ctrl
| ctr 单击 |就是右键，或者双指轻点
| cmd |多选，类似于control
| cmd delete |删除文件等
| cmd 上下箭头  |打开上下级目录，在finder等中快速到顶端或底部
| cmd o |打开文件／文件夹
| alt cmd space| 打开finder的搜索

---
## 软件

### 软件安装
软件安装可以从app store搜索并安装，或者网上下载安装。
* 如果是.app类型，直接将其拖到finder的“应用程序”目录下。
* 如果是.pkg或者.dmg，都直接双击安装。

###  常用软件
* ftp： 使用finder的cmd+k连接服务器。
* 远程桌面： ssh可以用命令行，vnc使用vncviewer，微软桌面使用Microsoft Remote Desktop（难得找到一个[下载页面](http://bbs.feng.com/read-htm-tid-10464436.html)）。
* markdown编辑器： 为了安装macdown先安装homebrew： 　`/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)”` 再安装macdown： `brew cask install macdown`
* shadowsocks： 下载[ShadowsocksX-NG](https://github.com/shadowsocks/ShadowsocksX-NG)，配置wifi的代理，指定自动代理的pac即可，可参考[Safari 如何快速切换代理服务器？](https://www.zhihu.com/question/20818790)
* nodejs和npm安装：  官网下载nodejs安装，完成试验：`npm -v`

---
##脚本与命令

###  terminal命令
* open a.app 运行一个app
* lsof -i :4000 查看一个端口对应的进程，然后使用kill -9 pid关闭进程

### 脚本示例
* 写一个脚本：关闭占用4000端口的进程，然后运行hexo s，发布在4000端口。
```
#!/bin/sh
echo "==================== shutdown pid for port 4000 =============="
ID=`lsof -i:4000 |tail -1 | awk '{print $2}'`
echo $ID
for id in $ID
do
  kill -9 $id
  echo "killed $id"
done
echo "==================== hexo s & ===================="
cd ~/Projects/www/hexoBlogManager
hexo s &
```

### 自动运行脚本
__注意脚本权限chmod u+x。__

* 系统偏好设置--用户与群组--登录项--添加运行脚本即可。
* ～／.bash_profile 是用户个人配置，用户新建一个terminal就会运行该脚本。不存在可新建该文件。修改即可生效。可以使用`source  bash_profile `强制刷新。  

---
## 系统配置

### 环境变量
* 设置用户环境变量
	* jetty 不区分平台。
	* jdk从官网下载，运行安装之后自动加载到环境中，安装后找到里面的jdk可复用，添加到用户环境变量中。
	* maven从官网下载。在用户文件中～／.bashrc_profile中添加maven环境变量：

```
M3_HOME=/Users/zhangyangyang/Opt/apache-maven-3.5.0
export M3_HOME

PATH=$M3_HOME/bin:$PATH
export PATH
```

