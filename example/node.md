#Node.js Web App

>注：

>这个例子环境是假设你已经运行了docker进程，更多详细信息请查看运行例子，如果你不喜欢sudo，你可以用户授权命令和docker组

这个例子目的是展示如何用父镜像创建自己的镜像，修改和保存一个新的镜像，我们会通过一个简单的web镜像做hello沙箱测试

这个例子的目的是想你展示如何使用Dockerfile来创建自己的docker，我们将会制作一个简单的node.js的hello word web应用运行在centos容器上。有你可以获取所有源代码在[https://github.com/gasi/docker-node-hello.](https://github.com/gasi/docker-node-hello.)

##创建一个Node.js 应用

首先，创建一个package.json文件来作为描述文件和依赖：

	{
	  "name": "docker-centos-hello",
	  "private": true,
	  "version": "0.0.1",
	  "description": "Node.js Hello World app on CentOS using docker",
	  "author": "Daniel Gasienica <daniel@gasienica.ch>",
	  "dependencies": {
	    "express": "3.2.4"
	  }
	}
	
现在，创建一个index.ks文件。。来设置一个web应用使用Express.js 框架：

	var express = require('express');
	
	// Constants
	var PORT = 8080;
	
	// App
	var app = express();
	app.get('/', function (req, res) {
	  res.send('Hello World\n');
	});
	
	app.listen(PORT)
	console.log('Running on http://localhost:' + PORT);

下一步，我们将看到如何使用docker运行这个app在centos容器内,第一步，你需要为你的app创建一个Docker镜像

##创建Dockerfile

创建一个空的Dockerfile文件

	touch Dockerfile
	
用你喜欢的编辑器打开Dockerfile并且添加一行来要求docker的版本来创建镜像：

	# DOCKER-VERSION 0.3.4
	
接下来，定义父镜像用于构建自己的镜像，在这里我们使用Centps(tag:6.4)在[Docker index](https://index.docker.io/):

	FROM centos:6.4
	
因为我们正在创建一个node.js的应用，我们就要先安装node.js使用的npm在你的centos镜像中，node.js运行运行你的应用并且通过npm安装你的app应用程序的依赖包.正确的安装centosnpm包，我们可以使用[Node.js wiki](https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager#rhelcentosscientific-linux-6)的指令：

	# Enable EPEL for Node.js
	RUN     rpm -Uvh http://download.fedoraproject.org/pub/epel/6/i386/epel-release-6-8.noarch.rpm
	# Install Node.js and npm
	RUN     yum install -y npm
	
捆绑你的应用程序源代码在你的docker镜像内，你可以使用ADD指令

	# Bundle app source
	ADD . /src
	
安装你的应用程序的二进制依赖包：

	# Install app dependencies
	RUN cd /src; npm install
	
你的应用绑定8080端口，所以你需要使用EXPOSE指令来映射到docker后台进程

	EXPOSE  8080
	
最后但并不是不重要，定义命令来运行你的应用程序，是用CMD指令来指定运行node 你的应用程序路径，即src/index.js(参见不周我们将源代码添加到容器中)

	CMD ["node", "/src/index.js"]

现在你的Dockerfile像下边这样

	# DOCKER-VERSION 0.3.4
	FROM    centos:6.4
	
	# Enable EPEL for Node.js
	RUN     rpm -Uvh http://download.fedoraproject.org/pub/epel/6/i386/epel-release-6-8.noarch.rpm
	# Install Node.js and npm
	RUN     yum install -y npm
	
	# Bundle app source
	ADD . /src
	# Install app dependencies
	RUN cd /src; npm install
	
	EXPOSE  8080
	CMD ["node", "/src/index.js"]

##创建你的镜像

来到你的Dockerfile目录并运行创建命令来创建一个docker镜像，-t参数让你设置的镜像名称，所以稍后用docker images命令你会很容器找到你的镜像：

	sudo docker build -t <your username>/centos-node-hello .
	
你的镜像现在将会列出来：

	sudo docker images
	
	> # Example
	> REPOSITORY                 TAG       ID              CREATED
	> centos                     6.4       539c0211cd76    8 weeks ago
	> gasi/centos-node-hello     latest    d64d3505b0d2    2 hours ago
	
##运行镜像

用-d运行你的镜像在后台模式下，-p参数会将你容器的私有端口转发到你主机上的共有端口，运行你创建的镜像

	sudo docker run -p 49160:8080 -d <your username>/centos-node-hello
	
打印你的APP输出

	# Get container ID
	sudo docker ps
	
	# Print app output
	sudo docker logs <container id>
	
	> # Example
	> Running on http://localhost:8080
	
##测试

测试你的应用，获得你的应用端口在主机上映射出的端口:

	sudo docker ps
	
	> # Example
	> ID            IMAGE                          COMMAND              ...   PORTS
	> ecce33b30ebf  gasi/centos-node-hello:latest  node /src/index.js         49160->8080
	
上边的例子，docker映射8080端口到容器的49160端口

现在你可以用curl你的app应用(通过`sudo apt-get install curl`安装curl)

	curl -i localhost:49160
	
	> HTTP/1.1 200 OK
	> X-Powered-By: Express
	> Content-Type: text/html; charset=utf-8
	> Content-Length: 12
	> Date: Sun, 02 Jun 2013 03:53:22 GMT
	> Connection: keep-alive
	>
	> Hello World
	
如果你想同学学习运行node.js在centos在docker上，你可以获取全部的源代码在[https://github.com/gasi/docker-node-hello.](https://github.com/gasi/docker-node-hello.)