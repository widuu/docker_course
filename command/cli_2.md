#Docker命令行帮助(2)

----------

##build

	Usage: docker build [OPTIONS] PATH | URL | -
	通过指定文件代码来创建一个新的容器

	  -t="":     库的名称和可选标记容器创建成功时候产生例如(ubuntu:widuu)
	      
	  -q=false:  抑制生成过程中的输出
	  
	  -no-cache: 构建镜像时不适用缓存

	  -rm:		 成功构建后去除中间容器

这个文件路径或者url被称为构建的环境，在生成过程中可以参考这些文件的环境，举个例子，使用ADD命令！当一个Dockerfile被给定位URL，则没有环境被设定，当一个git仓库被设置为URL时，这个仓库就被做为环境！

>参见
>Build Images (Dockerfile Reference)[###]

##Examples:

>非官方备注，前提是你的目录下有Dockerfile文件，我提供一下我写的测试文件

	# VERSION 0.0.1
	# 默认ubuntu server长期支持版本，当前是12.10
	FROM ubuntu
	# 签名啦
	MAINTAINER widuu "admin@widuu.com"
	
	# 更新源，安装ssh server
	RUN echo "deb http://archive.ubuntu.com/ubuntu precise main universe"> /etc/apt/sources.list
	RUN apt-get update
	RUN apt-get install -y openssh-server
	RUN mkdir -p /var/run/sshd
	
	# 设置root ssh远程登录密码为123456
	RUN echo "root:dgj99349" | chpasswd 
	
	# 容器需要开放SSH 22端口
	EXPOSE 22
	
	
	# SSH终端服务器作为后台运行
	ENTRYPOINT /usr/sbin/sshd -D

>开始官方列子

	sudo docker build .
	
![docker build](http://yun.widuu.com/docker/build.png)

这个例子指定的Dockerfile文件路径在.所以本地目录中所有文件信息被推送到docker进程中去，docker进程会找到指定路径中的文件中的环境信息来构建，请记住后台程序会执行在远程机器上，不需要解析Dockerfile在客户端发生什么(你在哪里运行docker build).这就意味着文件中的所有信息都会被发送，不仅仅是前边Dockerfile列出的ADD

环境从本地机器中传输到Docker进程中，这就是你从客户端看到“Uploading context”的信息。
	
	 sudo docker build -t vieux/apache:2.0 .

这个构建像以前的例子，但是他给镜像添加了tag,这个仓库的名字将会是vieux/apache，tag名称是2.0

	 sudo docker build - < Dockerfile

这个从输入段读取Dockerfile没有环境，由于缺少环境，本地的所有环境不会发送到docker进程中去，由于没有本地的环境，Dockerfile 中的ADD只能从远程URl中获取

	 sudo docker build github.com/creack/docker-firefox

这个将会从你的github的仓库中获取，并且使用获取的仓库作为环境，Dockerfile在存储在跟作用Dockerfile，请注意，您可以指定一个任意的git存储库使用 git:// 模式

##commit

	Usage: docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]

	通过容器的改变来创建一个新的镜像
	
	  -m="": 提交信息
	  
	  -author="": 提交作者信息 (eg. "John Hannibal Smith <hannibal@a-team.com>"
	  
	  -run="": 配置应用镜像是执行 `docker run`.
	           (ex: -run='{"Cmd": ["cat", "/world"], "PortSpecs": ["22"]}')
	           
##提交一个中断的容器

	$ sudo docker ps
	ID                  IMAGE               COMMAND             CREATED             STATUS              PORTS
	c3f279d17e0a        ubuntu:12.04        /bin/bash           7 days ago          Up 25 hours
	197387f1b436        ubuntu:12.04        /bin/bash           7 days ago          Up 25 hours
	$ docker commit c3f279d17e0a  SvenDowideit/testimage:version3
	f5283438590d
	$ docker images | head
	REPOSITORY                        TAG                 ID                  CREATED             VIRTUAL SIZE
	SvenDowideit/testimage            version3            f5283438590d        16 seconds ago      335.7 MB
	
##改变容器运行的命令

有时你运行一个应用容器服务，你需要迅速改变它，然后变回来

做为一个雷子，我们使用ls运行一个容器，然后我们改变镜像运行ls /etc

	$ docker run -t -name test ubuntu ls
	bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  selinux  srv  sys  tmp  usr  var
	$ docker commit -run='{"Cmd": ["ls","/etc"]}' test test2
	933d16de9e70005304c1717b5c6f2f39d6fd50752834c6f34a155c70790011eb
	$ docker run -t test2
	adduser.conf            gshadow          login.defs           rc0.d
	alternatives            gshadow-         logrotate.d          rc1.d
	apt                     host.conf        lsb-base             rc2.d
	...

![run example](http://yun.widuu.com/docker/run.png)

##完整的 -run 例子

-run json hash 改变配置部分当运行的docker检查容器或者配置当容器运行检索的ImageId

	$ sudo docker commit -run='
	{
	    "Entrypoint" : null,
	    "Privileged" : false,
	    "User" : "",
	    "VolumesFrom" : "",
	    "Cmd" : ["cat", "-e", "/etc/resolv.conf"],
	    "Dns" : ["8.8.8.8", "8.8.4.4"],
	    "MemorySwap" : 0,
	    "AttachStdin" : false,
	    "AttachStderr" : false,
	    "CpuShares" : 0,
	    "OpenStdin" : false,
	    "Volumes" : null,
	    "Hostname" : "122612f45831",
	    "PortSpecs" : ["22", "80", "443"],
	    "Image" : "b750fe79269d2ec9a3c593ef05b4332b1d1a02a62b4accb2c21d589ff2f5f2dc",
	    "Tty" : false,
	    "Env" : [
	       "HOME=/",
	       "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
	    ],
	    "StdinOnce" : false,
	    "Domainname" : "",
	    "WorkingDir" : "/",
	    "NetworkDisabled" : false,
	    "Memory" : 0,
	    "AttachStdout" : false
	}' $CONTAINER_ID
	
##CP

	Usage :docker cp CONTAINER:PATH HOSTPATH
	
	将容器的文件和文件夹复制到主机中
	
	path.  路径相对于文件系统的跟
	
	$ sudo docker cp 7bb0e258aefe:/etc/debian_version .
	$ sudo docker cp blue_frog:/etc/hosts .
	
##diff

	Usage:docker diff CONTAINER
	
	更改的文件和目录列表容器的文件系统
		
有3中列出不同的diff事件

1. `A` - Add
1. `D` - Delete
1. `C` - Change

例子

	$ sudo docker diff 7bb0e258aefe
	
	C /dev
	A /dev/kmsg
	C /etc
	A /etc/mtab
	A /go
	A /go/src
	A /go/src/github.com
	A /go/src/github.com/dotcloud
	A /go/src/github.com/dotcloud/docker
	A /go/src/github.com/dotcloud/docker/.git
	....
	
![docker diff](http://yun.widuu.com/docker/diff.png)		
	
本文由widuu贡献翻译，转载请注明来自[http://www.widuu.com](http://www.widuu.com)~
