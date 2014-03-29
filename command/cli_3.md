#Docker命令行帮助(3)

------

##events

	Usage :docker events
	
	从服务器获取实时事件
	
	-since="": 显示以前创建的事件流()
	
##Examples

	在这个例子里你需要运行两个shell
	
###Shell 1:监听事件
	
	sudo docker events
	
###Shell 2:开始和关闭一个容器
	
	$ sudo docker start 4386fb97867d
	$ sudo docker stop 4386fb97867d

###Shell 1: 再一次。。你会看到下面的事件

	[2013-09-03 15:49:26 +0200 CEST] 4386fb97867d: (from 12de384bfb10) start
	[2013-09-03 15:49:29 +0200 CEST] 4386fb97867d: (from 12de384bfb10) die
	[2013-09-03 15:49:29 +0200 CEST] 4386fb97867d: (from 12de384bfb10) stop
	
###查看事件从一个指定的过去的时间段

	$ sudo docker events -since 1378216169
	[2013-09-03 15:49:29 +0200 CEST] 4386fb97867d: (from 12de384bfb10) die
	[2013-09-03 15:49:29 +0200 CEST] 4386fb97867d: (from 12de384bfb10) stop
	
	$ sudo docker events -since '2013-09-03'
	[2013-09-03 15:49:26 +0200 CEST] 4386fb97867d: (from 12de384bfb10) start
	[2013-09-03 15:49:29 +0200 CEST] 4386fb97867d: (from 12de384bfb10) die
	[2013-09-03 15:49:29 +0200 CEST] 4386fb97867d: (from 12de384bfb10) stop
	
	$ sudo docker events -since '2013-09-03 15:49:29 +0200 CEST'
	[2013-09-03 15:49:29 +0200 CEST] 4386fb97867d: (from 12de384bfb10) die
	[2013-09-03 15:49:29 +0200 CEST] 4386fb97867d: (from 12de384bfb10) stop
	
