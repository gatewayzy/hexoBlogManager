---
title: Git
comments: true
date: 2017-01-12 13:58:07
updated: 2017-01-12 13:58:56
categories: Softwares
tags:
- Git
---

**说明：**Git配置，Git的使用。
<!-- more -->


参考文章：

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



