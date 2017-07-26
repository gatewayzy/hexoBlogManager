---
title: Ubuntu
comments: true
date: 2017-01-12 13:58:21
updated: 2017-01-12 13:58:56
categories: Softwares
tags:
- Ubuntu
- OS
---

**说明：**Ubuntu配置，Ubuntu的使用。
<!-- more -->

参考文章：

## 常用命令
---

* 初始设置root密码:sudo passwd root,修改密码：passwd
* su – 切换到root，-表示加载root的环境变量，su user 切换到user账户
* 新建一个terminal,按键：  ctr+alt+t  中断：ctrl+Z 退出：ctrl+C
* 命令帮助，man（manual），如：man cd
* login:用户名，密码; 以root登录：su; 退出root:exit; 退出用户：logout
* 改变当前路径：cd,到根目录为cd /,上一级为cd ..,当前一级为cd .
* 磁盘使用情况：df -a
* 安装iptux.deb软件包sudo dpkg -i iptux.deb（其中-i等价于--install）
* 自启动程序终端运行：gnome-session-properties
* 显示当前所在目录：pwd
* ifconfig查看/设置网卡 netstat查看网络连接状态 route查看/设置路由
* ping检查网络是否可达 tcpdump抓包命令
* ./hello 为什么在可执行文件前加./，与PATH环境变量相关，env或echo $PATH
* 添加路径export PATH=$PATH:/home/test
* 可以使用任何文本编辑器,比如gedit 文件名
* 查看安装的有desktop程序的命令：ls /usr/share/applications/
* 切换已打开程序：alt+tab
* 查看之前安装过旧版本：dpkg -l | grep qq
* 显示隐藏的qq界面:Ctrl + Alt + z  退出QQ：qq -k  添加桌面图标：dash图标拖拽
* 推荐分区：/8g,/boot500M,/var4g,/usr15g(程序),/home20g(用户下载文件)
* MP3播放列表乱码：进入mp3文件所在目录，然后运行：mid3iconv -e GBK *.mp3 
* 在linux命令行中使命令行程序后台运行，在命令后加个&即可，如启动fcitx并使其后台运行：fcitx &
* ls; pwd; passwd root 使用分号可以运行多个命令，属于shell编程
* 设置命令别名：alias ll='ls -l'
* service --status-all  显示所有服务
* date  日期时间
* ps -A 显示所有运行中的进程
* shell编程
	* echo "passwd" | sudo -S reboot可以实现脚本中执行sudo，-S sudo从标准输入读取密码
	* nohup ./test & 用&后台运行，用nohup保证关闭当前shell不会关闭运行中的程序
	* jobs  显示当前shell中的所有作业信息，包括运行状态等，-l列出pid、作业号，-r运行中的，-s指stopped
	* bg 2 以后台模式重启作业号为2的作业
	* fg 以前台模式重启默认作业，可指定作业号
	* nice renice用于调整作业优先级
	* at -f test.sh +25 定时任务25分钟后运行文件中的命令，结合atq查看列表，atrm删除
	* crontab -l  显示cron定时任务时刻表，-e编辑时刻表
	* anacron 可以弥补cron停机期间错过定时任务的问题
	* `$?` 返回上一条执行语句的状态返回值，`$0`代表程序名，`$1`为第一个参数类推，`$#`表示参数个数（不含程序名）
	* read -p "hello" a 输出hello并读取用户输入存放到a变量
	* 文件描述符fd：每个进程最多9个fd，且0是STDIN，1是STDOUT，2是STDREE，可进行重定向
		* lsof 查看所有文件描述符，可能被隐藏，用/usr/sbin/lsof全路径才能运行
		* exec 4>&1 创建fd4存放默认的fd1，exec 1>test 将fd1的输出指向test， exec 1>&4 还原fd1默认值，exec 4>&- 关闭fd4  
		* 安装shetool含有常用的shell命令和函数等，用于shell编程
	* sed流编辑器，每次读取一行进行命令匹配和修改，结果输出到STDOUT
		* sed 's/dog/cat/;s/big/small/' data1 将data1文件内容所有cat换为dog输出，不影响原文件
		* sed -f script1.sed data1 用文件中的规则处理一个文件
		* sed 还有删除d、插入i、追加o、修改c、转换y等功能
		* sed 支持N、D、P等多行文本操作
	* gawk是awk的gnu版本，提供了流编辑器的编程语言，支持复杂编程和格式化输出，处理也是按行输入处理，`$0`表示整行，`$1`表示第一个字段（默认用空白符分割字段）
		* gawk '{print "hello " $1;$2="bb"}' data1 输出hello 第一个字段
		* gawk 'BEGIN {print "before dealing: "}{print $0}' data1 运行前输出以及文本处理
		* gawk -f test.gawk data1 使用gawk脚本文件处理data1文件

