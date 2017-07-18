---
title: Docker
comments: true
date: 2017-01-12 13:58:04
updated: 2017-01-12 13:58:56
categories: Softwares
tags:
- Docker
---

**说明：**Docker配置和使用。
<!-- more -->

* 运行环境：
    * windows 10为主 / ubuntu 14.04
    * docker 1.12.3
* 参考文章
	* [docker store](https://store.docker.com/)
	* [docker hub](https://hub.docker.com/explore/)
    * [docker中文api](http://www.docker.org.cn/book/docker/what-is-docker-16.html)
    * [docker英文api](https://docs.docker.com/engine/reference/builder/)
    * [阿里云管理控制台(云服务、镜像站点等)](https://cr.console.aliyun.com)
    * [docker-image container 基本操作 -常用命令](http://www.cnblogs.com/xiadongqing/p/6144053.html)
    * [菜鸟教程--docker](http://www.runoob.com/docker/docker-tutorial.html)

---
## 介绍

### 简介
* docker基于Go语言开发。Docker 可以让开发者打包他们的应用以及依赖包到一个轻量级、可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。
* 容器的性能开销极低，容器之间相互独立。Docker成本低。一个只能开数个虚拟机的电脑一般能开数十个 container ，且 container 的启动时间一般在数秒内，比虚拟机快得多。另外，由于 docker 的 image 除了一些特殊的基础镜像外基本都是增量镜像，因此重复部分不会耗费额外的资源，所以几个看起来有数 g 的 image 如果里面使用的基础镜像有重复部分（大部分情况下都会有部分重复），那么它们实际占用空间将会小得多。
* docker 虽然是一个虚拟化技术，但使用上却更像是在管理系统软件或者代码。使用的命令也是经常接触的，因此程序员会感到很亲切且容易上手。
* 同时由于可以使用 Dockerfile 进行 image 的构建，且 docker hub 支持从 github 等地方自动根据 Dockerfile 进行构建，所以 docker 把运行环境也集成到 CI 中了。
* 美中不足的是由于 docker 目前仅支持 linux 上的容器技术，因此它要在非 Linux 系统下运行必须加多一个虚拟机层。这会造成一些在 Linux 上运行不会出现的问题（ip 地址、硬件资源、文件映射等），同时由于基于 linux ，一些 windows 的程序会水土不服，泛用性比虚拟机差一些。

### 相关术语
* container：容器。
	* 容器可以看做是一个独立的主机。 container 的创建通常有一个 image 作为其模板。类比成虚拟机的话可以理解为 image 就是虚拟机的镜像，而 container 就是一个个正在运行的虚拟机。一个虚拟机镜像可以创建出多个运行的虚拟主机容器且相互独立。 
	* container 一旦创建如果没有用 rm 命令移除，将会一直存在。所以用完后记得删除。
* image：镜像。
	* 镜像相当于 container 的模板，container 创建后里面有什么软件完全取决于它使用什么 image 。image 可以通过 container 创建（相当于把此时 container 的状态保存成快照），也可以通过 Dockerfile （一个文本文件，里面使用 docker 规定的一些写法）来创建。使用 Dockerfile 创建image时可以对环境配置和代码一起进行管理，使用container生成image的方法只能生成container中的东西。
* registry
	* 存放镜像的仓库。只要能连接到 registry 每个人都可以很方便地通过 pull 命令从仓库中获取镜像。docker 默认使用的仓库是 [docker hub](https://hub.docker.com/)，国内可以使用 DaoCloud 来建立 Mirror 连接到 docker hub，进而加快获取 image 的速度。
* docker client
	* docker客户端。通过命令行或者python等工具访问docker的守护进程和主机容器。
* docker host
	* docker主机，服务器后端。执行docker守护进行和容器。
* boot2docker
	* 一个轻量级 linux 虚拟机，主要是为了让非 linux 系统也能用上 docker 。它实质上是一个 virtualbox 虚拟主机+一个能管理这个虚拟主机的命令行工具。由于这个虚拟主机的存在，在非 linux 系统上 container 需要获取一些物理系统资源（如 usb 设备）时不仅需要配置 docker 命令，还需要配置 boot2docker 这个虚拟主机的资源配置。
* Docker Machine
	* Docker Machine是一个简化Docker安装的命令行工具，通过一个简单的命令行即可在相应的平台上安装Docker，比如VirtualBox、 Digital Ocean、Microsoft Azure。


---
## 安装配置

### Windows下使用Docker-for-Windows
* Docker-for-Windows自带了docker实现，并且提供了可视化的管理和设置界面，并提供可视化的仓库管理软件Kitematic。
* 从百度或者阿里云等地方下载msi安装文件，安装完即可。
	* 测试Docker-for-Windows：启动图标，注册账号并登录。
	* 测试docker：使用cmd或者powershell等，运行`docker --version`，查看版本。
	* 连接Kitematic：Docker-for-Windows托盘图标右键有Kitematic仓库，为了使用该镜像库，需要下载[Kitematic-Windows](https://github.com/docker/kitematic)并解压到 `C:\Program Files\Docker\Kitematic`，即可使用Kitematic。

### Ubuntu下使用docker

这一部分是我后来写的，所以内容比较乱...
#### 基本安装配置
* 下载安装docker: `curl -sSL https://get.docker.com/ | sh`
* 添加docker到组：如果不将docker添加到group的话，每次运行都需要sudo docker，所以可以`sudo usermod -aG docker yourgroup`，然后logout。
* 启动docker： `sudo service docker start|stop|restart...`，测试docker `docker run hello-world`，无法下载的话应该是被墙了，[Docker学习笔记 — 配置国内免费registry mirror](http://blog.csdn.net/qq_26091271/article/details/51501768)
* docker示例：
	* 运行程序容器 `docker run ubuntu:15.10 /bin/echo "Hello world"` 运行容器，没有镜像会自动下载。然后在容器里执行 /bin/echo "Hello world"。
	* 运行交互式容器 `docker run -i -t ubuntu:15.10 /bin/bash` -i是对容器内的stdin进行交互，-t是在容器内指定一个终端。可以在容器内运行命令。用exit退出容器。
	* 后台运行容器 `docker run -d ubuntu:15.10 /bin/sh -c "while true; do echo hello world; sleep 1; done"`
* 卸载docker：`sudo apt-get purge -y docker-engine`、`sudo apt-get autoremove -y --purge docker-engine`、`sudo apt-get autoclean`
### 搭建私人仓库
* 下载registry镜像 docker pull registry
* 创建registry容器 `docker run -d -v /opt/registry:/var/registry -p 5000:5000 --restart=always --name registry-tcm registry` 
* 下载webUI镜像 docker pull hyper/docker-registry-web 
* 创建webUI容器 `docker run -it -p 8080:8080 --name registry-web --link registry-tcm --restart=always -e REGISTRY_URL=http://10.15.82.66:5000/v2 -e REGISTRY_NAME=10.15.82.66:5000 hyper/docker-registry-web`
* 访问`http://10.15.82.66:8080/`，浏览image，进行pull等操作。

### docker配置仓库加速器：
* 常见仓库站点
    * 官方仓库比较正规，但可能有速度慢[docker hub](https://hub.docker.com/explore/)或者linux下无法访问的问题。
    * [阿里云docker](https://dev.aliyun.com/search.html)。也可以注册[阿里云](https://cr.console.aliyun.com)账号，在管理控制台中，查看docker镜像仓库。
	* [国内的DaoCloud](http://www.daocloud.io/)。注册账号，然后配置 Docker 加速器。
* docker的配置
	* 参考官方api[Configure the Docker daemon](https://docs.docker.com/registry/recipes/mirror/#configure-the-docker-daemon)
	* 参考文档[Docker 镜像使用帮助](https://lug.ustc.edu.cn/wiki/mirrors/help/docker)
	* linux下修改/etc/docker/daemon.json，添加`{ "registry-mirrors": ["https://<my-docker-mirror-host>"] }`。没有的话可以新建。不同的版本可能使用/etc/default/docker 进行配置，但这是过时的用法，还是推荐daemon.json方式，示例配置见附录。
	* windows下修改C:\ProgramData\docker\config\daemon.json，如果是用docker for windows等软件就可以设置软件的setting--daemon。
	* Docker 的Daemon的配置基本就是上面所述的方法，以后简略，还可以参考[Docker 加速器](http://guide.daocloud.io/dcs/daocloud-9153151.html)。



---
## 常用命令
 
* 参考文章：[Docker 命令大全](http://www.runoob.com/docker/docker-command-manual.html)
 
### 通用
* docker version 查看docker版本
* docker info 查看docker信息
* `docker --help` 查看docker命令提示
* docker help build 查看create命令的帮助提示

### 镜像仓库
* 登录仓库： docker login -u username -p password  登录一个docker仓库，不指定仓库地址则用官方仓库docker hub
* 登出仓库： docker logout 登出仓库
* 查找image： docker search ubuntu 查找仓库中的相关镜像。`docker search --filter=stars=100 ubuntu`过滤star数超过100的。
* 获取image： docker pull ubuntu:16.04 从登录库中下载ubuntu且tag是16.04的，tag默认是latest，但latest只是个名称，不代表最新。如 `docker pull -a java`下载所有java的tagged的镜像，--disable-content-trust:忽略镜像的校验,默认开启
* 上传image： docker push acom/ubuntu16.10 上传该image到已经登录的仓库中，**不指定tag则上传所有tag**。可--disable-content-trust。

### 本地image镜像
* images 查看所有image： docker images 查看本地所有的image。--digests显示摘要信息，-a显示本地所有镜像(含中间映象层)。docker images ubuntu  过滤出含有ubuntu名称的images。
* build 使用Dockerfile创建镜像，[可选参数比较多](http://www.runoob.com/docker/docker-build-command.html) 
	* docker build -t com/ubuntu15.10:v1 ./image1/ 用image1文件夹下Dockerfile创建image，名称叫做acom/ubuntu15，tag为v1，-t指定生成target名称。
	* docker build github.com/creack/docker-firefox  使用URL的 Dockerfile 创建镜像
* tag 标记分库： docker tag acom/ubuntu16.10 tag v1d5f 给镜像acom/ubuntu16.10打上tag v1d5f，将其归入某一个仓库。acom应该使用ip或者网址，如果不是的话在push的时候会提交到docker.io，会提示没有提交权限。
* rmi 删除本地image： docker rmi acom/ubuntu16.10:v1 删除本地image。-f强制删除，--no-prune不移除其过程镜像。
* history 历史创建信息： docker history ubuntu  查看镜像的创建历史
* save 将镜像保存成tar归档文件： docker save -o my_ubuntu_v3.tar runoob/ubuntu:v3  用-o指定保存到的文件
* import 从归档文件中创建镜像： docker import my_ubuntu_v3.tar runoob/ubuntu:v4 

* build demo
	* 每一个指令都会在镜像上创建一个新的层，每一个指令的前缀都必须是大写的。
	* 第一条FROM，指定使用哪个镜像源
	* RUN 指令告诉docker 在镜像内执行命令，安装了什么
	* 然后，我们使用 Dockerfile 文件，通过 `docker build -t runoob/centos:6.7 . `构建一个镜像，-t指定生成target镜像名称，.指定Dockerfile所在目录，或者指定配置文件名`docker build -t deepqa:latest -f Dockerfile.gpu ./` 使用Dockerfile.gpu配置。

```
## Dockerfile Demo: to build DeepQ&A container image with tensorflow
FROM python:3.5.2
## Dependencies
RUN \
  apt-get -qq -y update && apt-get -y install unzip
RUN  \
  pip3 install -U nltk \
  tqdm \
  django \
  asgi_redis \
  channels && \
  python3 -m nltk.downloader punkt
## Tensorflow
ARG TF_BINARY_URL=https://storage.googleapis.com/tensorflow/linux/cpu/tensorflow-1.0.0-cp35-cp35m-linux_x86_64.whl
RUN \
  pip3 install -U $TF_BINARY_URL
COPY ./ /root/DeepQA
## Run Config
# You should generate your own key if you want deploy it on a server
ENV CHATBOT_SECRET_KEY="e#0y6^6mg37y9^+t^p_$xwnogcdh=27)f6_=v^$bh9p0ihd-%v"
ENV CHATBOT_REDIS_URL="redis"
EXPOSE 8000
WORKDIR /root/DeepQA/chatbot_website
RUN python3 manage.py makemigrations
RUN python3 manage.py migrate
# Launch the server
CMD python3 manage.py runserver 0.0.0.0:8000
```

### container容器
* 查看所有运行中的容器： docker ps 查看所有运行中的容器的状态
* 创建容器： `docker create --name tf1 tf/tensorflow` 创建容器并指定名称，create语法与run相同
* 查看容器列表： docker ps 查看所有运行中的容器，-l查看最后常见的容器，-a查看所有容器。
* 创建并运行容器： `docker create -it -p 8080:8888 -p 8081:6006 -e PASSWORD=123456 --name=tf1 tensorflow/tensorflow:latest` 若找不到容器会创建默认容器，若有指定的容器会默认使用其创建时的配置。语法为 docker run [OPTIONS] IMAGE [COMMAND] [ARG...]，options常见选项：
	* -a stdin 指定标准输入输出内容类型，可选STDIN/STDOUT/STDERR 
	* -d 后台运行容器，并返回容器id，detach
	* -i 以交互模式运行容器，常常与-t同时使用
	* -t 为容器重新分配一个伪输入终端，常与-i同时使用
	* `--name=tf1` 指定容器运行时的name，又如--name tf2，不指定时会随机生成name。
	* `--dns 8.8.8.8` 指定容器dns，默认同宿主一致
	* `--dns-search a.com` 指定容器dns搜索域名，默认和宿主一致
	* -h host1 指定容器的hostname为host1
	* -e username="user1" 设置环境变量
	* `--env-file=[]` 从指定文件读取环境变量
	* `--cpuset="0-2"` 设置运行容器的cpu，eg. `--cpuset="0,2,3"`
	* -m 设置容器使用的最大内存
	* `--net="bridge"` 设置容器联网的方式，可选bridge/host/none/container
	* `--link=[]`  添加链接到另一个容器
	* `--expose=[]`  开放一组端口
	* -p 宿主port:containerPort  将宿主port映射到容器port，访问宿主的宿主port以访问容器的port。多个如 -p 8080:8888 -p 8081:6006
	* -P 随机使用主机的端口映射到容器的80端口
	* -v /tmp:/tmp/data  将主机的/tmp映射到容器的/tmp/data
* 容器管理：
	* 可以使用容器ID或者容器Name来标识一个容器，可以先用docker ps或者docker ps -a查看容器信息，再进行操作。
	* 容器生命周期： 
		* docker stop|stop|restart|kill|rm 6e5f/tf1   操作成功时返回其ID或者name。kill是强制关闭，rm是删除。
		* docker pause|unpause  暂停|恢复容器中的所有进程
		* docker exec -it mynginx /bin/sh /root/runoob.sh  在容器中运行sh runoob脚本，且使用交互终端模式。
	* 容器信息
		* docker inspect tf3  查看容器配置信息
		* docker port 5e6f  查看容器的端口映射配置
		* docker top tf1  查看容器当前的进程列表
		* docker logs tf3  查看容器的日志。-f表示跟踪输出，-t显示时间戳，--since限制日志开始时间，--tail限制N条输出。
		* docker export -o mysql-`date +%Y%m%d`.tar a404c6c174a2 将id为a404c6c174a2的容器保存为mysql.date.tar文件 
		* docker ps 查看所有运行中的容器，-l查看最后常见的容器，-a查看所有容器。
		* docker events 查看容器实时的事件，运行该句再开关容器试试。`docker events -f "image"="mysql:5.6" --since="1467302400" --until="1.."` -f过滤事件，--since过滤起始时间，--until过滤截止时间。
		* docker wait tf1  阻塞运行直到目标容器停止，并打印退出代码。
	* 容器文件控制
		* docker diff tf1  查看容器相对于image模板的文件修改
		* docker cp /www/home tf1:/www/  将主机文件夹拷贝到tf1的目录下。-L保持源目标中的链接link。
		* docker commit ubuntu acom/ubuntu14.04:latest 将容器ubuntu创建为image，名称叫acom/ubuntu，tag为latest。-a作者，-c用Dockerfile指令来创建镜像，-m说明信息，-p在commit时暂停容器。 `docker commit -a "uno.com" -m "my tf" tf1  tf:v1`



---
## Docker使用demo

### hello-world
* 运行`docker run hello-world`查找image hello-world，找不到会自动下载image，比较慢。下载完会接着实例化容器并运行，而且在Kitematic中可以看到本地该image和容器，并有对应的信息、管理、env、端口等设置。

### ubuntu
* 从阿里云搜索对应的image的地址
* 运行 `docker pull registry.cn-hangzhou.aliyuncs.com/ubuntu-14-04/ubuntu14.04` Kitematic的my images中可看到该image或运行docker images。


### tensorflow
* 参考文章: [TensorFlow in hub.docker](https://hub.docker.com/r/tensorflow/tensorflow/)
* 使用CPU TF
	* -e配置jupter密码
	* 访问浏览器，输入密码登录，新建python工程，书写tf代码，运行并查看结果，可参考[基于Docker的TensorFlow机器学习框架搭建和实例源码解读](http://blog.csdn.net/dream_an/article/details/55520205)

```
docker pull tensorflow/tensorflow
docker create -it -p 8080:8888 -p 8081:6006 -e PASSWORD=123456 --name=tf1 tensorflow/tensorflow:latest
docker start tf1
echo 'http://localhost:8080/-------------------'
```

* 使用GPU TF
	* 安装[nvidia-docker](https://github.com/NVIDIA/nvidia-docker)，以ubuntu为例。

```
echo 'Install nvidia-docker and nvidia-docker-plugin-------------------'
wget -P /tmp https://github.com/NVIDIA/nvidia-docker/releases/download/v1.0.1/nvidia-docker_1.0.1-1_amd64.deb
sudo dpkg -i /tmp/nvidia-docker*.deb && rm /tmp/nvidia-docker*.deb
echo 'Test nvidia-smi with nvidia-docker-------------------'
nvidia-docker run --rm nvidia/cuda nvidia-smi

nvidia-docker run -it -p 8888:8888 tensorflow/tensorflow:latest-gpu
echo 'http://localhost:8888/-------------------'
```



---
## Docker私有仓库

私人仓库的一般使用流程为：创建私人仓库、准备要上传的镜像、仓库管理（上传镜像、查看镜像、下载镜像）。__下文的‘私服’、‘私库’、‘私人仓库’均指代‘私有仓库’。__

### 私有仓库的使用
* 构建私有仓库需要使用registry镜像。
	* Registry镜像在github上有两份代码：老代码库和[新代码库](https://github.com/docker/distribution)。老代码是采用python编写的，存在pull和push的性能问题，出到0.9.1版本之后就标志为deprecated，不再继续开发。从2.0版本开始在新代码库进行开发，新代码库采用go语言编写，修改了镜像id的生成算法、registry上镜像的保存结构，大大优化了pull和push镜像的效率。
* 部署registry容器
	* pull+create+start `docker pull registry`、`docker create -p 5000:5000 --restart=always --name registry registry`再`docker start registry`
	* 或者直接run `docker run -d -v /opt/registry:/var/registry -p 5000:5000 --restart=always --name registry registry`，--restart表示docker运行时自动启动该容器。
	* 查看运行状态 docker ps，浏览器访问`http://127.0.0.1:5000/v2/_catalog`，查看当前私库的所有镜像，使用`http://127.0.0.1:5000/v2/tensorflow/tags/list`查看其tag的列表。更多api使用请参考[HTTP API V2](https://docs.docker.com/registry/spec/api/)。
* 发布镜像
	* 主机上下载或生成一个image，如hello-world
	* docker tag标记为将会推送的image： docker tag hello-world 127.0.0.1:5000/hello-world。查看本地images会看到tagged的image
	* docker push 127.0.0.1:5000/hello-world  发布到仓库中，访问`http://127.0.0.1:5000/v2/_catalog`查看仓库中的镜像。
	* 删除本地image，再试验从私服 docker pull 127.0.0.1:5000/hello-world
* 删除镜像
	* 私有仓库v2版本中的镜像，官方不建议删除，但是也提供了删除接口。删除的原理就是把索引删掉，但磁盘上的数据是删不掉的。这是由于各个镜像之间的不同层共用的关系，可能导致删除一个镜像后其余的镜像也无法使用。
	* [registry v2 api](https://github.com/docker/distribution/blob/master/docs/spec/api.md#deleting-an-image)中要求  `DELETE /v2/<name>/manifests/<reference>` For deletes, reference must be a digest。但是我pull下来看到digest之后，尝试删除`curl -X DELETE 127.0.0.1:5000/v2/hello-world/manifests/sha256:f3b3b...` 提示错误"unsupported"，也没找到合适好的解决方法。粗暴的方法就是不管它，或者重建registry...

### 使用私人仓库示例

下面以windows为例，详细记录私库的建立、可视化管理和使用。

#### 创建私服
* 创建私库容器 `docker run -d -p 5000:5000 --restart=always --name my-registry registry` 。
* 可以启动、修改一个容器（docker start tf1.2.0.py3、 docker diff tf1.2.0.py3），改成自己想要的配置。
* 将容器生成镜像，并tag到指定域名
	* docker commit tf1.2.0.py3 tcm/tf-1.2.0-rc2-py3:latest  从容器生成一个镜像
	* docker tag tcm/tf-1.2.0-rc2-py3 10.15.62.118:5000/tf:1.2.0-rc2-py3  将镜像进行tag，标记向**指定域名(不是乱写的)**推送
	* 也可以直接将上面两步合并 docker commit tf1.2.0.py3 10.15.62.118:5000/tf:1.2.0-rc2-py3
* docker images  查看本地所有镜像。不需要的tag镜像可以rmi，如docker rmi tcm/tf-1.2.0-rc2-py3:latest
* docker push tf1.2.0.py3 10.15.62.118:5000/tf:1.2.0-rc2-py3  向指定ip仓库(私服)进行push，如提示https问题，参考问题解决"push到私有仓库失败，提示https问题"。
* 访问`http://10.15.62.118:5000/v2/_catalog` 查看私有仓库的所有镜像。

#### registry的webUI管理
* 上面使用浏览器查看仓库的信息比较麻烦，这里使用工具连接到仓库进行web可视化管理。
* 参考文章
	1. [hyper/docker-registry-web](https://hub.docker.com/r/hyper/docker-registry-web/)
	2. [使用官方 docker registry 搭建私有镜像仓库及部署 web ui](http://blog.csdn.net/mideagroup/article/details/52052618)
* **下面以hyper/docker-registry-web为例对私库进行webUI管理**。
* docker-registry-web是docker hub提供的一个webUI工具的image，参考文章中也有相应的官方文档。总体使用流程为：先创建好私人仓库容器，再创建一个hyper/docker-registry-web容器，用后面这个容器去连接管理私库容器即可(支持镜像浏览、tag浏览，不支持删除)。
* 注意：文档中指出，创建的registry容器的name不要使用`registry` ，可以像这样 `docker run -d -p 5000:5000 --restart=always --name registry1 registry`使用registry1等乱七八糟的名字。
* 获取镜像docker pull hyper/docker-registry-web (可跳过该步骤)。
* `docker run -it -p 8080:8080 --name registry-web --link registry1 -e REGISTRY_URL=http://10.15.62.118:5000/v2 -e REGISTRY_NAME=10.15.62.118:5000 hyper/docker-registry-web`
* 访问10.15.62.118:8080，查看私服中所有的镜像和tag信息。可以查看一个image的history等信息，如上面的就是10.15.62.118:5000/hello:latest，可以让别人docker pull(网络可达才行)。
* **遇到的问题**：页面提示“Could not create URI object: Illegal character...”且没有镜像数据，原因是REGISTRY_URL环境变量必须写`http://`，否则找不到。我只写ip:5000/v2就出现了这个问题。

#### 使用私人仓库
* 使用私服一般：配置私服设置、登录私服、查找下载、更新提交。
* 配置私库与登录私库：如何配置登录设置？？？
* 查找下载私库镜像
	* 方法1是使用registry的webUI管理工具，比如使用hyper/docker-registry-web查看私库的所有image和对应的地址。
	* 方法2是登录私库，然后docker search？？？
* 下载使用私服的镜像
	* 首先要知道私服的镜像地址。比如通过webUI查看到想要的image是`10.15.62.118:5000/hello:latest`。
	* 下载镜像：在保证主机可达的情况下，`docker pull 10.15.62.118:5000/hello:latest`即可获取私库镜像。如果提示https错误，参考'问题与解决'设置‘insecure-registries’。
* 更新提交：生成镜像、tag、push...详略。






---
## 问题与解决

### docker for windows无法启动：Docker.Program.Run(IReadOnlyCollection`1 args)
* 问题描述：docker for windows双击图标以启动docker，提示docker服务not running，确定启动后提示错误Docker.Program.Run(IReadOnlyCollection`1 args).
* 解决方法：重装docker for windows（github上有该[issue208](https://github.com/docker/for-win/issues/208)，较多人支持删除`C:\ProgramData\Docker`、`%AppData%/Local/Docker and %AppData%/Roaming/Docker`），我是删除之后没起效果，重装可以。

### push到私有仓库失败，提示https问题
* 这是因为私人的registry服务是http，不是安全的https仓库。
* 解决方法：
	* Ubuntu修改/etc/docker/daemon.json，添加`{"insecure-registries" : ["myregistrydomain.com:5000"]}`，然后docker restart。
	* Windows在C:\ProgramData\docker\config\daemon.json，如docker for windows等软件就设置软件的setting--daemon。（仓库镜像也在这里添加）
	* 参考[Deploying a plain HTTP registry](https://docs.docker.com/registry/insecure/)，原文如下，就不翻译了

```
Edit the daemon.json file, whose default location is /etc/docker/daemon.json on Linux 
  or C:\ProgramData\docker\config\daemon.json on Windows Server. 
If you use Docker for Mac or Docker for Windows, click the Docker icon, choose Preferences, and choose +Daemon.
	{
  		"insecure-registries" : ["myregistrydomain.com:5000"]
	}
If the daemon.json file does not exist, create it....
```

---
## 附录
### window下docker daemon的配置

```
{
  "registry-mirrors": [
    "https://iuu105a7.mirror.aliyuncs.com"
  ],
  "insecure-registries": [
    "10.15.62.118:5000"
  ],
  "debug": true,
  "experimental": true
}
```



