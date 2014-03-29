#创建一个基础镜像

你是否想要创建你自己的基础镜像？好极了！

具体过程就要依赖你想要的linux发行版包。我们有下面一下例子，也鼓励一些人提交新的贡献

##开始

一般情况下，你要开始运行分配你想要一个工作的机器打包为基础镜像，虽然Debian的debootstrap不是必须的一些功能。但是你可以使用它来构建Ubuntu镜像

下边是一个简单的例子，来创建一个Ubuntu基础镜像

	$ sudo debootstrap raring raring > /dev/null
	$ sudo tar -C raring -c . | sudo docker import - raring
	a29c15f1bf7a
	$ sudo docker run raring cat /etc/lsb-release
	DISTRIB_ID=Ubuntu
	DISTRIB_RELEASE=13.04
	DISTRIB_CODENAME=raring
	DISTRIB_DESCRIPTION="Ubuntu 13.04"
	
这里有一些脚本例子来创建基础镜像

- [BusyBox](https://github.com/dotcloud/docker/blob/master/contrib/mkimage-busybox.sh)

- [CentOS / Scientific Linux CERN (SLC)](https://github.com/dotcloud/docker/blob/master/contrib/mkimage-rinse.sh)

- [Debian / Ubuntu](https://github.com/dotcloud/docker/blob/master/contrib/mkimage-debootstrap.sh)

