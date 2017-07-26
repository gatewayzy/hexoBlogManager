---
title: Python Django
comments: true
date: 2017-01-10 13:58:16
updated: 2017-01-10 13:58:56
categories: Language
tags:
- Django
---

**说明：**Django配置使用。
<!-- more -->


---
### 简介
* 参考文章：[Django 菜鸟教程](http://www.runoob.com/django/django-install.html)

### 安装

* `pip install django`
* 查看版本`import django; print(django.get_version())`
* 安装好django后可以使用django-admin.py进行项目管理

### 创建项目

* `django-admin startproject easyweb`  创建一个django项目工程
* django项目文件说明：
    * manage.py 命令行工具，用于管理项目
    * easyweb文件夹存放项目的容器，内含init表示是个python包，settings是配置文件（html模板路径、数据库连接、访问限制、编码等），urls存放url（路由和后台的映射关系），wsgi存放web入口。

### 启动项目

* pychamrm中创建django server，配置对应的django的root路径和settings路径等，启动并访问对应网址即可。
* `python ./manage.py runserver 127.0.0.1:8080` 是通过python的命令行模式启动项目。因此除了命令行运行，也可以使用pycharm添加python脚本和参数runserver...

### django使用

#### 基本页面与路由
* 使用return HttpResponse返回html文本，不适合返回完整html页面。
	* 必须使用request，不能省，不能简写

```
    from django.http import HttpResponse
    def home(request): return HttpResponse('<p>你好</p>') 
```
    

* 使用render方法返回html模板，将html文本和数据分开返回，类似于mvc结构。
 
 ```
    from django.shortcuts import render
    def toHome(request): context = {};context['myText'] = 'HI'; return render(request, 'home.html', context)
    容器内编写/templates/home.html，使用{{myText}}获取数据，使用{% if for include block 等语法，语法略 %}，使用{#此为html中的注释#}等等。
 ```
 
    * 配置MVC：
        * settings中配置TEMPLATES的`'DIRS': [BASE_DIR + '/templates']`以指定模板路径。还可以看到settings中有DATABASE默认使用了sqlite3等配置。
        * urls中配置路由：`from .views.index import home`以引入编写的后台py，然后urlpatterns中添加`url(r'^index/home$', home.toHome)`。其中url的第一项是url的正则表达式，注意正则表达式特性，如结束符$是否必须写以防home覆盖home2这种问题；第二项是路由对应的后台方法名。
