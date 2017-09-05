---
title: SCM
comments: true
date: 2017-01-12 13:58:19
updated: 2017-01-12 13:58:56
categories: Softwares
tags:
- SCM
---

**说明：**SCM (Source Code Management)的配置使用，包括Git、SVN等。
<!-- more -->


# Git
--------------------------------------------------------------------------------------------------------

## 简介
---
* Git属于项目管理软件，类似与SVN实现版本管理。
* 一些代码托管平台都支持git、svn等版本管理，如github、oschina等。



## Git使用
---
* 参考文章：[Git 常用命令大全](http://blog.csdn.net/dengsilinming/article/details/8000622)

### 安装
---
* Windows下Git安装：安装git for windows。
* ubuntu下安装：apt-get install subversion，使用见svn介绍。

### 远程仓库相关命令
---

```
检出仓库：$ git clone git://github.com/jquery/jquery.git
查看远程仓库：$ git remote -v
添加远程仓库：$ git remote add [name] [url]
删除远程仓库：$ git remote rm [name]
修改远程仓库：$ git remote set-url --push [name] [newUrl]
拉取远程仓库：$ git pull [remoteName] [localBranchName]
推送远程仓库：$ git push [remoteName] [localBranchName] 
如果想把本地的某个分支test提交到远程仓库，并作为远程仓库的master分支，或者作为另外一个名叫test的分支，如下：
$git push origin test:master         // 提交本地test分支作为远程的master分支
$git push origin test:test              // 提交本地test分支作为远程的test分支
```

### 分支(branch)操作相关命令
---

```
查看本地分支：$ git branch
查看远程分支：$ git branch -r
创建本地分支：$ git branch [name] ----注意新分支创建后不会自动切换为当前分支
切换分支：$ git checkout [name]
创建新分支并立即切换到新分支：$ git checkout -b [name]
删除分支：$ git branch -d [name] ---- -d选项只能删除已经参与了合并的分支，对于未有合并的分支是无法删除的。如果想强制删除一个分支，可以使用-D选项
合并分支：$ git merge [name] ----将名称为[name]的分支与当前分支合并
创建远程分支(本地分支push到远程)：$ git push origin [name]
删除远程分支：$ git push origin :heads/[name] 或 $ gitpush origin :[name]
创建空的分支：(执行命令之前记得先提交你当前分支的修改，否则会被强制删干净没得后悔)
$git symbolic-ref HEAD refs/heads/[name]
$rm .git/index
$git clean -fdx 
```

### 版本(tag)操作相关命令
---

```
查看版本：$ git tag
创建版本：$ git tag [name]
删除版本：$ git tag -d [name]
查看远程版本：$ git tag -r
创建远程版本(本地版本push到远程)：$ git push origin [name]
删除远程版本：$ git push origin :refs/tags/[name]
合并远程仓库的tag到本地：$ git pull origin --tags
上传本地tag到远程仓库：$ git push origin --tags
创建带注释的tag：$ git tag -a [name] -m 'yourMessage' 
```

### 子模块(submodule)相关操作命令
---

```
添加子模块：$ git submodule add [url] [path]
添加子模块如：$git submodule add git://github.com/soberh/ui-libs.git src/main/webapp/ui-libs
初始化子模块：$ git submodule init  ----只在首次检出仓库时运行一次就行
更新子模块：$ git submodule update ----每次更新或切换分支后都需要运行一下
```
删除子模块：（分4步走）
* $ git rm --cached [path]
* 编辑“.gitmodules”文件，将子模块的相关配置节点删除掉
* 编辑“ .git/config”文件，将子模块的相关配置节点删除掉
* 手动删除子模块残留的目录 

### 忽略一些文件、文件夹不提交
---
在仓库根目录下创建名称为“.gitignore”的文件，写入不需要的文件夹名或文件，每个元素占一行即可，如

```
target
bin
*.db
```

### Git其他常用命令
---

```
git branch 查看本地所有分支
git status 查看当前状态 
git commit 提交 
git branch -a 查看所有的分支
git branch -r 查看本地所有分支
git commit -am "init" 提交并且加注释 
git remote add origin git@192.168.1.119:ndshow
git push origin master 将文件给推到服务器上 
git remote show origin 显示远程库origin里的资源 
git push origin master:develop
git push origin master:hb-dev 将本地库与服务器上的库进行关联 
git checkout --track origin/dev 切换到远程dev分支
git branch -D master develop 删除本地库develop
git checkout -b dev 建立一个新的本地分支dev
git merge origin/dev 将分支dev与当前分支进行合并
git checkout dev 切换到本地dev分支
git remote show 查看远程库
git add .
git rm 文件名(包括路径) 从git中删除指定文件
git clone git://github.com/schacon/grit.git 从服务器上将代码给拉下来
git config --list 看所有用户
git ls-files 看已经被提交的
git rm [file name] 删除一个文件
git commit -a 提交当前repos的所有的改变
git add [file name] 添加一个文件到git index
git commit -v 当你用－v参数的时候可以看commit的差异
git commit -m "This is the message describing the commit" 添加commit信息
git commit -a -a是代表add，把所有的change加到git index里然后再commit
git commit -a -v 一般提交命令
git log 看你commit的日志
git diff 查看尚未暂存的更新
git rm a.a 移除文件(从暂存区和工作区中删除)
git rm --cached a.a 移除文件(只从暂存区中删除)
git commit -m "remove" 移除文件(从Git中删除)
git rm -f a.a 强行移除修改后文件(从暂存区和工作区中删除)
git diff --cached 或 $ git diff --staged 查看尚未提交的更新
git stash push 将文件给push到一个临时空间中
git stash pop 将文件从临时空间pop下来
git remote add origin git@github.com:username/Hello-World.git
git push origin master 将本地项目给提交到服务器中
git pull 本地与服务器端同步
git push (远程仓库名) (分支名) 将本地分支推送到服务器上去。
git push origin serverfix:awesomebranch
git fetch 相当于是从远程获取最新版本到本地，不会自动merge
git commit -a -m "log_message" (-a是提交所有改动，-m是加入log信息) 本地修改同步至服务器端 ：
git branch branch_0.1 master 从主分支master创建branch_0.1分支
git branch -m branch_0.1 branch_1.0 将branch_0.1重命名为branch_1.0
git checkout branch_1.0/master 切换到branch_1.0/master分支
mkdir WebApp
cd WebApp
git init  初始化git工程
touch README  创建文件
git add README 添加要提交的文件
git commit -m 'first commit'
git remote add origin git@github.com:daixu/WebApp.git
git push -u origin master
```

## Github使用
* 创建public库，private需要收费，可以自己搭建gitLab替代github。
* maven提交到库中的代码只需要src和pom.xml。

### 本地登录SSH认证
---
参考文章：[Git版本管理 本地登录SSH认证](http://jingyan.baidu.com/article/6d704a13171c7428db51cacd.html)
* 生成本地ssh公钥。打开git bash终端，输入ssh-keygen -t rsa -C "github账号邮箱地址"。执行前述命令后若成功则会提示在用户文件夹下生成了ssh公钥的文件。是否成功可以通过访问文件夹 .ssh 来确定，若有此文件夹则说明生成成功。
* 在资源管理器中打开这个.ssh文件夹，在它下面会看到两个文件，选择id_rsa.pub的文件并用记事本打开，复制这个文件中的所有内容。打开浏览器登陆github，在自己的账户面板下找到SSH keys这一栏，打开后即会看到目前该账户下已进行过SSH认证的机器，选择Add SSH key之后，将前一步复制的内容粘贴至Key中，同时需要编辑一个Title来说明此Key认证的是哪一台机器，通常会使用计算机的名字。
* 保存后，回到git bash中，键入 ssh git@github.com进行连接认证，其中有一步会询问是否确定进行连接，需要键入yes。
* 在完成认证后，即可将git上的开源项目或自己账号下的私有项目clone到本地。同一台机器（或虚拟机）只需要进行一次SSH认证即可。

### 项目分支
---
* 需要多人开发时可以使用分支，比如要贡献其他人的项目。
* 对项目进行fork，自己的repo中就会创建一个分支，对自己repo中的分支进行修改和更新。
* 自己的repo更新后，点击pull request向原始项目管理人员申请提交。

### 个人分支更新
---
#### 克隆项目到本地
由于此时项目只是存在与远程GitHub，所以必须克隆到本地用于开发，执行如下命令即在本地创建了一个项目的克隆。
* `git clone https://github.com/username/Spoon-Knife.git`	#https方式
* `git clone git@github.com:gatewayzy/importDB.git`	#ssh方式

#### 配置本地库与原始库的关联
---
由于克隆后的本地库的只有一个与自己的GitHub上的分支关联的名为origin的远程，所以要提交自己的更新到原始库，必须配置与原始库 `https://github.com/octocat/Spoon-Knife` 的关联，名为upstream，执行如下：

```
cd importDB	#进入本地克隆的项目
git remote add upstream https://github.com/octocat/Spoon-Knife.git	#添加与原始库的关联，名为upstream
git fetch upstream	#从原始库上抓取最新更新
```

#### 修改代码并提交和PUSH
修改项目下的README文件作为测试，添加一行“first change.”，提交操作可能需要用户名和密码。

```
git pull #检查远端更新
git status #查看工程中的文件状态
git diff filename #查看文件对比
git add logs/ #将添加logs文件夹下的所有文件 git add –A 表示全部提交
git commit -a -m 'Update README'  #-a表示add，已添加的修改
git push origin master	#提交push到远程自己的项目分支库
git fetch upstream	#抓取远程原始库的更新
git merge upstream/master	#将抓取的更新合并到本地的库中
```

## 问题与解决
---
### 强制覆盖本地文件
---

```
git fetch --all  
git reset --hard origin/master 
git pull
```

### Git提交无权限
---
* 问题描述：提交有 `fatal: empty ident name (for <pms@yhd-jqhadoop39.int.yihaodian.com>) not allowed`
* 解决方法：设置user.email和user.name即可
	* $git config --global user.name "ouyangyewei"
	* $git config --global user.email "ouyangyewei@yhd.com"


# SVN
--------------------------------------------------------------------------------------------------------
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
* svn add * 提交所有更新
* svn commit -m "message"  更新到服务器
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