![docker events](http://yun.widuu.com/docker/events.png)

##export 
	
		Usage: docker export CONTAINER(容器)
		
		导出文件系统作为一个tar文档发送到stdout
		
###例子

		$ sudo docker export red_panda > latest.tar
		
![docker export](http://yun.widuu.com/docker/export.png)

##history 

	Usage: docker history [OPTIONS] IMAGE

	显示镜像的历史操作
	
	  -notrunc=false: 不截断输出
	  -q=false: 只显示数字id
	  
###查看docker:last最新创建的镜像

	$ docker history docker
	ID                  CREATED             CREATED BY
	docker:latest       19 hours ago        /bin/sh -c #(nop) ADD . in /go/src/github.com/dotcloud/docker
	cf5f2467662d        2 weeks ago         /bin/sh -c #(nop) ENTRYPOINT ["hack/dind"]
	3538fbe372bf        2 weeks ago         /bin/sh -c #(nop) WORKDIR /go/src/github.com/dotcloud/docker
	7450f65072e5        2 weeks ago         /bin/sh -c #(nop) VOLUME /var/lib/docker
	b79d62b97328        2 weeks ago         /bin/sh -c apt-get install -y -q lxc
	36714852a550        2 weeks ago         /bin/sh -c apt-get install -y -q iptables
	8c4c706df1d6        2 weeks ago         /bin/sh -c /bin/echo -e '[default]\naccess_key=$AWS_ACCESS_KEY\nsecret_key=$AWS_SECRET_KEYn' > /.s3cfg
	b89989433c48        2 weeks ago         /bin/sh -c pip install python-magic
	a23e640d85b5        2 weeks ago         /bin/sh -c pip install s3cmd
	41f54fec7e79        2 weeks ago         /bin/sh -c apt-get install -y -q python-pip
	d9bc04add907        2 weeks ago         /bin/sh -c apt-get install -y -q reprepro dpkg-sig
	e74f4760fa70        2 weeks ago         /bin/sh -c gem install --no-rdoc --no-ri fpm
	1e43224726eb        2 weeks ago         /bin/sh -c apt-get install -y -q ruby1.9.3 rubygems libffi-dev
	460953ae9d7f        2 weeks ago         /bin/sh -c #(nop) ENV GOPATH=/go:/go/src/github.com/dotcloud/docker/vendor
	8b63eb1d666b        2 weeks ago         /bin/sh -c #(nop) ENV PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/goroot/bin
	3087f3bcedf2        2 weeks ago         /bin/sh -c #(nop) ENV GOROOT=/goroot
	635840d198e5        2 weeks ago         /bin/sh -c cd /goroot/src && ./make.bash
	439f4a0592ba        2 weeks ago         /bin/sh -c curl -s https://go.googlecode.com/files/go1.1.2.src.tar.gz | tar -v -C / -xz && mv /go /goroot
	13967ed36e93        2 weeks ago         /bin/sh -c #(nop) ENV CGO_ENABLED=0
	bf7424458437        2 weeks ago         /bin/sh -c apt-get install -y -q build-essential
	a89ec997c3bf        2 weeks ago         /bin/sh -c apt-get install -y -q mercurial
	b9f165c6e749        2 weeks ago         /bin/sh -c apt-get install -y -q git
	17a64374afa7        2 weeks ago         /bin/sh -c apt-get install -y -q curl
	d5e85dc5b1d8        2 weeks ago         /bin/sh -c apt-get update
	13e642467c11        2 weeks ago         /bin/sh -c echo 'deb http://archive.ubuntu.com/ubuntu precise main universe' > /etc/apt/sources.list
	ae6dde92a94e        2 weeks ago         /bin/sh -c #(nop) MAINTAINER Solomon Hykes <solomon@dotcloud.com>
	
###带图的都是我本地自己测试的

![docker history](http://yun.widuu.com/docker/history.png)

##images

	Usage: docker images [OPTIONS] [NAME]
	
	列出镜像
	
	  -a=false: 显示所有镜像(默认情况下过略掉了用于构建的镜像)
	  -notrunc=false: 不截断输出
	  -q=false: 指输出镜像id
	  -tree=false: 用树形结构输出镜像
	  -viz=false:  用graphviz输出镜像图(非官方备注，你需要安装apt-get install graphviz)
	
###列出最近创建的镜像

	$ sudo docker images | head
	REPOSITORY                    TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
	<none>                        <none>              77af4d6b9913        19 hours ago        1.089 GB
	committest                    latest              b6fa739cedf5        19 hours ago        1.089 GB
	<none>                        <none>              78a85c484f71        19 hours ago        1.089 GB
	docker                        latest              30557a29d5ab        20 hours ago        1.089 GB
	<none>                        <none>              0124422dd9f9        20 hours ago        1.089 GB
	<none>                        <none>              18ad6fad3402        22 hours ago        1.082 GB
	<none>                        <none>              f9f1e26352f0        23 hours ago        1.089 GB
	tryout                        latest              2629d1fa0b81        23 hours ago        131.5 MB
	<none>                        <none>              5ed6274db6ce        24 hours ago        1.089 GB

![docker images](http://yun.widuu.com/docker/images1.png)

###列出全部的镜像ID

	$ sudo docker images -notrunc | head
	REPOSITORY                    TAG                 IMAGE ID                                                           CREATED             VIRTUAL SIZE
	<none>                        <none>              77af4d6b9913e693e8d0b4b294fa62ade6054e6b2f1ffb617ac955dd63fb0182   19 hours ago        1.089 GB
	committest                    latest              b6fa739cedf5ea12a620a439402b6004d057da800f91c7524b5086a5e4749c9f   19 hours ago        1.089 GB
	<none>                        <none>              78a85c484f71509adeaace20e72e941f6bdd2b25b4c75da8693efd9f61a37921   19 hours ago        1.089 GB
	docker                        latest              30557a29d5abc51e5f1d5b472e79b7e296f595abcf19fe6b9199dbbc809c6ff4   20 hours ago        1.089 GB
	<none>                        <none>              0124422dd9f9cf7ef15c0617cda3931ee68346455441d66ab8bdc5b05e9fdce5   20 hours ago        1.089 GB
	<none>                        <none>              18ad6fad340262ac2a636efd98a6d1f0ea775ae3d45240d3418466495a19a81b   22 hours ago        1.082 GB
	<none>                        <none>              f9f1e26352f0a3ba6a0ff68167559f64f3e21ff7ada60366e2d44a04befd1d3a   23 hours ago        1.089 GB
	tryout                        latest              2629d1fa0b81b222fca63371ca16cbf6a0772d07759ff80e8d1369b926940074   23 hours ago        131.5 MB
	<none>                        <none>              5ed6274db6ceb2397844896966ea239290555e74ef307030ebb01ff91b1914df   24 hours ago        1.089 GB
	
![docker images -notrunc ](http://yun.widuu.com/docker/imagesfull.png)

###显示图像图片

	$ sudo docker images -viz | dot -Tpng -o docker.png

(这里不引用官方的了,我本地测试的)

![docker images -viz](http://yun.widuu.com/docker/docker-images.png)
		
###显示镜像的曾次结构

	$ sudo docker images -tree
	
	├─8dbd9e392a96 Size: 131.5 MB (virtual 131.5 MB) Tags: ubuntu:12.04,ubuntu:latest,ubuntu:precise
	└─27cf78414709 Size: 180.1 MB (virtual 180.1 MB)
	  └─b750fe79269d Size: 24.65 kB (virtual 180.1 MB) Tags: ubuntu:12.10,ubuntu:quantal
	    ├─f98de3b610d5 Size: 12.29 kB (virtual 180.1 MB)
	    │ └─7da80deb7dbf Size: 16.38 kB (virtual 180.1 MB)
	    │   └─65ed2fee0a34 Size: 20.66 kB (virtual 180.2 MB)
	    │     └─a2b9ea53dddc Size: 819.7 MB (virtual 999.8 MB)
	    │       └─a29b932eaba8 Size: 28.67 kB (virtual 999.9 MB)
	    │         └─e270a44f124d Size: 12.29 kB (virtual 999.9 MB) Tags: progrium/buildstep:latest
	    └─17e74ac162d8 Size: 53.93 kB (virtual 180.2 MB)
	      └─339a3f56b760 Size: 24.65 kB (virtual 180.2 MB)
	        └─904fcc40e34d Size: 96.7 MB (virtual 276.9 MB)
	          └─b1b0235328dd Size: 363.3 MB (virtual 640.2 MB)
	            └─7cb05d1acb3b Size: 20.48 kB (virtual 640.2 MB)
	              └─47bf6f34832d Size: 20.48 kB (virtual 640.2 MB)
	                └─f165104e82ed Size: 12.29 kB (virtual 640.2 MB)
	                  └─d9cf85a47b7e Size: 1.911 MB (virtual 642.2 MB)
	                    └─3ee562df86ca Size: 17.07 kB (virtual 642.2 MB)
	                      └─b05fc2d00e4a Size: 24.96 kB (virtual 642.2 MB)
	                        └─c96a99614930 Size: 12.29 kB (virtual 642.2 MB)
	                          └─a6a357a48c49 Size: 12.29 kB (virtual 642.2 MB) Tags: ndj/mongodb:latest
	                         
![docker images tree](http://yun.widuu.com/docker/images-tree.png)

##import

	Usage: docker import URL|- [REPOSITORY[:TAG]]
	
	创建一个空文件系统映像和引用压缩文件的内容
	(.tar, .tar.gz, .tgz, .bzip, .tar.xz, .txz) into it, 然后选择性的标记.
	
此时，这个URl必须指向一个文件存档(.tar, .tar.gz, .tgz, .bzip, .tar.xz, or .txz) 包含一个跟文件系统，如果你想引用一个本地的文件压缩文件，你可以使用从输入端使用参数

###Examples

####输入一个远程的地址

	sudo docker import http://example.com/exampleimage.tgz
	
**你可以引用我的url速度快，http://docker.widuu.com/ubuntu.tar**

####输入一个本地的文件

通过管道输入docker

 	cat exampleimage.tgz | sudo docker import - exampleimagelocal:new
	
![docker import](http://yun.widuu.com/docker/docker-import.png)

注意本例子中 sudo 你必须保留文件的所有权(特别是root所有权)在tar文档，如果当你的tar不是root(或者 sudo 命令)，那么所有权可能不会保留！

##info

	Usage: docker info
	
	显示整个文件系统

	$ sudo docker info
	Containers: 292
	Images: 194
	Debug mode (server): false
	Debug mode (client): false
	Fds: 22
	Goroutines: 67
	LXC Version: 0.9.0
	EventsListeners: 115
	Kernel Version: 3.8.0-33-generic
	WARNING: No swap limit support	
	
![docker info](http://yun.widuu.com/docker/info.png)	

本文由widuu贡献翻译，转载请注明来自[http://www.widuu.com](http://www.widuu.com)~