---
title: MySQL
comments: true
date: 2017-01-12 13:58:13
updated: 2017-01-12 13:58:56
categories: Softwares
tags:
- MySQL
---

**说明：**MySQL配置使用。
<!-- more -->


---
参考文章：

## 常用函数
---
### Date_formate日期格式转换
---
将datetime转换为字符串： `Select date_format(feedback_time,'%Y-%c-%d %h:%i:%s') AS feedbackTime`

### Concat拼接函数
---
* 拼接条件 `Select * from table1 where title like concat('%', #{title} ,'%')` 
* 拼接字符串 `SELECT CONCAT('HELLO', ' WORLD') AS exp`
* 将符合条件的同一列中的不同行数据拼接, 以逗号分隔 `SELECT GROUP_CONCAT(name) AS names FROM xxx GROUP BY yy `

### 中文排序问题
---
* 如果存储使用gbk字符集，直接order by就不会出现中文拼音排序的问题。如果mysql使用UTF-8存储中文，则order by中文拼音排序有问题，需要变换编码。
`select * from user order by convert(username USING gbk) asc; `

### 服务器状态信息
---
* 常用show status查看运行统计状态。
* show status like '%queries%'

### Explain语句解析
---
* 使用explain查看sql 语句的解析。
* explain select * from… 

### 选取存在重复项的数据
---

```
SELECT * from qa_dis_dic_code a where not EXISTS (SELECT 1 from qa_dis_dic_code b where a.category_2= b.category_2 and b.id<a.id)
```

## 命令行操作
---
* mysql登陆root：mysql -u root -p
	* 在Linux下mysql使用$HOME/.my.cnf读取特定的启动命令和设置，可以在里面设置连接mysql的默认密码，然后将配置文件设置为本人权限 chmod 700 .my.cnf 就可以用默认密码登录，方便shell编程等
* 显示数据库：mysql> show databases;
* 创建用户及授权：
mysql> create database zyy; mysql> create user zyy; mysql> use mysql; mysql> update user set Password values PASSWORD("112233445566") where user='zyy'; mysql> flush privileges;
* 使用更改
mysql> grant all on zyy.* to 'zyy'@'localhost' ;  mysql> flush privileges; mysql> show grants for snort@localhost; localhost换为%指可以远程登陆。
* 修改用户密码：
root登陆，mysql>update mysql.user set password=password('新密码') where User="test" and Host="localhost";mysql>flush privileges;
* 删除用户密码
mysql>Delete FROM user Where User='test' and Host='localhost';
mysql>flush privileges;
mysql>drop database testDB; //删除用户的数据库
* 删除账户及权限：
drop user 用户名@'%';
drop user 用户名@ localhost; 
* 查看表结构信息方法：
1.desc 表名;
2.show columns from 表名;
3.describe 表名;
* 修改类型：ALTER TABLE S MODIFY sno SMALLINT NOT NULL;
* 支持汉字：创表前：set names utf8;或者创建数据库时：create database yan2 default character set utf8;就可以在varchar字段里写汉字了。

### shell编程

假设设置了默认连接的密码，进行下面的shell编程
* 使用单行编程

```
#!/bin/bash
mysql1=$(which mysql)
# 查找mysql命令的位置
$mysql1 defaultdb -u root -X -e 'select * from students'
# 使用默认的密码连接数据库并运行一行命令，运行该脚本即可查看结果，与正常在mysql中运行效果一样
# -X使用XML输出结果
```

* 使用多行编程

```
#!/bin/bash
mysql1=$(which mysql)
# 查找mysql命令的位置
$mysql1 defaultdb -u root <<EOF
show tables;
select * from students;
insert ...
EOF
echo "运行该文件即对mysql进行shell多行，注意多行需要添加EOF开始，结束时的EOF必须为开头"
```

