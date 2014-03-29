#Process Management with CFEngine

创建Docker容器来管理进程

docker监控容器的进程从开始到结束的整个过程，通过在Docker容器内引入CFEngine，我们可以解决以下几个问题：

- 可以轻松的在一个容器内启动多个进程，并且所有进程将呗自动管理，与标准的docker run 命令。

- 如果管理进程终止或者崩溃，CFEngine会在一分钟内重新启动它。

- 容器本身的生存是入住在CFEgine的后台进程中生存的，CFEgine中，我们能够分离正常运行的服务的容器

##如何工作

CFEngine，和cfe-docker融合使用，将会作为Dockerfile中安装的一部分。来用作创建我们的Docker的CFEngine镜像。

Dockerfile的ENTRYPOINT需要任意数量的命令（与任何需要的参数）作为参数，当我们运行docker容器，这些会被写如到CFEngine策略中并且由CFEngine接管，以确保容器运行在一个理想的过程中

如果没有找到basename ，CFEngine会扫描进程表通过ENTRYPOINT给的基本命令来生成basename ，并在进程启动时运行命令。举例，如果我们启动容器使用 docker run "/path/to/my/application parameters"，CFEngine将会查找一个进程命名的应用并运行命令，如果在进程表的任何时间内没有找到这个进程，CFEngine将会再次执行/path/to/my/application parameters来启动应用，它会检查进程表在每一分钟内发生的变化。

请注意，因此重要的是启动你的应用程序来离开相对文件的命令，如果你需要你可以做一些小CFEngine策略调整，使其更加灵活 

##使用
这个例子假设你已经安装好了docker并且正常运行，我们将会在一个容器中安装和管理apache2和sshd。

这里有三个步骤：

- 在容器内安装CFEngine。

- 复制CFEngine进程管理策略到已经安装的CFEngine容器内。

- 启动你的应用进程作为docker运行的一部分

##创建一个镜像容器

前两个步骤可以作为Dockerfile的一部分,如下 

	FROM ubuntu
	MAINTAINER Eystein Måløy Stenberg <eytein.stenberg@gmail.com>
	
	RUN apt-get -y install wget lsb-release unzip
	
	# install latest CFEngine
	RUN wget -qO- http://cfengine.com/pub/gpg.key | apt-key add -
	RUN echo "deb http://cfengine.com/pub/apt $(lsb_release -cs) main" > /etc/apt/sources.list.d/cfengine-community.list
	RUN apt-get update
	RUN apt-get install cfengine-community
	
	# install cfe-docker process management policy
	RUN wget --no-check-certificate https://github.com/estenberg/cfe-docker/archive/master.zip -P /tmp/ && unzip /tmp/master.zip -d /tmp/
	RUN cp /tmp/cfe-docker-master/cfengine/bin/* /var/cfengine/bin/
	RUN cp /tmp/cfe-docker-master/cfengine/inputs/* /var/cfengine/inputs/
	RUN rm -rf /tmp/cfe-docker-master /tmp/master.zip
	
	# apache2 and openssh are just for testing purposes, install your own apps here
	RUN apt-get -y install openssh-server apache2
	RUN mkdir -p /var/run/sshd
	RUN echo "root:password" | chpasswd  # need a password for ssh
	
	ENTRYPOINT ["/var/cfengine/bin/docker_processes_run.sh"]

通过在你的工作目录保存Dockerfile,你可以通过docker build命令来创建你的容器,docker build -t managed_image.

##测试容器

启动容器apache2和sshd运行和管理,转发端口SSH实例:

	docker run -p 127.0.0.1:222:22 -d managed_image "/usr/sbin/sshd" "/etc/init.d/apache2 start"

现在我们可以看到cfe-docker集成的好处，它允许我们使用docker run命令启动多个进程。

我们现在可以登录我们新创建容器并且发现apache2和sshd正在运行，我们需要在上边的Dockerfile文件设置root密码来登录ssh:

	ssh -p222 root@127.0.0.1
	
	ps -ef
	UID        PID  PPID  C STIME TTY          TIME CMD
	root         1     0  0 07:48 ?        00:00:00 /bin/bash /var/cfengine/bin/docker_processes_run.sh /usr/sbin/sshd /etc/init.d/apache2 start
	root        18     1  0 07:48 ?        00:00:00 /var/cfengine/bin/cf-execd -F
	root        20     1  0 07:48 ?        00:00:00 /usr/sbin/sshd
	root        32     1  0 07:48 ?        00:00:00 /usr/sbin/apache2 -k start
	www-data    34    32  0 07:48 ?        00:00:00 /usr/sbin/apache2 -k start
	www-data    35    32  0 07:48 ?        00:00:00 /usr/sbin/apache2 -k start
	www-data    36    32  0 07:48 ?        00:00:00 /usr/sbin/apache2 -k start
	root        93    20  0 07:48 ?        00:00:00 sshd: root@pts/0
	root       105    93  0 07:48 pts/0    00:00:00 -bash
	root       112   105  0 07:49 pts/0    00:00:00 ps -ef

如果我们停止apache2，CFEngine会在一分钟内重启它。

	service apache2 status
	 Apache2 is running (pid 32).
	service apache2 stop
	         * Stopping web server apache2 ... waiting    [ OK ]
	service apache2 status
	 Apache2 is NOT running.
	# ... wait up to 1 minute...
	service apache2 status
	 Apache2 is running (pid 173).

##选配你的应用程序

为了确保你的应用程序以相同的方式得到管理，目前你可以在上边的例子中做两处调整：

- 在上边的Dockerfile安装你的应用来代替apache2和sshd。

- 当你docker run启动容器, 则在你的应用程序内指定命令参数，而不是apache2和sshd