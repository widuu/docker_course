#Docker命令行帮助(4)


##insert

	Usage: docker insert IMAGE URL PATH
	
	通过url给镜像路径增加文件

使用指定的镜像作为父镜像增加一层新的文件，insert命令不修改原始奖项，而新的镜像的内容包含父镜像和新文件

###Examples

增加github文件

	$ sudo docker insert 8283e18b24bc https://raw.github.com/metalivedev/django/master/postinstall /tmp/postinstall.sh
	06fd35556d7b

##inspect

	Usage: docker inspect CONTAINER|IMAGE [CONTAINER|IMAGE...]
	
	返回容器底层信息
	
	  -format="": 格式化输出的格式

默认情况下，这将使所有结果放在一个JSON数组中，如果指定格式，就安装指定的格式来执行输出每个结果

Go的text/template包描述格式化的细节

![docker inspect](http://yun.widuu.com/docker/inspect.png)

###Examples

获得一个容器ip地址

	 sudo docker inspect -format='{{.NetworkSettings.IPAddress}}' $INSTANCE_ID

列出所有绑定的端口地址

	sudo docker inspect -format='{{range $p, $conf := .NetworkSettings.Ports}} {{$p}} -> {{(index $conf 0).HostPort}} {{end}}' $INSTANCE_ID

查找一个特定端口映射

当放字段名是一个数字时.Field语法不工作，网络设置端口部分包含一个内部端口影响到外部地址和端口的对象的列表！如果你要找出数字公共端口，你使用索引来查找特定端口映射，索引0包含的第一个对象！我们要求主机端口字段是获得公共地址！

	$ sudo docker inspect -format='{{(index (index .NetworkSettings.Ports "8787/tcp") 0).HostPort}}' $INSTANCE_ID

##kill

	Usage: docker kill CONTAINER [CONTAINER...]

	杀死一个正在运行的容器（发送终止信号） 

>已知问题（kill） issue 197标明`docker kill`将很难删除目录和删除容器

##load

	Usage: docker load < ubuntu.tar
	
	从输入流中加载一个压缩包，恢复包含的所有镜像和tag 

##login

	Usage: docker login [OPTIONS] [SERVER]
	
	登陆你在docker服务器注册的账号 index.docker.io
	
	-e="": email
	-p="": password
	-u="": username
	
	如果你想登陆你的私有的仓库你可以添加的你服务器名称
	
	example:
	docker login localhost:8080

##logs

	Usage: docker logs [OPTIONS] CONTAINER
	
	取出容器的log日志

![docker logs](http://yun.widuu.com/docker/logs.png)

`docker logs`命令提供一个便利的任何执行时间的记录，当你使用`docker run`的时候并不能保证执行顺序（当你`docker logs`你就可能没有产生一些执行记录）

##port

	Usage: docker port [OPTIONS] CONTAINER PRIVATE_PORT

	查找私有地址转换到共有地址端口

![docker port](http://yun.widuu.com/docker/port.png)

##ps

	Usage: docker ps [OPTIONS]
	
	List containers
	
	  -a=false: 查看所有的镜像 默认情况下只查看正在运行的镜像
	  -notrunc=false: 不把输出截断
	  -q=false: 仅仅只显示镜像ID

运行docker ps查看2个容器

	$ docker ps
	CONTAINER ID        IMAGE                        COMMAND                CREATED              STATUS              PORTS               NAMES
	4c01db0b339c        ubuntu:12.04                 bash                   17 seconds ago       Up 16 seconds                           webapp
	d7886598dbe2        crosbymichael/redis:latest   /redis-server --dir    33 minutes ago       Up 33 minutes       6379/tcp            redis,webapp/db

![docker ps](http://yun.widuu.com/docker/ps.png)

##pull

	Usage: docker pull NAME
	
	从远程仓库中获取镜像

##push

	Usage: docker push NAME
	
	推送本地镜像到远程仓库

##restart

	Usage: docker restart [OPTIONS] NAME

	重启正在运行的远程仓库

##rm

	Usage: docker rm [OPTIONS] CONTAINER

	删除一个或者所有的容器

    -link="": Remove the link instead of the actual container

>已知问题（kill） issue 197标明`docker kill`将很难删除目录和删除容器

#Examples

	$ sudo docker rm /redis
	/redis

这将删除容器下引用的链接/reids

	$ sudo docker rm -link /webapp/redis
	/webapp/redis

这将删除/webapp /redis底层之间的联系，容器中会删除所有的网络通信

	$ sudo docker rm `docker ps -a -q`

这个命令会删除所有停止运行的容器，`docker ps -a -q`会返回所有的镜像ID通过这些镜像ID再执行rm命令删除它们，当然正在运行的是不会被删除的

##rmi

	Usage: docker rmi IMAGE [IMAGE...]

	删除一个或者多个镜像

##删除标记的镜像
	
	镜像可以通过它们的长或者短ID删除或者他们的镜像名称，如果一个镜像有一个或者多个名称，在删除镜像之前，他们每一个都必须要删除

	$ sudo docker images
	REPOSITORY                TAG                 IMAGE ID            CREATED             SIZE
	test1                     latest              fd484f19954f        23 seconds ago      7 B (virtual 4.964 MB)
	test                      latest              fd484f19954f        23 seconds ago      7 B (virtual 4.964 MB)
	test2                     latest              fd484f19954f        23 seconds ago      7 B (virtual 4.964 MB)
	
	$ sudo docker rmi fd484f19954f
	Error: Conflict, cannot delete image fd484f19954f because it is tagged in multiple repositories
	2013/12/11 05:47:16 Error: failed to remove one or more images
	
	$ sudo docker rmi test1
	Untagged: fd484f19954f4920da7ff372b5067f5b7ddb2fd3830cecd17b96ea9e286ba5b8
	$ sudo docker rmi test2
	Untagged: fd484f19954f4920da7ff372b5067f5b7ddb2fd3830cecd17b96ea9e286ba5b8
	
	$ sudo docker images
	REPOSITORY                TAG                 IMAGE ID            CREATED             SIZE
	test1                     latest              fd484f19954f        23 seconds ago      7 B (virtual 4.964 MB)
	$ sudo docker rmi test
	Untagged: fd484f19954f4920da7ff372b5067f5b7ddb2fd3830cecd17b96ea9e286ba5b8
	Deleted: fd484f19954f4920da7ff372b5067f5b7ddb2fd3830cecd17b96ea9e286ba5b8

##run

	Usage: docker run [OPTIONS] IMAGE[:TAG] [COMMAND] [ARG...]
	
	Run a command in a new container
	
	  -a=map[]: 附加标准输入、输出或者错误输出
	  -c=0: 共享CPU格式（相对重要）
	  -cidfile="": 将容器的ID标识写入文件
	  -d=false: 分离模式，在后台运行容器，并且打印出容器ID
	  -e=[]: 设置环境变量
	  -h="": 容器的主机名称
	  -i=false: 保持输入流开放即使没有附加输入流
	  -privileged=false: 给容器扩展的权限
	  -m="": 内存限制 (格式: <number><optional unit>, unit单位 = b, k, m or g)
	  -n=true: 允许镜像使用网络
	  -p=[]: 匹配镜像内的网络端口号
	  -rm=false:当容器退出时自动删除容器 (不能跟 -d一起使用)
	  -t=false: 分配一个伪造的终端输入
	  -u="": 用户名或者ID
	  -dns=[]: 自定义容器的DNS服务器
	  -v=[]: 创建一个挂载绑定：[host-dir]:[container-dir]:[rw|ro]. 如果容器目录丢失，docker会创建一个新的卷
	  -volumes-from="": 挂载容器所有的卷
	  -entrypoint="": 覆盖镜像设置默认的入口点
	  -w="": 工作目录内的容器
	  -lxc-conf=[]: 添加自定义 -lxc-conf="lxc.cgroup.cpuset.cpus = 0,1"
	  -sig-proxy=true: 代理接收所有进程信号 (even in non-tty mode)
	  -expose=[]: 让你主机没有开放的端口
	  -link="": 连接到另一个容器 (name:alias)
	  -name="": 分配容器的名称，如果没有指定就会随机生成一个
	  -P=false: Publish all exposed ports to the host interfaces 公布所有显示的端口主机接口

docker会先创建一个新的可写的容器层来指定镜像，通过指定命令来启动他，`docker run `相当于运行API/containers/create在/container/(id)/start之前

`docker run`命令可以用结合`docker commit`来改变正在运行的容器！

###知道问题(run -volumes-from)

Issue 2702: “lxc-start: Permission denied - failed to mount” 这是AppArmor一个权限的问题，请参见问题解决方案

	$ sudo docker run -cidfile /tmp/docker_test.cid ubuntu echo "test"

这个命令将会创建一个容器，并且在终端打印'test',cidfile参数使docker试图穿件一个文件并且写入容器的id,如果这个文件存在，docker就会返回一个错误，如果`docker run`exists,docker就会关闭这个文件！

	$ sudo docker run -t -i -rm ubuntu bash
	root@bc338942ef20:/# mount -t tmpfs none /mnt
	mount: permission denied

这条命令没有执行，因为在默认情况下，大多数有潜在危险内核命令都被舍弃，包括cap_sys_admin(需要挂载文件系统)，然而 
-privileged参数会让其执行

	$ sudo docker run -privileged ubuntu bash
	root@50e3f57e16e6:/# mount -t tmpfs none /mnt
	root@50e3f57e16e6:/# df -h
	Filesystem      Size  Used Avail Use% Mounted on
	none            1.9G     0  1.9G   0% /mnt

 -privileged参数给容器所有功能，同时它也强迫升级了所有设备的执行权限，换句话说，这个容器已经有了所有主机能做的事情，这个参数的存在允许特殊的用例，像docker内运行docker

	$ sudo docker  run -w /path/to/dir/ -i -t  ubuntu pwd

 -w 让命令在在目录/path/to/dir内运行,如果路径不存在创建，在容器外运行

![docker run -w](http://yun.widuu.com/docker/run-w1.png)

	$ sudo docker  run  -v `pwd`:`pwd` -w `pwd` -i -t  ubuntu pwd

![docker run -v -w](http://yun.widuu.com/docker/run-w.png)
	
-v参数安装当前工作目录到容器中，-w参数让命令在当前目录执行，通过pwd改变目录的返回值，这种组合命令使容器在当前目录下中心命令！

	$ sudo docker run -p 127.0.0.1:80:8080 ubuntu bash
	
容器的8080端口绑定在主机127.0.0.1的80端口。端口重定向解释了docker的端口是如何工作的

	$ sudo docker run -expose 80 ubuntu bash

让主机系统开放80端口供容器连接

	$ sudo docker run -name console -t -i ubuntu bash

这会创建一个新的容器并且运行bash，容器的名字是console

![docker run -name](http://yun.widuu.com/docker/name.png)

	$ sudo docker run -link /redis:redis -name console ubuntu bash

-link表示会将连接一个名为/redis的容器到新建的一个容器别名叫做redis,新的容器可以通过redis容器的环境变量访问网络和环境， -name参数会将名字console分配给新建的容器！

![docker run -link](http://yun.widuu.com/docker/link.png)

	$ sudo docker run -volumes-from 777f7dc92da7,ba8c0c54f0f2:ro -i -t ubuntu pwd

 -volumes-from 参数将从引用容器中挂载所有定义的卷. 容器引用列表可以用逗号分隔或者重复的引用 -volumes-from参数. 容器ID可以用:ro或者:rw来作为后缀挂载卷来标识只读或者读写权限，默认情况下，挂载的权限(读写权限)和引用容器的权限相同.

###一个完整的例子

	$ sudo docker run -d -name static static-web-files sh
	$ sudo docker run -d -expose=8098 -name riak riakserver
	$ sudo docker run -d -m 100m -e DEVELOPMENT=1 -e BRANCH=example-code -v $(pwd):/app/bin:ro -name app appserver
	$ sudo docker run -d -p 1443:443 -dns=dns.dev.org -v /var/log/httpd -volumes-from static -link riak -link app -h www.sven.dev.org -name web webserver
	$ sudo docker run -t -i -rm -volumes-from web -w /var/log/httpd busybox tail -f access.log

这个实例展示了5个容器设置测试一个web应用程序的改变：

后台运行一个已经准备好的镜像卷 static-web-files，镜像里含有CSS,images,和静态html, (Dockerfile中的卷指令允许web server使用这些文件);

运行一个预先准备好的riakserver,给这个容器命名为riak,并且开放8098端口让其它镜像可以连接它！ 

give the container name riak and expose port 8098 to any containers that link to it;

运行一个appserver镜像，限制它的内存时100MB，设置两个环境变量DEVELOPMENT和BRANCH，并且依只读模式将本地文件pwd的目录挂载绑定到你容器的/app/bin下

运行webserver,将容器443端口映射到外部的docker主机的1443端口，设置DNS服务dns.dev.org，创建一个卷来存放日志文件(所以我们可以从另外的主机访问它)，导入静态文件从开放的8098端口，从static容器，连接riak和app的所有开放端口，最后我们设置hostname web.sven.dev.org,与生成的SSL证书一致.


最终，我们创建了一个容器，可以运行tail -f access.log来查看web容器的卷日志，设置工作目录是/var/log/httpd. -rm表示的意思是如果这个容器存在，我们就删除容器的层


##save

	Usage: docker save image > repository.tar
	
	通过标准输出流存储镜像包含所有父层的、tag、version

##search

	Usage: docker search TERM
	
	搜索docker 镜像
	
	 -notrunc=false: 不截断输出
	 -stars=0: 仅仅输出带有lastest标记的镜像
	 -trusted=false: 只显示被信任的构建

##start

	Usage: docker start [OPTIONS] CONTAINER
	
	启动一个停止的容器
	
	  -a=false: 附加标准输入和输出，并转发所有进程信号
	  -i=false: 附加标准输入

##stop

	Usage: docker stop [OPTIONS] CONTAINER [CONTAINER...]
	
	关闭一个容器
	
	  -t=10: 等待停止容器需要的时间

这个容器主进程接收终止进程信号，在一段时间内，终止

##tag

	Usage: docker tag [OPTIONS] IMAGE REPOSITORY[:TAG]

	给镜像仓库添加标签
	
	  -f=false: Force

##top

	Usage: docker top CONTAINER [ps OPTIONS]

	查看容器内运行的进程

![docker top](http://yun.widuu.com/docker/top.png)

##version

显示docker的版本和最后更新版本信息

##wait

	Usage: docker wait [OPTIONS] NAME

	等待容器停止，打印出退出编码