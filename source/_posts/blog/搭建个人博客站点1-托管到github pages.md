---
title: 搭建个人博客站点(1)-托管到github pages
comments: true
date: 2016-08-08 20:21:04
updated: 2016-08-08 20:21:42
categories: Blog
tags: 
- blog
- github pages
- github
- hexo
- 搭建网站
---
**说明：**介绍建立个人blog站点的主要流程，使用github pages以及hexo进行实现。
<!-- more -->

## 为什么建站 
***
- **写博客的好处**
写博客可以总结知识，加深理解，相互交流...

- **写博客方法**
 使用CSDN、OSChina等网站写博客.    
 搭建个人博客站点...    

- **个人原因** 
 记笔记不喜欢用云笔记.  
各方前辈们的影响.  
偏好搭建个人网站...  

## 搭建博客站点主要流程
***

搭建博客要素：域名、服务器、域名解析、blog工程、工程发布到服务器。如果不需要设置个人域名，可以忽略域名、域名解析的工作。

### 域名
---  

- 访问网站的时候，好的域名容易记住。
- 可以在阿里云、godaddy等网站租用域名，域名花费不多。

### 服务器
---

- 服务器用来发布blog站点，服务器可以使用ECS、VPS等服务。  
- 云服务器ECS(Elastic Compute Service)基于Border Gateway Protocol (BGP)边界网关协议，提供可弹性伸缩的计算服务器。常用的有阿里云、亚马逊AWS等。  
- 虚拟专用服务器VPS(Virtual Private Server)将服务器分成多个虚拟专享服务器，基于容器技术和虚拟化技术。常用的有搬瓦工、linode、vultr等。  
- ECS和VPS一般能够提供独立的IPV4或者域名，价格也高低不一。

### 域名解析
---

- 域名要解析到服务器，可以是IP解析，也可以是CNAME解析等，域名解析服务需要服务商。
- 常用的域名解析有阿里云、DNSPod、godaddy等。普通服务基本免费。

### 创建blog站点工程
---

- 创建一个web工程用于发布blog，可以使用WordPress、hexo等工具架构。
- 个人blog站点应尽量少使用图片等网络开销较大的资源，尽量使用文字描述。
- 真正需要发布大量图片、flash等资源的话，可以租用CDN服务（Content Delivery Network，内容分发网络）。

### 发布blog
---
- 将站点代码发送到服务器并发布，即可访问相应的网站查看blog。

## 开始建站
***
*为方便描述，本文使用了一些个人的用户名、域名等变量，请注意替换。*
### 域名与域名解析
---
- 我从阿里云租用了bebetter.site，注册了DNSPod进行域名解析，服务器站点采用github提供的个人站点。
- 由于github通过github pages服务向每个用户免费提供了一个个人站点，并且有二级域名，所以没有单独租服务器。
- 下面开始写博客工程。

## github + github pages 写博客
---
### 创建github博客站点
---

