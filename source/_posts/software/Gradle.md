---
title: Gradle
comments: true
date: 2017-01-12 13:58:07
updated: 2017-01-12 13:58:56
categories: Softwares
tags:
- Gradle
---

**说明：**Gradle配置使用。
<!-- more -->


---
参考文章：

## Gradle简介
* Gradle是Apache基于Apache Ant和Apache Maven的一种项目构建方式。它使用一种基于Groovy的特定领域语言(DSL)来声明项目设置，抛弃了基于XML的各种繁琐配置。
* 面向Java应用为主。当前其支持的语言限于Java、Groovy和Scala，计划未来将支持更多的语言。
* 官网：http://gradle.org/
* GitHub地址：https://github.com/sitemesh/sitemesh3

## Gradle安装
---
下载gradle，添加GRADLE_HOME为gradle文件路径，在path中添加%GRADLE_HOME%\bin。CMD运行gradle –v可查看安装信息。

## 项目编译运行
---
### Cmd运行gradle：
---
在项目目录下运行gradle build进行编译，运行gradle jettyRun用jetty发布。

### Eclipse运行gradle：
---
安装gradle IDE插件，项目右键运行gradle build编译，右键运行run as正常发布。