## 系统设置
---
### 系统关机与系统信息

* 关机
	* shutdown -s [时间]，立即关机使用0或者now
	* init 0，这个是运行级别关机，需要确保系统没有在读写硬盘等操作，否则可能导致损坏
	* power off，这个命令很好用
	* halt，这个命令不太好用
* 重启 
	* reboot 主要用于单用户模式
	* shutdown -r [时间] 如now
* 查看电脑型号：sudo dmidecode | grep Product 查看服务器型号，如Dell PowerEdge R730


### 系统编码

* 通过修改/var/lib/locales/supported.d/local文件 为三行`en_US.UTF-8 UTF-8` `zh_CN.UTF-8 UTF-8` `zh_CN GB2312`
* Ubuntu默认的中文字符编码为zh_CN.UTF-8，这个可以在/etc/environment中看到：
* sudo gedit /etc/environment，可以看到如下内容：
`PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games"`  `LANG="zh_CN.UTF-8"` 
`LANGUAGE="zh_CN:zh:en_US:en"`
* 第二行即是默认的中文字符编码。注：可以通过这里修改默认的中文编码字符，比如修改为zh_CN.GBK
* 运行sudo dpkg-reconfigure locales 重启ubuntu。如果存在部分中文乱码，建议查看下一步中`安装字体的部分`。

### 使用ibus的中文输入法

* 可以先将系统编码设置为中文
* 如果不想安装中文语言包，则需要安装中文字体：`$ sudo apt-get install ttf-wqy-* ` `$ sudo apt-get install xfonts-wqy ` `$ sudo apt-get install fonts-wqy-*`
* 安装ibus框架：`sudo apt-get install ibus ibus-clutter ibus-gtk ibus-gtk3 ibus-qt4`
* 安装输入法：`sudo apt-get install ibus-pinyin ibus-table-wubi ibus-googlepinyin ibus-sunpinyin`
* 进行配置ibus `ibus-setup`，添加输入法。
* 执行im-switch -s ibus，选中ibus即可。如果出错进行如下操作：`cd /etc/X11/xinit/xinput.d/ $ sudo cp default ibus $ sudo vim ibus`，修改三行`XIM=ibus XIM_PROGRAM=/usr/bin/ibus-daemon XIM_ARGS="—xim"`，然后再运行im-switch -s ibus。
* 配置ibus的自启动：在~/.bashrc中添加如下内容。因为放在bashrc中，所以需要有terminal开着才行，也可以在~/.profile中也加上下面这段以防止没有terminal开着时ibus退出。修改文件一般最多重启一下电脑就好了。

```
export GTK_IM_MODULE=ibus  
export QT_IM_MODULE=ibus  
export XIM_PROGRAM="ibus-daemon"
export XMODIFIERS=@im=ibus 
ibus-daemon -drRx
```

