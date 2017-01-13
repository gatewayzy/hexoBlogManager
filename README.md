# hexo博客的管理代码
***
## 说明
---
hexo blog的原始代码。项目中含有大量的个人配置。

## links
---
-  [home](http://bebetter.site/)
-  [open my github](https://github.com/gatewayzy)

## 使用步骤
---
**1. windows**
- 安装git for windows。
- 安装windows版Node.js，使用CMD测试node命令和npm命令。
- 在Git bash中运行 `npm install -g hexo` 以安装hexo，-g表示global全局安装。运行hexo命令查看安装。
- 在一个文件夹打开git bash，运行 `hexo init` 来下载hexo示例的git项目。
- ssh下载github中的hexoBlogManager
- 将hexoBlogManager项目(包括.git文件)复制到demo项目中覆盖原有文件(推荐先删除demo中要覆盖的文件内容，然后直接拷贝进去)，此时已经可以本地运行hexo。
- 还可以删除hexoBlogManager，将demo项目重命名为hexoBlogManager，并与github建立ssh连接。如果要部署的话，应该接着运行命令`npm install hexo-deployer-git --save`。

**2. ubuntu**
- 不推荐使用apt-get install方式安装nodejs，因为之后再安装hexo的时候使用命令为node，但是apt-get安装的nodejs命令为nodejs。
- 使用https://nodejs.org/download/进行下载源码安装nodejs。
- 项目中只有网站文章部分，必须通过hexo init下载hexo模块。


