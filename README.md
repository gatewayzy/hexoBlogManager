# hexoBlogManager
***
## 说明
---
* Hexo博客的管理代码，网址为[github/gatewayzy/hexoBlogManager](https://github.com/gatewayzy/hexoBlogManager)。
* 项目中含有大量的个人配置，一般使用是没什么问题的，但是如果您想发布自己的hexo blog，建议看一看本站搭建方法[搭建个人博客站点(1)-托管到github pages](http://bebetter.site/2016/08/08/Blog/%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E7%AB%99%E7%82%B91-%E6%89%98%E7%AE%A1%E5%88%B0github%20pages/)。

## Links
---
-  [home](http://bebetter.site/)
-  [open my github](https://github.com/gatewayzy)

## 安装步骤
---
* 安装git
* 安装node [nodejs下载地址](https://nodejs.org/en/download/)
	* Windows nodejs：安装windows版Nodejs.msi，使用CMD测试node命令和npm命令。
	* Ubuntu nodejs：
		* 不推荐使用apt-get install方式安装nodejs，因为之后再安装hexo的时候使用命令为node，但是apt-get安装的nodejs命令为nodejs。
		* 使用官网下载安装nodejs：下载tar.gz，解压，添加到path文件，如在/etc/profile中添加`export NODE_HOME=/opt/node7.7.2` `export  PATH=$PATH:${NODE_HOME}/bin`，运行`source /etc/profile`，试验`node``npm`命令。
	* MacOS nodejs：下载nodejs.pkg并安装，试验npm命令。
* 使用node安装hexo：运行 `npm install -g hexo` 以安装hexo，-g表示global全局安装。运行`hexo`命令查看安装结果。
* 新建文件夹并在其内部运行`hexo init` 来下载hexo示例项目（称为init项目）
* 用git下载本项目，将init项目中**未在本项目中出现的文件、文件夹**拷贝进来
- 在本项目路径下，依次运行`hexo clean`、 `hexo g`、 `hexo s`，然后访问 `127.0.0.1::4000`即可
- 如果需要发布到github，需要搭建github page，并设置`_config.yml`的`deploy`配置，然后运行命令`npm install hexo-deployer-git --save`安装发布工具，然后运行 `hexo d` 进行发布。
- 注意本项目使用了hexo的pdf功能，需要使用node运行`npm install --save hexo-pdf`才能正常发布，可参考下面的hexo支持pdf拓展说明。

## Hexo功能拓展
### 支持pdf：
* 参考：[Github: superalsrk/hexo-pdf](https://github.com/superalsrk/hexo-pdf/)
	* 在hexo项目中运行`npm install --save hexo-pdf`安装插件
	* 如果是外部网站pdf文件，直接使用网址引入：`{% pdf http://www.mywebsite/readme.pdf %}`
	* 本站的话：在/source/statics中添加pdfs文件夹，存放pdf文件，hexo g生成的时候会自动生成到/public/statics/pdfs目录下，md文件中引入：`{% pdf /statics/pdfs/1.pdf %}`。
	* *为了便于更新和管理pdf，本站将word等源文件放在posts里，然后生成pdf并手动拷贝到/source/statics中。但是github的速度慢，将pdf放到CDN服务器上。*

## 启动、发布项目
* 可以参考sh文件夹下的脚本。
* 启动项目：
	* 在项目目录下，运行命令`hexo clean`清除之前生成的文件(可跳过)
	* 运行`hexo s`发布项目，访问默认的4000端口即可。 md源文件在source/_posts目录下。
	* 如果想连同草稿一起发布，运行`hexo s --draft`。 md源文件包括source/_drafts和_posts。
	* 网站的“关于”对应到source/about/index.md。


