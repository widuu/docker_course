#DOCKER教程

####注意事项

>1.官方申明docker还是在开发完善中，不建议在运营的产品中使用它，但是现在离正式版越来越接近了，请关注我们的博客http://blog.docker.io/2013/08/getting-to-docker-1-0/

>2.系统注意事项-由于现在的docker的局限性，现在只能使用在64位的服务器上边

####安装教程

#####ubntu安装教程（12.04）
>由于linux容器的bug，docker在linux的kernel3.8上运行最佳，由于12.04的内核是3.2kernel,所以我们必须，如果你不确定你的内核或者你只是装在virbox上使用，你可以跳过这些步骤
	
	#安装内核
	sudo apt-get update
	sudo apt-get install linux-image-generic-lts-raring linux-headers-generic-lts-raring

    #重启
	sudo reboot

>安装警告-由于Docker指令在0.6的版本有所改变，如果你从一个早期版本升级，你需要重新安装他们
	
- Docker作为一个有效的debian的安装包，当你安装的时候你可能看到‘ See the :ref:`installmirrors` section below if you are not in the United States’，你可以选择其他的镜像源debain包来进行快速的安装。
 
第一次添加Docker的repository到你的本地秘钥库


	sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 36A1D7869245C8950F966E92D8576A8BA88D21E9


将Docker添加到你的apt软件安装源中，然后update和install lxc-docker，期间会遇到一个警告，说这个包不可靠，你只需要回复yes然后继续安装就可以了！


	sudo sh -c "echo deb http://get.docker.io/ubuntu docker main\
	>/etc/apt/sources.list.d/docker.list"
	sudo apt-get update
	sudo apt-get install lxc-docker

>提示，官方也提供了一个简单脚本帮助你安装，你可以用curl来获取这个脚本然后执行安装

	curl -s https://get.docker.io/ubuntu/ | sudo sh

>现在你就可以下载安装ubuntu的镜像封装到一个沙箱中了

	sudo docker run -i -t ubuntu /bin/bash


#####ubntu安装教程（13.04）

>首先要确认是否安装了AUFS,如果没有安装运行下边的命令

	sudo apt-get update
	sudo apt-get install linux-image-extra-`uname -r`

然后下边安装教程同12.04看上边就可以了！

#####安装好后测试hello word

>可以用命令行，让它在后台执行（原文英语太长简缩就是这样）

	sudo docker -d &

>现在你可以运行docker的客户端了，但是如果你运行命令的话，一定要用root权限，或者用sudo执行，因为docker是被当做一个软件使用，而且是受保护的，你的命令会被转发到docker的进程中来运行，就像你vim一个受保护的文档，所以需要root权限，原文太简单怕各位不理解(by default all commands will be forwarded to the docker daemon via a protected Unix socket, so you must run as the root or via the sudo command.)

>hello word最基本的例子就是可以共用Docker，先要现在最基本的ubunt的镜像（就好像你写程序的基类）

	sudo docker pull ubuntu

>获得ubuntu的镜像后，你可以选择你的镜像盒子，一个最小化的linux系统，这个镜像是你从Docker的仓库中检索出来的

	sudo docker run ubuntu /bin/echo hello world

上边的是运用docker运行echo命令输出hello word

-	“sudo”       运用root权限运行
-	“docker run” 在一个新的容器中运行命令
-	“ubuntu”     是我们想要运行命令的镜像
-	“/bin/echo”  我们在容器用运行的shell命令行
-	“hello word” 我们要输出的


#####Docker 和 UFW

>Dockers是用桥接的方式管理容器的网络，默认情况下，如果你安装了UFW防火墙，他会过滤掉所有的转发，所以你需要允许UFW转发

	sudo nano /etc/default/ufw
	----
	# Change:
	# DEFAULT_FORWARD_POLICY="DROP"
	# to
	DEFAULT_FORWARD_POLICY="ACCEPT"

>然后刷新UFW

	sudo ufw reload

>当然你也可以只放行Docker容器允许的端口4243

	sudo ufw allow 4243/tcp

#####Mirrors

你应该ping get.docker.io ,然后看下延迟，选择最快的一个

#####Yandex

这个的意思是普京他们家那边有个镜像包搜索的引擎，每6个小时更新一次，你可以用 http://mirror.yandex.ru/mirrors/docker/代替 http://get.docker.io/ubuntu

举个简单的例子

	sudo sh -c "echo deb http://mirror.yandex.ru/mirrors/docker/ docker main\
	> /etc/apt/sources.list.d/docker.list"
	sudo apt-get update
	sudo apt-get install lxc-docker

####Red Hat Enterprise Linux安装教程

>注意事项是red hat是社区贡献的所以这个不需要我多说了，人家建议用ubuntu

####安装步骤

	#安装包
	sudo yum -y install docker-io

	#升级安装包
	sudo yum -y update docker-io

	#启动docker
	sudo service docker start

	#开机启动，加入3,5就可以了
	sudo chkconfig docker on

	#然后运行吧--比较坑的就是fedora
	sudo docker run -i -t fedora /bin/bash


本文由widuu贡献翻译，转载请注明来自[http://www.widuu.com](http://www.widuu.com)~








 
   
 