参考文章：[创建GitHub技术博客全攻略](http://blog.csdn.net/renfufei/article/details/37725057/)
	
Github允许每个账号拥有一个基于github pages的user page站点，可以作为静态博客站点。个人站点的配置方法如下：

1. 新建repo，取名必须为账户名，如我的是gatewayzy，github会自动识别
2. 在该repo中设置启用github page（Settings-GitHub Pages#page generator），并发布，username.github.io就是个人的站点，可以打开并查看
3. 这个repo就是一个静态站点，可以增改删里面的文件，做出想要的网站效果。接下来是设置个人域名。
4. 将此站点与租用的域名映射：在repo根目录中添加文件“CNAME”，输入域名，如bebetter.site，不要写www等前缀。设置CNAME的作用体现在：当访问username.github.io的时候，将会跳转到CNAME域名。
5. 使用DNSPod进行解析，为bebetter.site添加解析如下。

|主机记录	|记录类型	|记录值	|
|:---:	|:---:	|:---:	|
|@	|CNAME	|gatewayzy.github.io	|
|www	|CNAME	|gatewayzy.github.io	|


*主机记录@表示xxx.bebetter.site都解析为这一行的记录值，CNAME表示记录值是域名而非IP等。
 主机记录www表示www.bebetter.site解析为这一行的记录值。其他的主机类型和记录类型等就不详述了。
 设置域名解析的作用体现在：当访问添加的域名的时候，将会访问对应的记录值的域名。域名解析发生循环是可控的，不用担心。*

通过CNAME和域名解析设置，可以在访问gatewayzy.github.io和bebetter.site时都会访问gatewayzy.github.io网站，但是域名显示是bebetter.site。
由于我都是使用github的私人主页作为博客站点，只存在博客站点的开发上的区别，所以域名解析都是一样的。
如果是使用VPS或者ECS的话，应该会有独立的IP或者一个域名，域名解析的步骤相同，具体参数设置可以搜一下。

### 更新博客
---
后期更新本质就是就是更新一个github项目。repo里面的index.html就是博客首页。  
可以使用在线编辑，进行增改删文件。不便之处在于：网速问题、发布预览麻烦等。  
可以将项目下载到本地进行建站工作，使用tomcat、Apache等进行预览，完成后使用git上传。可能问题是相对路径。  

## github + hexo 写博客
---
上面更新博客的方法原始又太麻烦，可以一些博客框架工具，如jelly、hexo。这里我们使用hexo管理之前建好的github个人网站。
hexo支持markdown语法，提供主题封装、网站预览、项目发布等功能。
使用hexo建站的步骤包括：安装hexo、创建本地的github个人博客、使用hexo对github上的个人博客进行覆盖更新。

### 参考文章
---
- [windows 下安装nodejs](http://jingyan.baidu.com/article/b0b63dbfca599a4a483070a5.html)
- [史上最详细“截图”搭建Hexo博客并部署到Github](http://jingyan.baidu.com/article/d8072ac47aca0fec95cefd2d.html)
- [HEXO+Github,搭建属于自己的博客](http://www.jianshu.com/p/465830080ea9)
- [hexo博客搭建时遇到的一些问题](http://chitanda.me/2015/06/11/tips-for-setup-hexo/)




### 安装hexo
---
- 安装windows版Nodejs，在CMD测试node和npm命令查看安装。
- 在Git bash中运行npm install -g hexo以安装hexo，-g表示global全局安装。运行hexo查看安装。
- 在一个文件夹打开git bash，运行hexo init来下载hexo示例的git项目。
- 运行hexo generate进行项目编译，运行hexo server发布项目预览。访问localhost:4000查看站点。
	

### 使用hexo建站
---
- 设置hexo发布项目到github。
	修改工程下的_config.yml文件，添加发布到github的地址和分支。再运行下面一行命令安装项目发布插件hexo-deployer-git。

```` bash
     deploy:
       type: git
       repository: https://github.com/gatewayzy/gatewayzy.github.io.git
       branch: master
````
```` bash
npm install hexo-deployer-git –-save
````
	
- 运行hexo generate生成静态页面到public目录，运行hexo deploy将项目发布到github私人博客中。访问私人博客站点即可。
- 注意deploy会覆盖之前的github个人博客中原有的文件，所以建议先将repo中的项目备份，等完全理解了hexo的发布机制再运行deploy，否则会丢失一些配置文件。
- 为了进一步理解和编辑个人站点，需要对hexo的站点管理机制进一步的说明。

	
### hexo工程目录结构
---
-  node_modules 存放hexo程序模块。
-  public 存放发布的站点文件，包括主页index.html、js、css、所有html、归类的html等。
-  scaffolds 存放md模板，包括draft.md、page.md、post.md，在hexo new post操作生成的post将使用post.md的内容，依次类推，可以自定义模板。
-  source 存放博客的md源文件，不同的draft、post等存放在对应的_drafts, _posts等目录下，在hexo generate的时候，所有的md源文件生成详细的html，所有的html都会依据hexo主题样式生成目录放到index.html中，然后可以链接查看每一篇详细的html。

*按需拷贝到source下的配置有：CNAME（用于域名解析）、favicon.ico（站点icon）、LICENSE（开源协议）、params.json（站点生成page的配置）、.gitignore（好像hexo自动忽略该文件）、README.md（项目readme）。
	配置文件等应该放到source中而不是public中，因为hexo clean会删除所有的public文件。*
-  themes 存放hexo的主题样式，每个主题样式都有readme对主题的配置升级等操作进行了说明。
-  _config.yml 站点配置文件，包括网站开发人员信息、插件、主题等重要配置。


### hexo 命令解析
---
- 创建新文章，如下命令将会在source/_posts目录下创建相应的”ahexo.md”。

```` bash	
hexo new “ahexo” 	#创建新文章，默认是post，如果是page、draft需指明
````
- 编辑ahexo.md，使用markdown语法即可。
- 生成静态页面，将会在public目录下生成html。在public目录下的index.html将会是博客站点的主页，里面包含所有hexo文章的摘要，具体内容和样式将根据hexo的主题样式生成。Public目录下还有hexo主题所用的css、js、2016等文件，其中2016等时间文件中存放的是各个详细的博客文章html。
	
````bash		
	hexo generate	#生成静态页面
````
- 发布项目到github个人站点，将public文件内容上传到github 个人仓库中。特定的配置可以放在source目录下，generate会自动放到public中。如果运行下面的程序提示未找到git命令，可能需要npm安装插件 `npm install hexo-deployer-git --save`。

````bash		
	hexo deploy	#发布项目到github个人站点
````

### hexo常用命令
---
hexo部分常用命令如下。由于默认hexo不发布草稿，所以草稿类似于“私密文章”，查看草稿需强制发布草稿，且`hexo d`不会上传草稿到github。

```bash  
npm install hexo -g #安装 –g表示全局global   
npm update hexo -g #升级    
hexo init #初始化  
hexo n "我的博客" == hexo new "我的博客" #新建文章  
hexo p == hexo publish  
hexo g == hexo generate#生成  
hexo s == hexo server #启动服务预览  
hexo d == hexo deploy#部署  
hexo server #Hexo 会监视文件变动并自动更新，您无须重启服务器。  
hexo server -s #静态模式  
hexo server -p 5000 #更改端口  
hexo server -i 192.168.1.1 #自定义 IP  
hexo clean #清除缓存 网页正常情况下可以忽略此条命令  
hexo generate --watch #监视文件变动  
hexo generate –deploy #完成后部署 
hexo s --drafs #强制发布草稿
```

### hexo使用细节
---
#### 开启评论、添加分类、标签格式
- 开启评论、添加分类和标签格式需要在scaffolds目录中修改相应的模板，如post.md，示例设置如下。
- 注意：设置完成后，对新建的文章才有效，新建完成后，用hexo generate生成文件时会创建categories目录，但是分类页面不会创建。
	
``` bash
---
title: {{ title }}
date: {{ date }}
updated: {{ date }}
comments: true
categories:
tags:
---
```

#### 文章摘要	
首页中的文章摘要可以在对应的md源文件中添加`<!-- more -->`，那么这一行的上部分会作为摘要显示在首页，查看详细内容会查看全文(含摘要)。
	
```bash
---
title: 搭建博客站点
date: 2016-08-06 14:08:36
comments: true
categories: 我的博客
tags: 
- blog
- github pages
- github
- hexo
---
**说明：**介绍建立个人blog站点的主要流程，使用github pages以及hexo进行实现。
<!-- more -->
```

#### 设置网站中的分类、关于、标签对应的链接页面
- 分类、关于、标签对应的目录下不会自动创建index.html，所以对应的链接会提示找不到。
- 需要创建相应的页面(page)，hexo会自动创建页面，运行命令如下。

```bash
hexo n page "about"
hexo n page "categories"
hexo n page "tags"
```
	
> tags页面(sources/tags/index.md)默认就好，但需要指明`type: "tags"`，hexo会自动生成标签云。
> categories页面(sources/categories/index.md)默认就好，但需要指明`type: "categories"`，hexo会自动生成分类列表。
> about页面(sources/about/index.md)需要自己编写内容。

```bash
---
title: tags
date: 2016-08-08 19:32:59
updated: 2016-08-08 19:32:59
type: "tags"
categories:
tags:
comments: false
---
```

#### 添加github的README
- 由于README.md会被解析成html，所以可以把md后缀改为MDOWN，让hexo不解析，而github可以解析。

#### hexo删除文章
- 删除文章可以通过直接删除source中对应的文件夹或者文件，使用`hexo clean`删除db缓存、文件缓存，重新发布即可，tags等也会自动更新。
- hexo支持实时更新，源文件的增改删操作可以不用重启server。 

### hexo高级配置
---
#### 参考文章
- [开始使用NexT](http://theme-next.iissnan.com/getting-started.html)
- [在hexo中加入多说评论](http://www.lichanglin.cn/%E5%9C%A8hexo%E4%B8%AD%E5%8A%A0%E5%85%A5%E5%A4%9A%E8%AF%B4%E8%AF%84%E8%AE%BA/)
- [多说回复后显示浏览器及操作系统信息（Useragent）](http://ssk.91txh.com/209)

#### 主题修改
- 使用了参考文章中的NexT主题。

#### 插件配置
- 创建多说评论网站，修改`themes/next/layout/_partials/comments.swig`内容如下。

```bash
{% if page.comments %}
  <div class="comments" id="comments">
      	<!-- 多说评论框 start -->
		<div class="ds-thread" data-thread-key="{{ page.path }}" data-url="{{ page.permalink }}"></div>
		<!-- 多说评论框 end -->
		<!-- 多说公共JS代码 start (一个网页只需插入一次) -->
		<script type="text/javascript">
			var duoshuoQuery = {short_name:"这里是多说生成的名称"};
			(function() {
				var ds = document.createElement('script');
				ds.type = 'text/javascript';ds.async = true;
				ds.src = (document.location.protocol == 'https:' ? 'https:' : 'http:') + '//static.duoshuo.com/embed.js';
				ds.charset = 'UTF-8';
				(document.getElementsByTagName('head')[0] || document.getElementsByTagName('body')[0]).appendChild(ds);
			})();
		</script>
		<!-- 多说公共JS代码 end -->
  </div>
{% endif %}
```

- 自定义多说评论的样式与头像
	- **头像设置。**头像存为`source/statics/images/avatar.png`，这个目录是我自己建的，hexo g的时候会生成`public/statics`，我用来存放图片、js等内容。头像的指定是在主题配置中设置`avatar: /statics/images/avatar.png`。
	- **多说评论显示userAgent信息(浏览器和OS信息)。**
		- 将多说的embed.js本地化，添加到主题中，可以按照[开始使用NexT](http://theme-next.iissnan.com/getting-started.html)文中的方式配置。我是直接修改`themes/next/layout/_partials/comments.swig`中的embed.js的链接，改成我的博客地址：`ds.src = (document.location.protocol == 'https:' ? 'https:' : 'http:') + '//bebetter.site/statics/js/embed.js';`
		- 再按照[多说回复后显示浏览器及操作系统信息（Useragent）](http://ssk.91txh.com/209)文中的描述，将js代码添加到embed.js，并把样式代码添加到`themes/next/source/css/_custom/custom.sty`中。
		- 这时需要将statics目录及文件上传到github站点上，需要等几分钟才能请求到js等资源，这时候再评论的时候就可以看到浏览器信息和OS信息了。

> 注意：在embed.js本地化的过程中会遇到问题：hexo默认会将source下的所有文件都进行渲染，导致的问题就是README.md被渲染成html，embed.js被渲染改变了内容等问题。这时候可以修改hexo全局配置中的skip_render，以source为默认路径，如配置`skip_render:statics/**`。参考文章[Hexo编译文档时把JS给写乱了，请问怎么解决？](https://segmentfault.com/q/1010000003957602/a-1020000003957954)。

#### 友情链接
- 修改next主题配置如下。

```bash
# Blogrolls
links_title: 友情链接
#links_layout: block
links_layout: inline
links:
  #Title: http://example.com/
  my-github: https://github.com/gatewayzy
  百度: http://baidu.com/
```

#### 支持LaTex公式
* 开启next主题配置中的公式插件配置。
* 不同主题有相应的配置方式，默认主题landscape可参看文章：[在hexo博客中使用Mathjax写LaTex数学公式](http://blog.csdn.net/sherlockzoom/article/details/43835613)
* 公式举例如下。更多公式请查看LaTex使用教程。
* 如果没有效果，应该确实是否是网络无法访问cdn.mathjax.org对应的js，建议开启全局代理模式以访问国外服务器。
	* $ 表示行内公式：`$E=mc^2$`  $E=mc^2$
	* $$ 表示整行公式：`$$\sum_{i=1}^n a_i=0$$` $$\sum_{i=1}^n a_i=0$$
	* `$$f(x_1,x_2,\ldots,x_n) = x_1^2 + x_2^2 + \cdots + x_n^2$$` $$f(x_1,x_2,\ldots,x_n) = x_1^2 + x_2^2 + \cdots + x_n^2$$
	* `$$\alpha (x)=\sum_{m=0}^{m<2^2} \alpha_2m +\mu$$` $$\alpha (x)=\sum_{m=0}^{m<2^2} \alpha_2m +\mu$$
	* `$$dudx|x=0$$` $$dudx|x=0$$
	* `$$f(x,y,z)=3y2z(3+7x+51+y2)$$` $$f(x,y,z)=3y2z(3+7x+51+y2)$$
	* `$$\mbox{对任意的$x>0$}, \mbox{有 }f(x)>0. $$`  $$\mbox{对任意的$x>0$}, \mbox{有 }f(x)>0. $$
	* `$$\LaTeX \ast\TeX$$` $$\LaTeX \ast\TeX$$
	* `$$x=\frac{-b\pm\sqrt{b^2-4ac}}{2a}$$` $$x=\frac{-b\pm\sqrt[5]{b^2-4ac}}{2a}$$
```
# MathJax Support
mathjax:
  enable: true
  cdn: //cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML
```