### 开机启动
---
参考文章 [here](http://www.cnblogs.com/hongzg1982/articles/2101792.html)

* 系统开机时启动
	* 编辑 /etc/rd.local 脚本，添加运行命令
* 用户登录时启动
	* 终端中执行gnome-session-properties，进行添加
	* 编辑用户对应的 ~/.profile， 添加运行命令。该文件在用户登录后只运行一次，常用于初始化环境变量和程序设定等操作。
	* 用户对应的~/.bashrc 每个shell都会读一遍该文件。

## 磁盘内存
---
* df  查看磁盘使用情况，df -lh 格式化查看磁盘
* free 查看内存使用情况
* du  查看当前文件夹下的文件占用磁盘大小，-s /etc列出目录下所有目录一级，-S对子目录进行占用大小统计展示


### 系统环境变量
---
* 使用env查看当前环境变量，可进行grep。
* /etc/profile 文件的环境变量对所有用户生效，且只在开机时加载。修改之后，source /etc/profile 生效，但只有该terminal有效，重启后所有terminal有效。
* ~/.profile 文件是每个用户名用户的环境变量，在用户初次登陆时加载。修改之后，source ~/.profile 生效，但只有该terminal有效，logout该用户，重新登录后有效。
* 如在/etc/profile和~/.profile中都添加如下的内容，或者用户可以自修改个人的env值。

```
export JAVA_HOME=/opt/jdk1.8.0
export JRE_HOME=${JAVA_HOME}/jre  
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib  
export M2_HOME=/opt/maven3.3.3
export PATH=$PATH:$M2_HOME/bin:$JAVA_HOME/bin
export NODE_HOME=/opt/node7.7.2
export  PATH=$PATH:${NODE_HOME}/bin
```

## 文件文件夹
---
### 新建文件文件夹
---
* 只要目标文件不存在，很多情况下都会自动创建目标文件，如cp、mv、>。
	* touch a.txt  创建文件，再用vi编辑
	* vi a.txt  创建文件再保存等等
	* 
* 创建新文件夹：mkdir dir1，使用路径：mkdir -p /home/user1(-p指示路径)

### 显示文件文件夹
---
* 查看文件夹的文件列表
	* 列出当前路径下文档目录：l、ls、ls -a、ls -l等，ll查看文件及权限
	* sort 进行排序，-n按照数字排序，-r按照最大数字排序，如du -S /var/ |sort -rn
* 查看文件内容
	* cat 显示一个文档的内容，反过来的tac命令将文档内容倒着显示
	* more 分页显示，enter是下一行，space是下一页，不能向上回滚，如ls –l|more 管道再分页显示   more prog1.c分页显示文件内容
	* less 分页显示，与more类似，但支持上下查看，比more强大，支持向上回滚，“less is more”
	* head -20 a.log  显示前20行，默认显示10行
	* tail -10 a.log  显示最后10行；tail +2 a.log  显示从第2行到最后

### 查找文件与命令
---
* find /etc -name "*.cnf"  在指定目录下查找文件名
* which mysql  查找命令所在位置
* whereis mysql  查找软件使用的相关路径 
### 拷贝剪切压缩
---
* 拷贝
	* 拷贝本地文件：cp src.c  to.c，cp -r ./*  /tmp(当前目录下所有都拷贝到/tmp)
	* 从远程服务器复制文件到当前目录(.)：scp zhangsan@10.15.62.108:/home/zheda/1.sh ./ 
* 压缩
	* 压缩 tar -czvf 压缩后文件.tar.gz 要压缩的文件夹
	* 解压缩 tar -xzvf 压缩文件.tar.gz
* 剪切、重命名文件或文件夹使用移动即可，mv old new 

### 删除文件文件夹
---
* 删除文件：rm file1，删除当前目录下所有：rm *
* 删除空文件夹： rm -d dir1 或者 rmdir work
* 递归删除一个文件夹及内部文件： rm -rf  dir1，其中-r递归，-f强制删除，一定要谨慎使用

## 用户用户组
---
###  查看用户
*	who -T 查看的当前系统的登录用户，-T可查看是否开启消息功能和tty，Ubuntu可能关闭了消息功能
*	mesg 查看本人是否允许发送消息，mesg y 可开启
*	write root tty3 换行 hello  用who查看root在tty3的话可向其发送hello


### 添加用户用户组
* useradd 选项 用户名  添加用户
* groupadd 选项 用户组  添加用户组

### 删除用户用户组
* userdel 选项 用户名  删除用户
* groupdel 用户组   删除用户组

### 修改用户用户组
* 修改用户
	* usermod -g zju user1  为user1主组设置为zju，主组只能有一个，附加组可以多个
	* usermod -G grp2 user1  为user1添加附件组
	* usermod -U user1  解除user1的密码锁定，使用-L为锁定用户密码让密码无效
	* usermod -l user1 zju  将用户名zju修改为user1
	* usermod -d /etc  设置用户登录时的目录等等
* 修改用户组
	* groupmod 选项 用户组  修改用户组信息
	* newgrp root  将当前用户切换到其他组，类似login指令，当它是以相同的帐号，另一个群组名称，再次登入系统。

* 用户、组、密码等重要配置在/etc/下
	* 如less /etc/sysctl.info  系统信息，按q退出
	* etc的group是组信息
	* etc的passwd是用户密码文件
	* etc的adduser.conf 添加user的默认配置

## 权限
---
* 查看权限：ll或者l -l。
	* -rwxrwxr-x 1 zju zju  869 Nov  9 05:52 deploy.sh*
	* drwxrwxr-x 2 zju zju 4096 Nov  9 02:31 sh/
	* -表示是文件，d表示是文件夹，l表示链接文件等。第一个rwx表示属主读4、写2、执行1权限，第二个rwx表示所在组的权限，第三个表示其他用户权限，如果是-表示无权限。1表示文件inode数量。zju表示所有者。zju表示所属组。869表示869Bytes。年月日是最后修改时间。文件名或文件夹名。

* 权限修改
	* 修改所属权
		* chown zheda 1.c	修改文件所有者为zheda这个用户
		* chown zheda:zju 1.c	修改文件所有者为zheda这个用户和zju这个组
		* chgrp zju /archieve  修改文件目录所属组
		* chown –R zheda:zju mydir	设置目录及递归的内文件所有者为zheda，用户组为zju
	* 修改读写执行权
		* 修改读写执行权限使用chmod ijk 1.sh，其中ijk分别对应文件属主(user)、所在组(group)、其他用户组(other)分配的权限，可写w=4，可读r=2，可执行x=1，所以ijk都是0到7之间。chmod默认对所有用户(all,a)进行设置，要不就应该标明u,g,o。
		* sudo chmod u+rwx file  比如属主无权file，使用该语句设置属主有权执行file（如果当前用户不是属主，应考虑更改文件属主或者开放other权限等方式）
		* sudo chmod g+x file  比如文件所属的组无法执行file，使用该语句设置所属组有权执行file
		* sudo chmod o+x file  比如属主和所在组之外的人无法执行file，使用该语句设置其他人有权执行file
		* sudo chmod 777 ××  每个人都有读写以及执行的权限，××指文件名，也可以是文件夹名，不过要在chmod后加-ld
		* chmod 751 file 　　　 给file的属主分配读、写、执行(7)的权限，给file的所在组分配读、执行(5)的权限，给其他用户分配执行(1)的权限
		* chmod u=rwx,g=rx,o=x file 上例的另一种形式
		* chmod =r file 　　　　为所有用户分配读权限
		* chmod 444 file 　　　　 同上例
		* chmod a-wx,a+r file 　　 　 同上例
		* chmod -R u+r directory 　 递归地给directory目录下所有文件和子目录的属主分配读的权限




## 网络
---
### 网络IP设置
---
* 修改ip地址
	* 即时生效: ifconfig eth0 192.168.1.155 netmask 255.255.255.0
	* 重启生效: 修改/etc/network/interfaces，修改如下内容即可，然后重启服务service networking restart或者sudo /etc/init.d/networking restart

```
auto em1
iface em1 inet static
address 10.15.82.64
gateway 10.15.82.53
netmask 255.255.255.0
network 10.15.82.0
broadcast 10.15.82.255
dns-nameservers 10.10.0.21
```

* 修改dns
	* 修改 /etc/resolvconf/resolv.conf.d/base 修改后即时并永久生效，添加两行：nameserver 192.168.1.1 nameserver 220.170.64.68
* zju-vpn-ubuntu 使用 vpn-connect
	1. 下载一个全自动vpn安装包。http://zjufiles.lifetoy.org/linux/vpn/xl2tpd_1.2.5+zju-1_i386.deb (ftp和org等找)
	2. 安装之前先删除原来的xl2tpd包。命令：`sudo dpkg --purge xl2tpd`
	3. 安装。命令：sudo dpkg -i xl2tpd_1.2.5-zju1_i386.deb (根据提示或错误信息完成安装)
	4. 配置。命令：sudo vpn-connect -c (用户名后面一定要加@a)。
	5. 连接。命令：sudo vpn-connect
	6. 断开。命令：sudo vpn-connect -d

```
#!/bin/bash
echo ">>>> disconnecting vpn >>>>"
sudo vpn-connect -d
#vpn-connect -c
echo ">>>> using 21326@a >>>>"
sudo vpn-connect
```


### 端口控制
---
* 防火墙，使用ufw进行端口设置
	* sudo apt-get install ufw  安装ufw
	* sudo ufw enable| disable | default deny |status ，对ufw的运行控制
	* sudo ufw allow 8080 | delete allow 80 | allow from 192.168.1.1 | deny smtp ，控制ufw的规则

* 端口查看
	* ps –aux | grep 8080 |grep java | grep tomcat | grep tcm ，使用ps查看，结合grep进行筛选
	* netstat -ap | grep 8080  网络端口运行状态
	* lsof -i:8888  查看端口状态

* 关闭端口对应的进程
	* 使用kill -9 PID 关闭进程，PID为进程号，-9表示发送信号让其正常关闭。如果不知道PID，可用上面查看端口的命令查看对应的PID。
	* 下面的脚本就是删除8100-8102的所有进程：

```
ID=`lsof -i:8100 -i:8101 -i:8102 | awk '{print $2}'`
echo $ID
echo "To kill all the tomcat processes:"
echo "##############################"
for id in $ID
do
  kill -9 $id
  echo "killed $id"
done
echo "#############################"
```

### FTP
---
* FTP server建立可以使用FileZilla的server。
* 客户端使用软件：
	* winscp：windows访问linux的文件，支持FTP/SFTP等方式，常进行文件上传下载。
	* Private shell：windows访问linux的security shell，支持SFTP、FTP。
	* Xshell：windows访问linux的ssh，不支持FTP等。
	* Xmanager：包括了Xshell、XFTP等一系列解决方案。

### web
* lynx是古老而强大的web浏览工具，基于文本浏览网站，支持在命令行查看网页，支持图形库显示、文本数据显示和html源码显示
	* `lynx -dump http://www.baidu.com`  查看web文本数据，可以接管道存放到文本
* wget能将页面下载到本地
	* wget www.baidu.com 还可下载文件等
	* wget -nv --spider www.com  测试网站是否有效，-nv表示精简输出信息会将无效的转为404.html提示 
* crul可以向web发送数据和获取数据，还支持ftp等协议
	* `crul www.baidu.com -d uid=123 -d "msg=hi"` 使用crul向网站发送数据

### mail
* mailx
	* `echo "hello" | mailx -s "title1" root`  向root发送hello，root用户可以使用`mail` 查看用exit退出查看，可能需要安装mailutils，配置为local可进行用户间通信，还可以配置为internet等方式
* mail
	* `mail -s "hi" 123@qq.com ` 发送邮件，系统需要运行一个邮件服务器


### 远程访问
---

#### 使用SSH访问Linux
---
* SSH为Secure Shell，建立在应用层和传输层基础上的安全传输协议，传输数据经过加密和压缩。Window下的telnet就没有加密，所以已经不太常用。
	* 命令行访问可使用：ssh user1@10.15.62.11:port，端口不写默认为22，根据提示输入用户名密码
	* 可视化软件可使用软件Xmanager的xshell、putty等。

#### Linux访问Windows桌面系统
---
* sudo apt-get install remmina，运行remmina的图形界面访问windows即可。

#### Windows访问linux的图形界面
---
* 1.使用xrdp访问
	* 安装xRDP及vncserver：sudo apt-get install xrdp vnc4server tightvncserver 
	* 安装Xfce桌面环境sudo apt-get install xfce4 或者 安装xubuntu-desktop桌面 sudo apt-get install xubuntu-desktop
	- 设置xRDP对当前用户有效echo xfce4-session >~/.xsession
	- 设置配置文件sudo vim /etc/xrdp/startwm.sh 在./etc/X11/Xsession 前一行插入一行 `xfce4-session`
	* 重启 xrdp: sudo service xrdp restart，使用mstsc连接linux，选择sesman-Xvnc输入账户密码即可。

* 2.使用 vnc viewer 访问：
	* 参考文章：
		* [windows下通过VNC图形化访问Ubuntu桌面环境](http://www.cnblogs.com/lanxuezaipiao/p/3724958.html)
		* [Linux远程控制之VNC](http://www.jeepshoe.org/2015/1289874.html)
	* Linux中安装server:sudo apt-get install vnc4server
	* 先运行vnc4server以设置密码并让vnc生成配置文件夹~/.vnc，修改~/.vnc/xstartup配置文件。原始内容如下。

```
#!/bin/sh

# Uncomment the following two lines for normal desktop:
# unset SESSION_MANAGER
# exec /etc/X11/xinit/xinitrc

[ -x /etc/vnc/xstartup ] && exec /etc/vnc/xstartup
[ -r $HOME/.Xresources ] && xrdb $HOME/.Xresources
xsetroot -solid grey
vncconfig -iconic &
x-terminal-emulator -geometry 80x24+10+10 -ls -title "$VNCDESKTOP Desktop" &
x-window-manager &
```

其中，需要取消注释unset SESSION_MANAGER和exec /etc/X11/xinit/xinitrc来激活图像界面，后面的代码基本是测试文件是否存在、设置是否存在、启动桌面程序等，都可以注释掉，使用系统默认的桌面环境或者接着配置自己要用的图形界面。
* 如果是gnome桌面，启用gnome-session &
* 如果是KDE桌面，启用startkde &
* 如果是Unity桌面，首先去 /usr/share/gnome-session/sessions/下看桌面session的名称，如果是ubuntu-2d，则将最后一行改为：
export STARTUP="/usr/bin/gnome-session --session=ubuntu-2d"$STARTUP	（仅供参考）

* 下面是示例xstartup配置。如果访问后的界面不完整，应确认是否已经安装gnome-panel、gnome-settings-daemon等软件，没有的话使用 ` sudo apt-get install gnome-panel gnome-settings-daemon metacity nautilus gnome-terminal vnc4server`安装相关软件。
* 还有问题的话应该通过查看vnc4server对应的log文件排查问题，比如用户权限问题。
	* 比如提示没有权限在~/.config下创建文件等提示，可将.config文件夹属主修改为当前用户和用户组（chown -R zju:zju .config/），注意应确保不影响其他用户和用户组。
	* 比如提示gnome-session的shell失败，可以分配~/.dbus的权限或者直接改chown。
	* 比如界面很丑，蓝色背景和黑色导航栏，提示`failed to load theme adwaita`，可以`sudo apt-get install gnome-themes-standard`，重启
* 修改分辨率：
	* 一种是每次启动vnc4server命令行添加参数：vncserver -geometry 800x600
	* 修改配置文件：查看whereis vncserver的相关文件，查看相关路径。我是修改/usr/bin/vnc4server中的-geometry参数为1920乘以1080

```
#!/bin/sh

export XKL_XMODMAP_DISABLE=1
unset SESSION_MANAGER
#exec /etc/X11/xinit/xinitrc
#unset DBUS_SESSION_BUS_ADDRESS
#exec /etc/X11/Xsession

[ -x /etc/vnc/xstartup ] && exec /etc/vnc/xstartup
[ -r $HOME/.Xresources ] && xrdb $HOME/.Xresources
xsetroot -solid grey
vncconfig -iconic &

#xterm -geometry 80x24+10+10 -ls -title "$VNCDESKTOP Desktop" &
#gnome-session &
gnome-panel &
gnome-settings-daemon &
metacity &
nautilus &
gnome-terminal &
```

* vnc4server使用方法
	* 运行服务：vnc4server	（默认从1号服务开始，可用ps –aux查看到，用于客户端连接）
	* 运行服务时修改分辨率：vnc4server 1900x1080	(分辨率之间用小写x)
	* 查看已经运行的vnc服务：ps –aux | grep vnc
	* 关闭在运行的服务：vnc4server –kill :1	(注意kill后面的空格和冒号，1指vnc4server服务序号)
	* 强制关闭运行的服务：sudo kill pid	(pid是进程号，可以从ps -aux命令中查看第二列)
	* 修改vnc4server服务密码：vncpasswd 
	* 可以删除~/.vnc/passwd或者备份显示配置后删除.vnc目录，再启动的时候就会重新设置服务密码。服务密码为8位。
	* 关闭vncserver的端口后，可以 `rm /tmp/\.X*-lock` 避免之后重新打开的端口累加

* VNC Viewer客户端
	* Window下安装VNC Viewer软件，访问地址就是ip:Sid，其中Sid就是vncserver中启动的服务序号，根据提示输入密码即可。

* 3.Xmanager(未成功)
	* 参考文章：
		* [在Windows下用XManager远程控制ubuntu](http://blog.sina.com.cn/s/blog_a83c1a6301014mtv.html)
	* 设置防火墙 sudo ufw allow 177	
	* 安装桌面程序sudo apt-get install xrdp vnc4server tightvncserver gdm
	* 编辑/etc/gdm/custom.conf
	* 重启服务并连接sudo service gdm restart，xbrowser中添加XDMCP，设置secure XDMCP、host等即可。
	
```
[security]
AllowRemoteRoot=true		#允许root用户远程登录
[xdmcp]
Enable=true		#开启远程桌面
```




## 软件
---
### 软件管理
---
* 软件源
	* sudo vi /etc/apt/sources.list  #设置软件源，比如[在list中添加zju的镜像源](http://mirrors.zju.edu.cn/)
* 安装软件
	* 解压缩免安装版软件：tar -xzvf 压缩文件.tar.gz
	* apt-get管理：sudo apt-get install/remove 软件名，安装后支持查看软件安装使用的各个目录：whereis nginx
	* 编译安装源程序：解压缩源代码，运行文件夹中的./configure（有的压缩包已经编译过，这一步可以省去），运行make进行编译，运行make install完成安装。
	
* 软件删除
	* sudo apt-get remove --purge 软件名
	* sudo apt-get purge nginx	#purge参数为彻底删除文件
	* sudo apt-get autoremove  删除系统不再使用的孤立软件
	* sudo apt-get autoclean  清理旧版本的软件缓存
	
* 清理内核及软件
	* 包管理的临时文件目录:/var/cache/apt/archives，没有下载完的在/var/cache/apt/archives/partial
	* 软件删除
		* sudo apt-get remove --purge 软件名
		* sudo apt-get autoremove  删除系统不再使用的孤立软件
		* sudo apt-get autoclean  清理旧版本的软件缓存
		* dpkg -l |grep ^rc|awk '{print $2}' |sudo xargs dpkg -P   清除残余的配置文件保证干净。
	* 清理多余内核：
`dpkg -l 'linux-*' | sed '/^ii/!d;/'"$(uname -r | sed "s/\(.*\)-\([^0-9]\+\)/\1/")"'/d;s/^[^ ]* [^ ]* \([^ ]*\).*/\1/;/[0-9]/!d' | xargs sudo apt-get -y purge`

###  推荐软件
---
* 个性化小工具
	* 7zip 压缩软件、fcitx 输入法、speechcrunch 计算器、Docky 桌面任务栏管理、shutter 截图工具、thunderbird-邮箱管理、firefox 浏览器、transmission BT BT下载、vlc 多媒体播放、rhythmbox 音乐播放、adobe flash、wine 执行exe文件、qq、gmail、cairo-dock dock桌面管理、software&updates 软件管理、nautilus 文件管理、nautilus-open-terminal 右键打开命令行(安装后运行nautilus -q，然后运行nautilus)
* 编程工具
	* python: pycharm, wingIDE




