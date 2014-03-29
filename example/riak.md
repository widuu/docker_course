#Riak Service

>注：

>这个例子环境是假设你已经运行了docker进程，更多详细信息请查看运行例子，如果你不喜欢sudo，你可以用户授权命令和docker组

这个例子会展示如何安装一个预安装号的docker

##创建一个Dockerfile

创建一个空文件叫做Dockerfile

	touch Dockerfile
	
下一步，定义你想要创建你自己镜像的父级镜像，我们用ubuntu(tag:latest),你可以通过docker index中获取

	# Riak
	#
	# VERSION       0.1.0
	
	# Use the Ubuntu base image provided by dotCloud
	FROM ubuntu:latest
	MAINTAINER Hector Castro hector@basho.com
	
下一步，我们更新APT并且应用一些升级

	# Update the APT cache
	RUN sed -i.bak 's/main$/main universe/' /etc/apt/sources.list
	RUN apt-get update
	RUN apt-get upgrade -y
	
做完以后，我们安装一些依赖包需要一段时间

- curl 用来下载 Basho’s APT仓库key
- lsb-release 帮助我们获得ubuntu版本代号
- openssh-server 允许我们远程登录容器，加入riak节点组成集群
- supervisor 用来管理openssh和Riak进程

	# Install and setup project dependencies
	RUN apt-get install -y curl lsb-release supervisor openssh-server
	
	RUN mkdir -p /var/run/sshd
	RUN mkdir -p /var/log/supervisor
	
	RUN locale-gen en_US en_US.UTF-8
	
	ADD supervisord.conf /etc/supervisor/conf.d/supervisord.conf
	
	RUN echo 'root:basho' | chpasswd
	
现在我们需要添加 Basho’s APT 仓库:

	RUN curl -s http://apt.basho.com/gpg/basho.apt.key | apt-key add --
	RUN echo "deb http://apt.basho.com $(lsb_release -cs) main" > /etc/apt/sources.list.d/basho.list
	RUN apt-get update
	
做完之后，我们需要默认安装Riak需要一点时间

	# Install Riak and prepare it to run
	RUN apt-get install -y riak
	RUN sed -i.bak 's/127.0.0.1/0.0.0.0/' /etc/riak/app.config
	RUN echo "ulimit -n 4096" >> /etc/default/riak
	
到这里，下一步我们需要添加一个方法来让我们的nitctl缺失:

	# Hack for initctl
	# See: https://github.com/dotcloud/docker/issues/1024
	RUN dpkg-divert --local --rename --add /sbin/initctl
	RUN ln -s /bin/true /sbin/initctl
	
接下来，我们需要开放raik协议缓冲区/http/ssh接口

	# Expose Riak Protocol Buffers and HTTP interfaces, along with SSH
	EXPOSE 8087 8098 22
	
最后我们运行supervisord让riak和openssh开始：

	CMD ["/usr/bin/supervisord"]
	
##创建一个supervisord配置文件

创建一个空文件叫做supervisord.conf，使配置文件的目录路径和你的Dockerfile的文件相同

	touch supervisord.conf
	
填充下面的定义:

	[supervisord]
	nodaemon=true
	
	[program:sshd]
	command=/usr/sbin/sshd -D
	stdout_logfile=/var/log/supervisor/%(program_name)s.log
	stderr_logfile=/var/log/supervisor/%(program_name)s.log
	autorestart=true
	
	[program:riak]
	command=bash -c ". /etc/default/riak && /usr/sbin/riak console"
	pidfile=/var/log/riak/riak.pid
	stdout_logfile=/var/log/supervisor/%(program_name)s.log
	stderr_logfile=/var/log/supervisor/%(program_name)s.log

##创建一个Riak的docker 镜像

	docker build -t "<yourname>/riak" .
	
##下一步

Riak是分布式数据库，很多生产部署至少5个节点，你可以在docker-riak项目中获得如何通过docker和管道来部署riak集群


