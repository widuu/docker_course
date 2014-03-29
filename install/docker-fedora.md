#Fedora安装

##官方警告

1. 官方申明docker还是在开发完善中，不建议在运营的产品中使用它，但是现在离正式版越来越接近了，请关注我们的博客http://blog.docker.io/2013/08/getting-to-docker-1-0/

1. windows的安装是社区贡献出来的，唯一的官方的安装方法是ubuntu就是上一节我们介绍的，这个版本可能是过期的因为它取决于一些二进制包的更新和发布

docker允许在Fedora 19以后版本中运行，请注意由于docker的限制，docker只能运行在64位主机上

##安装

在Fedora中已经提供docker-io包了 

如果你已经安装了(不相关)的docker包，它会跟docker-io有冲突，有一个错误报告，如果想继续安装docker-io，请先删除docker.

	sudo yum -y remove docker

在Fedora 20版本之后，wmdocker包与docker-io提供相同的功能，但是也不会冲突 

	sudo yum -y install wmdocker
	sudo yum -y remove docker

开始安装docker-io包在我们的主机上

	sudo yum -y install docker-io

升级docker-io包

	sudo yum -y update docker-io

现在已经安装好了，让我们启动docker进程

	sudo systemctl start docker

如果我们想开机启动，我们需要 

	sudo systemctl enable docker

现在测试一下是否正常工作了

	sudo docker run -i -t fedora /bin/bash

好了，现在你可以继续做hello word的例子
