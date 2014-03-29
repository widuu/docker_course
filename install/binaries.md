#Binaries

- 官方申明docker还是在开发完善中，不建议在运营的产品中使用它，但是现在离正式版越来越接近了，请关注我们的博客http://blog.docker.io/2013/08/getting-to-docker-1-0/

通过配置docker环境来执行docker的指令集。

在开始下边之前，你应该检查docker版本是否提供你的发行版支持，我们已经提供了许多发行版

##检查你的内核

你的linux主机内核是docker 内核支持的

##查看你的空间工具

你必须安装了lxc。

##获取docker二进制文件 

	wget https://get.docker.io/builds/Linux/x86_64/docker-latest -O docker
	chmod +x docker

##后台运行docker

	# start the docker in daemon mode from the directory you unpacked
	sudo ./docker -d &

##启动你的第一个容器

	# check your docker version
	sudo ./docker version
	
	# run a container and open an interactive shell in the container
	sudo ./docker run -i -t ubuntu /bin/bash

继续你可以运行hello word实例了。
