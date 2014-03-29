#Docker简介

docker-作为一个容器运行，运行一个unix进程与服务器隔离提供里强大的安全保障，你的软件可以重复地运行在任何地方，因为它的容器包含了所有的环境依赖关系！

docker有三种方式运行

- 作为一个守护进程来管理你的linux主机上的LXC容器（sudo docker -d）

- 作为一个命令行的方式来告诉docker进程的程序接口来做什么（docker run ...）

- 作为一个仓库的客户端让你分享你搭建的环境(docker pull,docker commit)

这里记录着如何使用docker,docker的特性正在积极开发中，所以这个文档会经常变化。

docker的介绍，请看详细介绍，当你准备开始使用docker，我们有一个[快速开始教程](http://www.docker.io/gettingstarted/)和深入的[Ubuntu指南](http://docs.docker.io/en/latest/installation/ubuntulinux/#ubuntu-linux)和其它的[安装方式](http://docs.docker.io/en/latest/installation/#installation-list)包括二进制文件安装、Vagrant创建VMS、Rackspace和Amazon的实例！

完成阅读后，来[试一试](http://docs.docker.io/en/latest/examples/hello_world/#running-examples)