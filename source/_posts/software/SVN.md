---
title: SVN
comments: true
date: 2017-01-12 13:58:19
updated: 2017-01-12 13:58:56
categories: Softwares
tags:
- SVN
---

**说明：**SVN配置，SVN的使用。
<!-- more -->


参考文章：

## SVN简介
---
* SVN包括客户端和server端。推荐用TortoiseSVN + VisualSVN Server搭配。
* 客户端有VisualSVN、TortoiseSVN等。其中VisualSVN集成了Subversion和Apache，与Visual Studio集成在一起，但是需要收费；VisualSVN是免费自由软件，它是一个Windows平台下的Subversion用户端软件，以Windows shell extension的方式写成。
* 服务器端有Subversion、VisualSVN Server等。其中Subversion需要自己配置文件控制权限、一般还要配置Apache进行http web访问；VisualSVN Server免费，且集成了Subversion、Apache和用户权限管理等。

## Subversion与apache2做Server
---
* Linux下没有VisualSVN server软件，可以使用subversion作为服务器，但是只支持ftp和svn检索方式，不支持http和https检索方式。如要支持http和https，可以使用apache服务与subversion结合。

### 安装使用Subversion
---
* 安装软件：		sudo apt-get install subversion
* 创建一个repo：	svnadmin create /home/myrepo
* 创建好的repo可以checkout下来然后commit初始化代码。
* 配置svn服务的配置文件svnserver.conf文件： vi /svn/project/conf/svnserve.conf

```
[general]
anon-access = none
auth-access = write
password-db = /svn/project/conf/passwd
authz-db = /svn/project/conf/authz
realm = My Test Repository         #这是个提示信息
```
* 添加两个访问用户及口令： vi /svn/project/conf/passwd

```
[users]
xiaoran.shen = 123456
test1 = 123456
test2 = 123456
```
* 分配用户权限：	vi /home/svn/authz.conf
* 权限配置分析：配置分组，对每个repo的不同分组设置不同的权限，示例如下。

```
[groups]
vip = wang
admin = wangqixuan, yang
develop-manager = zhong
business-manager = shi
developer = ma, hu, zhou, cen, huang

[repos:/]
@admin = rw
* = r

[repos:/develop/repo1]
@vip =rw
@admin = rw
@develop-manager = rw
* =
[repos:/develop/repo1/repo2]
@vip =rw
@admin = rw
@develop-manager = rw
zhou = rw
hu =rw
ma = r
* =
```
`*=`表示其他用户没有任何权限，@用户组，*用户，[/]表示全部目录，用户“a=r,b=r”这种不能合并成“a,b=r”。

### Subversion与apache结合
----
在subversion基础上使用apache服务，可以支持http、https检索方式，并且配置用户时可以使用apache的用户配置，为所有项目进行统一配置，从而不必像subversion那样专门为每一个repo配置权限。
* 安装apache2 服务：		sudo apt-get install apache2 
* 安装apache2 帮助命令：	sudo apt-get install apache2-utils 
* 添加用户：	htpasswd -c /home/svn/passwd.conf user1（注意-c表示清除已有用户，普通添加不要写-c，用户的密码存储方式为MD5）
* 打开apache的dav_svn插件：sudo a2enmod dav_svn
* 配置dav_svn的svn配置：`vi /<apache-dir>/mods-enable/dav_svn.conf`，去掉 `<Location /svn>` 的注释并加入下面设置，修改相应的svn目录、名称和密码目录等，Basic表示用户方式访问。

```
  DAV svn
  SVNParentPath  /svn
  AuthType Basic
  AuthName "svn repos"
  AuthUserFile /svn/passwd
  Require valid-user
```
* 重启apache服务：sudo service apache2 restart，之后就可以正常使用svn客户端进行访问。
* 可能提交的时候遇到写权限问题，可以尝试对svn项目仓库的文件权限进行修改。将www-data（apache的默认用户名）加入到登录的组中，将登录的组设置仓库权限，命令为：`sudo chmod -R g+rwx svns`，这条命令允许所在组用户对版本库进行读写操作，-R是递归，g是group 


## VisualSVN Server做Server
---
### Server设置
---
运行bin下的VisualSVN Server.msc打开服务器管理界面，进入设置界面即可。如果server启动失败可以用server安装程序进行repair。
* 用户管理：server设置 ->authentication添加用户名和密码。
* 存储管理：server设置 ->storage

### 仓库管理
---
建立新的仓库并初始化项目空间可以如下操作。
* Server中建立仓库。
* 将server对应的仓库url复制下来（注意将url修改为正确信息），在本地上checkout出来一个空的项目目录。
* 将本地项目代码复制到空的项目目录中，进行commit操作。

