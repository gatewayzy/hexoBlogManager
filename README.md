# hexoBlogManager
***
## 说明
---
* Hexo博客的管理代码。
* 项目中含有大量的个人配置，一般使用是没什么问题的，但是如果您想发布自己的hexo blog，建议看一看本站搭建方法[搭建个人博客站点(1)-托管到github pages](http://bebetter.site/2016/08/08/blog/%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E7%AB%99%E7%82%B91-%E6%89%98%E7%AE%A1%E5%88%B0github%20pages/)。

## Links
---
-  [home](http://bebetter.site/)
-  [open my github](https://github.com/gatewayzy)

## 使用步骤
---
### Windows

- 安装git for windows。
- 安装windows版Node.js，使用CMD测试node命令和npm命令。
- 在Git bash中运行 `npm install -g hexo` 以安装hexo，-g表示global全局安装。运行hexo命令查看安装。
- 在一个文件夹打开git bash，运行 `hexo init` 来下载hexo示例的git项目。
- ssh下载github中的hexoBlogManager
- 将hexoBlogManager项目(包括.git文件)复制到demo项目中覆盖原有文件(推荐先删除demo中要覆盖的文件内容，然后直接拷贝进去)，此时已经可以本地运行hexo。
- 还可以删除hexoBlogManager，将demo项目重命名为hexoBlogManager，并与github建立ssh连接。如果要部署的话，应该接着运行命令`npm install hexo-deployer-git --save`。

### Ubuntu

- 不推荐使用apt-get install方式安装nodejs，因为之后再安装hexo的时候使用命令为node，但是apt-get安装的nodejs命令为nodejs。
- 使用https://nodejs.org/download/进行下载源码安装nodejs。
- 项目中只有网站文章部分，必须通过hexo init下载hexo模块。

### MacOS
- 下载nodejs并安装，试验npm命令
- 使用`npm install -g hexo` 全局安装hexo，试验hexo命令
- 新建文件夹并在其内部运行`hexo init` 来下载hexo示例项目（称为init项目）
- 下载本项目，将init项目中**未在本项目中出现的文件、文件夹**拷贝进来
- 在本项目路径下，依次运行`hexo clean`、 `hexo g`、 `hexo s`，然后访问 `127.0.0.1::4000`即可
- 如果需要发布到github，需要搭建github page，并设置`_config.yml`的`deploy`配置，然后运行命令`npm install hexo-deployer-git --save`安装发布工具，然后运行 `hexo d` 进行发布。



