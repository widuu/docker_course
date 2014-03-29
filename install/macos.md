#Mac OS X安装教程

>注意事项

>这些指令使用的版本是docker version 0.8版本，当然随时可能发生变化。

>注意事项

>docker一直在稳健的发展，不建议在生产环境中使用，但是我们已经离那个版本越来越近了。请参阅我们的博客，“Getting to Docker 1.0”

docker支持 Mac OS X 10.6及以上版本

##如何在Mac OS X上安装Docker

##VirtualBox

在OS X运行docker需要安装virtualbox,首先，先从Virtualbox页面获取安装包(OS X 主机版本 x86/amd64)。

下载完成后，打开磁盘镜像，设置和运行文件（VirtualBox.pkg）来安装virtualbox,不要简单的复制没有运行的安装包。

###boot2docker

[boot2docker](https://github.com/steeve/boot2docker)提供了一个简单的脚本来管理正在运行docker进程的虚拟主机。它还负责为操作系统镜像的安装工作。

如果你还没安装boot2docker请打开一个新的终端窗口

运行下边的命令来获得boot2docker

	# 进去安装目录
	cd ~/bin
	
	# 获取安装文件
	curl https://raw.github.com/steeve/boot2docker/master/boot2docker > boot2docker
	
	# 设置可执行
	chmod +x boot2docker

##Docker OS X Client

docker进程使用docker客户端访问。

运行下边的命令，来获取docker并且设置它

	# 获取文件
	curl -o docker http://get.docker.io/builds/Darwin/x86_64/docker-latest
	
	# 设置可执行
	chmod +x docker
	
	# 设置docker进程的环境变量
	export DOCKER_HOST=tcp://
	
	# 复制可执行文件
	sudo cp docker /usr/local/bin/

然后让我们看看如何使用它。

##如何在Mac OS X上使用Docker

###docker进程(通过boot2docker)

进行~/bin目录，运行下边的命令：

	# 初始化虚拟主机
	./boot2docker init
	
	# 运行虚拟主机 (the docker daemon)
	./boot2docker up
	
	# 看所有可用的命令:
	./boot2docker
	
	# Usage ./boot2docker {init|start|up|pause|stop|restart|status|info|delete|ssh|download}



##docker客户端

一旦虚拟主机运行docker进程，你可以像使用其它的一些应用一样来使用docker客户端。


	docker version
	# Client version: 0.7.6
	# Go version (client): go1.2
	# Git commit (client): bc3b2ec
	# Server version: 0.7.5
	# Git commit (server): c348c04
	# Go version (server): go1.2


##用SSH来连接虚拟主机

如果你感觉需要连接虚拟主机，你可以简单的运行下边的命令：


	./boot2docker ssh
	
	# User: docker
	# Pwd:  tcuser

现在你可以使用hello World的例子了~

##学习更多

###boot2docker:

查看[boot2docker](https://github.com/steeve/boot2docker) github页面

如果SSH提示需要秘钥

	ssh-keygen -R '[localhost]:2022'




