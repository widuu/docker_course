#Using Supervisor with Docker

>注：

>这个例子环境是假设你已经运行了docker进程，更多详细信息请查看运行例子，如果你不喜欢sudo，你可以用户授权命令和docker组

传统的docker启动时只运行一个进程，例如apache进程或者ssh服务进程。但是镜像你想要在一个容器中运行多个进程。有许多方法可以做到这一点，譬如使用一个简单的bash脚本作为容易的CMD指令来管理

在这个例子中，我们将利用线程管理工具，管理多个进程在我们的容器内运行，使用Supervisor使我们更好的控制、管理和重启这个进程，这个将演示我们要安装和管理一个SSH后台进程和apache后台进程

##创建Dockerfile

让我们为新镜像创建一个基础的Dockerfile

	FROM ubuntu:latest
	MAINTAINER examples@docker.io
	RUN echo "deb http://archive.ubuntu.com/ubuntu precise main universe" > /etc/apt/sources.list
	RUN apt-get update
	RUN apt-get upgrade -y
	
##安装Supervisor

现在我们安装我们的ssh和apache 以及Supervisor在我们的容器内

	RUN apt-get install -y openssh-server apache2 supervisor
	RUN mkdir -p /var/run/sshd
	RUN mkdir -p /var/log/supervisor
	
这里我们安装openssh-server、apache2和supervisor（提供supervisor进程）我们还需要创建两个目录运行ssh进程和supervisor进程

##添加一个supervisor的配置文件

现在让我们天津唉一个supervisor的配置文件，默认的配置文件叫做supervisor.conf,路径位置是etc/supervisor/conf.d/。

	ADD supervisord.conf /etc/supervisor/conf.d/supervisord.conf
	
让我们看看添加什么配置到supervisord.conf文件

	[supervisord]
	nodaemon=true
	
	[program:sshd]
	command=/usr/sbin/sshd -D
	
	[program:apache2]
	command=/bin/bash -c "source /etc/apache2/envvars && /usr/sbin/apache2 -DFOREGROUND"
	
supervisord.conf配置文件包含supervisord配置和进程管理，第一块[supervisord]提供本身配置文件。然后我们使用一个指令，来告诉supervisord运行交互式的nodaemon而不是daemonize。

下边两块是管理服务控制的，每一块是一个单独的进程，块包含指令，每个流程的开始决定于它指定了什么指令

##开放端口和运行Supervisor

现在我们完成Dockerfile指定开发一些端口，并且用CMD指令来在容器启动开时开始Supervisor。

	EXPOSE 22 80
	CMD ["/usr/bin/supervisord"]
	
现在我们的容器开启了22和80端口，当容器运行时，我们运行/usr/bin/supervisord

##创建我们的容器

我们现在可以创建一个新的容器

	sudo docker build -t <yourname>/supervisord .
	
##运行我们的Supervisor容器

一旦我们有了镜像我们就可以启动一个容器了

	sudo docker run -p 22 -p 80 -t -i <yourname>/supervisor
	2013-11-25 18:53:22,312 CRIT Supervisor running as root (no user in config file)
	2013-11-25 18:53:22,312 WARN Included extra file "/etc/supervisor/conf.d/supervisord.conf" during parsing
	2013-11-25 18:53:22,342 INFO supervisord started with pid 1
	2013-11-25 18:53:23,346 INFO spawned: 'sshd' with pid 6
	2013-11-25 18:53:23,349 INFO spawned: 'apache2' with pid 7
	
我们用一个新的容器来交互式的使用docker运行命令。容器已经运行Supervisor、ssh和apache进程，我们指定了开放端口22和80，从这里我们确定开放端口连接到一个或者所有的ssh和apache进程。
