---
title: Windows
comments: true
date: 2017-01-12 13:58:23
updated: 2017-01-12 13:58:56
categories: Softwares
tags:
- Windows
- OS
---

**说明：**Windows配置，Windows的使用。
<!-- more -->

## 系统
---
### 环境变量
---
* set zy=zyy;echo %zy% 命令行设置环境变量和显示，只对当前terminal有效


### 快捷键
---

* Windows key键（简称win） + R  运行命令或打开文件
* 运行msconfig管理其他系统配置
* 运行dxdiag查看电脑配置
* 运行regedit查看注册表
* 运行mstsc使用远程桌面连接
* Win + E  打开资源管理器
* Win + L  锁定电脑
* Win + D  显示桌面
* Alt + Tab  切换窗口，不停留式切换
* Win + Tab  切换窗口，停留式切换 
* ctrl win 左右 #切换桌面
* Ctrl + W  关闭窗口，包括资源管理器等窗口均有效
* Ctrl shift esc  调出任务管理器

### 个性化设置
---
*  Win7中在淡绿色背景：个性化-窗口颜色-高级外观设置-窗口-其他-自定义-窗口RGB值为（204，232，207）或色调:84饱和度:91亮度:205
*  Win10中修改注册表：运行regedit，修改 `[HKEY_CURRENT_USER\Control Panel\Colors] windows`，修改值为202 234 206，修改 `[HKEY_LOCAL_MACHINE \SOFTWARE \Microsoft\Windows \CurrentVersion\Themes\DefaultColors\Standard\Window]`改为十六进制的caeace。点击确定退出注册表。重启电脑即可。

## 网络
***
### VPN
---

* 实验室VPN配置
	* VPN属性：域名lns.zju.edu.cn，连接方式：L2TP，安全协议：可选加密、允许使用这些协议
	* IPV4：子网掩码255.255.255.0，网关10.15.62.1，DNS：10.10.0.21 或者 10.10.2.53

* 错误VPN 789：L2TP连接尝试失败
	* 原因应该是电脑未开启ProhibitIpSec注册表值，尤其是ghost安装的系统。应该将ProhibitIpSec 注册表值设为1。添加 ProhibitIpSec 注册表值步骤如下。
	* S1.找到下面的注册表子项，然后单击它：
`HKEY_LOCAL_MACHINE\system\CurrentControlSet\Services\Rasman\Parameters`
	* S2.在“编辑”菜单上，单击“添加值”，“值名称”框中，键入 ProhibitIpSec。在“数据类型”列表中，单击“REG_DWORD”，然后单击“确定”。（使用32位值）在“数据”框中，键入 1，然后单击“确定”。
	* S3.退出注册表编辑器，然后重新启动计算机。

### 端口
---
#### 端口占用

* netstat -ano 查看端口占用
* netstat -aon|findstr "9050" 查找并过滤端口占用，可查看对应的pid
* tasklist|findstr "2016" 查找pid进程
* taskkill /f /t /im nodejs.exe  关闭进程 

## 常用软件
---
###  cmd 命令提示符
***
* cd跳转至文件夹或目录，cd ..回到上一级，换D盘运行D:。
* chdir  /d  F:\files  可以更改分区的目录跳转
* tab自动补齐，重复可多按tab。
* dir显示目录下所有文件。
* del filename删除文件。
* cmd运行java程序：cd到a.java目录，用javac a.java编译生成a.class，运行java a。
* shutdown –f –s –t 60表示60秒后强制（f）关机（shutdown），-r为重启reboot
* shutdown –a表示取消关机计划
* rasdial  VPN名称  用户名  密码 ：连接vpn，可以设置自动连接任务。`rasdial VPNckce6 ckce6 cad22`
* rasdial VPN名称 /disconnect 断开该VPN连接，可能需要管理员身份运行
* 文件夹下按shift并右键，可以显示更多选项，包括在此目录打开cmd。
* cmd后台运行start /b ping 127.0.0.1，关闭当前cmd会结束进程。

### 程序自启动
设置程序自启动有多种方法。
* 计划任务程序
	* 打开任务计划程序，选择创建基本任务，设置名称，触发条件和运行程序等
	* 设置任务的属性，推荐不管用户是否登录都要运行，并且输入用户名和密码，防止程序无权自动运行
* 将程序添加到启动菜单中
	* 将程序或者快捷方式复制到`C:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp`目录中
 

### mstsc 远程桌面
---
* 可能涉及到的配置
	* S1开启“允许被远程桌面连接”：在系统高级设置的远程桌面连接中开启。然后更改防火墙设置：一般只要设置允许应用通过防火墙，允许远程桌面通过。进一步可以设置高级防火墙运行远程桌面访问公用网和工作网。
 
	* S2网络发现：并不一定要启用网络发现功能。但是如果想在网络邻居中查看同一网段中的电脑，需要启用。
 
	* S3本地安全策略：只要保持一致就行(如大家都设置为未定义)，一般不需要调整。
 
	* S4本地组策略：只要保持一致就行(如均未配置)，一般不需要调整。如要调整需要进行编辑，选择“已启用” 然后在“显示”里面输入 TERMSRV/*（允许保存所有），然后刷新组策略，使设置生效：开始、运行、输入 `gpupdate  /force` 强制刷新。

* 相同网段与不同网段问题
	* 只要IP无误，能够ping通，不存在相同网段和不同网段的问题。可以使用tracert IP查看路由情况。
* 域名\用户名问题
	* 用网络邻居连接时，域名是默认值。IP连接时最好使用完整的DOMAIN\user。完整的DOMAIN\user信息查看方式可以通过“高级系统设置” >> “添加远程连接用户” >> “用户名检查”的方法确定。
* 用户名与密码问题
	* 用户名和密码是对应的。但是如果用户是网络账户，一定要注意要使用的密码不是pin码，而是网络账户对应的密码（如win10的微软账户）。

### 其他软件
---
* Xmanager：包含xshell、xftp、xbrowser等，支持一站式解决window访问linux的问题。
* MarkdownPad：实时编写markdown，支持extra扩展版、css样式修改等。

### 问题与解决
---
#### 资源管理器消失
----
* 问题描述：资源管理器消失不见
* 解决方法：使用ctrl shift esc 调出任务管理器，在文件->运行新任务->输入 explorer确定即可。








