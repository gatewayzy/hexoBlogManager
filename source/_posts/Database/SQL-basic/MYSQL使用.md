---
title: MYSQL使用
comments: true
date: 2017-09-01 13:58:10
updated: 2017-09-01 13:58:10
categories: Database
tags:
- SQL
---

**说明：**MYSQL使用。
<!-- more -->

## MYSQL的备份与恢复
* 备份：
	* 备份单个数据库：`mysqldump -uroot -ppasswd person > D:\backup.sql`
	* 备份多个数据库，使用--databases：`mysqldump -uusername -ppasswd --databases db1 db2 > D:\backup.sql`
	* 备份所有数据库，视同-all-databases：`mysqldump -uusername -ppasswd --all-databases > D:\backup.sql`
	* 直接备份mysql的data文件，可以直接用到新的mysql上，但是二者版本最好一致。
	* 使用mysqlhotcopy工具快速备份，支持热备份，比mysqldump快，用于linux环境。基本语法是：mysqlhotcopy [option] dbname1 dbname2 backupDir/，详细使用--help。
* 备份脚本示例如下，基本流程就是指定备份文件路径，用mysql/bin/mysqldump程序进行所有db的dump。

```
rem 数据库备份
set target_db=db-82.50
set file_dir=G:/MySQL_backup/%target_db%
echo 备份文件所在的目录：%file_dir%
set file_name=%target_db%_%date:~0,4%-%date:~5,2%-%date:~8,2%
echo 备份文件名：%file_name%
C:
cd C:/Program Files/MySQL/MySQL Server 5.5/bin/
echo 备份到文件：%file_dir%/%file_name%.sql
mysqldump.exe -uroot -p123 --all-databases>%file_dir%/%file_name%.sql
rem D:/develop/MySQL/MySQL Server 5.5/bin/mysqldump.exe为mysqldump程序所在地址，root为用户名，mysql为密码，tcm为待备份的数据库名
```