### VisualSVN Server仓库迁移到Linux
---
如果svn服务器发生变化，需要对将svn进行迁移，迁移包含所有版本, 权限，用户信息。
* 进入visualSVN server存储项目的repositories目录下，`svnadmin dump usercenter > mysave.dump`（确定要备份的目录下有版本信息的format文件）
* 在subversion中创建一个repo，将dump备份拷贝到subversion机器中
* 将dump备份导入repo中：`svnadmin load myrepo < mysave.dump`
* 将客户端的地址更新：右键要更改的已有仓库->TortoiseSVN->Relocate->在To URL这一项里填：`http://10.15.82.52/svn/myrepo` ->确定。然后就可以从新地址进行checkout了。或者进入项目目录下运行如下命令，再通过命令svn info查看是否成功。
`svn switch --relocate https://10.15.82.58/svn/usercenter/ http://10.15.82.52/svn/usercenter`


## TortoiseSVN客户端使用
---
### 导入工程
---
新建文件夹，右键checkout，设置server的URL和本地路径，输入用户名密码。当然还可以设置导入的版本、勾选文件等，点show log可以查看日志。一般只下载源码等，服务器中和下载时都不要加入编译生成的文件（添加到ignore list）。

### Update与commit
---
* 更新与提交，一般先更新检查，看是否存在文件冲突，再提交。
* 如果存在冲突，可以先将本地代码剪切到其他地方，下载最新的版本，将自己代码冲突的文件融合进去，再提交。

### 增改删操作
---
* 在文件或目录上右键，TortoiseSVN->Add，Delete等，点OK，之后再commit。因为只有commit才会提交到服务器。
* 如果想让一个文件恢复到修改之前的版本，可以TortoiseSVN->revert。

### 分支管理
---
1. 创建一个项目，下面有trunk，branch，tag文件夹。
2. 在trunk中添加初始化的代码工程，trunk就是主干版本。
3. 在trunk上右击，选择创建branch/tag，如选取创建到/trunk/proj-1.0.0，然后update将会下载该分支内容。

### 工程代码管理
---
* 项目右键选项说明：在项目源码文件夹上右键，可进行svn客户端的各种操作，部分说明如下。

```
Show log 看日志
Repo-browser 看仓库文件目录
Check formodifications 看文件修改
Revision graph 
Resolve 处理版本冲突问题
Update to version 版本选择
Revert 放弃修改，本地的版本working copy（WC）会恢复到你修改前的状态，也就是说你最近的一次update后的版本。
Clean up 删除文件副本
Get lock 锁定文件，commit动作会自动锁定文件。
Release lock 释放文件锁
Branch/tag 
Switch 
Merge 
Export 
Relocate 
Add 
Unversion and add to ignore list 
Create patch 
Apply patch 
Properties 属性
Setting 配置
```

* 文件右键说明：在项目文件上进行右键可以进行各种操作，部分说明如下。

```
Diff with previous version 和之前版本进行文件对比
Delete 删除本地文件，当提交本地版本后将同步删除服务器中的该文件
Blame 查找修改过的该文件的人
```

## SVN的命令行操作
---
* svn checkout URL （默认最新version）
* svn checkout https://10.15.82.58/svn/usercenter/  #第一次检出会出现密码等交互设置
* svn cleanup
* svn update

## 问题与解决
---
### 强制commit操作提供messenge
---
可以在客户端和服务器端分别设置。
* 客户端设置（不推荐）只会在本地机器上生效，并不会影响其他分支:项目副本右键选择properties，添加一个log size设置即可。
* 服务器端设置（推荐）
	* 参考文章：[SVN与TortoiseSVN修改日志和强制写日志实现详解](http://www.cnblogs.com/chengxiaohui/articles/3780481.html)
	* 在server库的hooks目录下编写脚本（其实就是批处理文件）文件：pre-commit.bat，内容如下。想修改限制长度只需要增加或减少".............................."中点的数量。

```
@echo off 
setlocal
set REPOS=%1
set TXN=%2
rem check that logmessage contains at least 10 characters
"D:\Program Files\VisualSVN Server\bin/svnlook" log "%REPOS%" -t "%TXN%" | findstr ".........." > nul
if %errorlevel% gtr 0 goto err
exit 0
:err
echo 提交必须写10个字以上的日志!>&2
exit 1
```

### Can't convert string from 'UTF-8' to native encoding
---
* 原因是文件目录使用了中文，SVN在Ubuntu下，ubuntu设置为utf-8，svn中就不能有中文文件路径。
* 方法1：可以改成英文文件名（推荐）
* 方法2：添加linux系统编码：需要修改`/home/.bash_profile`文件(可能要新建)就可以，添加下面几行，然后 `source .bash_profile`更新生效运行。

```
export LC_ALL=en_US.UTF-8
export LANG=en_US.UTF-8
export LANGUAGE=en_US.UTF-8
```

### svn不是内部或外部命令
---
* 原因是安装svn客户端没有安装svn command line
* 运行TortoiseSVN安装程序，在modify中勾选上svn command line，或者一开始安装的时候就勾选上。





