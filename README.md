# hexo博客的管理代码
***
## 说明
---
hexo blog的原始代码。项目中含有大量的个人配置。

## 使用步骤
---
- 安装git for windows。
- 安装windows版Node.js，使用CMD测试node命令和npm命令。
- 在Git bash中运行 `npm install -g hexo` 以安装hexo，-g表示global全局安装。运行hexo命令查看安装。
- 在一个文件夹打开git bash，运行 `hexo init` 来下载hexo示例的git项目。
- ssh下载github中的hexoBlogManager
- 将hexoBlogManager项目(包括.git文件)复制到demo项目中覆盖原有文件(可以删除demo中的source文件夹)，此时已经可以本地运行hexo。
- 还可以删除hexoBlogManager，将demo项目重命名为hexoBlogManager，并与与github建立ssh连接。如果要部署的话，应该接着运行命令`npm install hexo-deployer-git –save`。


