#创建镜像 (Dockerfile 参考)

**Docker可以作为创建者**读取Dockerfile文件中的指令，自动执行步骤并且创建一个新的镜像。执行`docker build`命令，它会按照文档执行并最终创建一个镜像。


目录

- 创建镜像
 
  - 1.使用
  - 2.格式
  - 3.指令
  
	 - 3.1  FROM
	 - 3.2  MAINTAINER 
	 - 3.3  RUN
	 - 3.4  CMD
	 - 3.5  EXPOSE
	 - 3.6  ENV
	 - 3.7  ADD
	 - 3.8  ENTRYPOINT
	 - 3.9  VOLUME
	 - 3.10 USER
	 - 3.11 WORKDIR

   - 4.Dockerfile举例
  

1.Usage

从镜像资源中创建一个镜像，在你的跟目录下创建一个描述文件叫做Dockerfile，这个将描述你创建镜像的步骤.

`docker build`要用路径的资源库来作为参数(像.)

	sudo docker build .
	
先从存储源查找要本地创建的环境，build会在docker后台运行的，而不是用命令行界面，所以所有的本地环境都会被后台进程多调度。当环境被发送到后台进程时，Docker命令行会报告"Uploading context"

你可以指定存储卡和标记，来保存构建成功的镜像:

	sudo docker build -t shykes/myapp .

Docker进程会按照步骤步骤一步操作，必要时会输出必要的结果, 创建完成之后会输出你的镜像id,Docker会自动清楚你发送的环境。

当你构建成功之后，你就要考虑把镜像推送到库中。

#2. Format

Dockerfile格式很简单的:

	# Comment
	指令 参数
	
指令是不区分大小写的, 然而约定是大写的以便区分其他的参数

Docker会读测试Dockerfile中的指令，第一条命令必须是'FROM',来指定你正在构建的基本镜像

docker以#开头为一个注释，一个#在的地方会被认为是一个注释，例如

	# Comment
	RUN echo 'we are running some # of cool things'

#3.指令

可以使用Dockerfile中的指令来构建镜像

##3.1 FROM

	FROM <image>

或者

	FROM <image>:<tag>
	
FROM指令来构建基础镜像。因此，一个有效的Dockerfile必须是用FROM做第一个指令。镜像可以是任何有效的镜像，它会很容易从公共存储卡中获取。

FROM在Dockerfile必须是第一个没有意见性（RUN之类的）指令

FROM命令可以在Dockerfile中出现多次创建多个镜像，在用新的FROM之前简单的记录下镜像的id

如果FROM指令没有tag，就假设tag是latest.如果tag不存在，就会发挥一个错误信息。

##3.2 MAINTAINER

	MAINTAINER <name>
	
这个指令是允许你设置镜像生成的作者信息

##3.3 RUN

	RUN <command>
	
RUN指令将会在当前镜像执行任何命令并提交结果，提交的镜像的结果将用户Dockerfile的下一步操作

分层RUN指令和生成提交是docker的核心概念，提交是廉价的从任何点创建一个镜像历史，就行代码控制一样。

>已知问题

- 问题783 关于文件权限的问题，可能会发生在使用AUFS文件系统。你要注意她视图使用rm文件，例如，描述一个解决方案

- 问题2424 本地将不会自动被设置

##3.4 CMD

CMD有三种形式：

- CMD ["executable","param1","param2"](就像exec,首选)

- CMD ["param1","param2"] (作为ENTRYPOINT默认参数)

- CMD command param1 param2 (像shell一样)

Dockerfile只能有一个CMD，如果你写了多个CMD，最后那个CMD会生效。

CMD主要给一个容器执行提供默认参数，这些默认值可以是一个热可执行的，或者他们可以省略可执行文件，在这种情况下，你必须指定一个ENTRYPOINT你交号

当shell中使用执行格式，CMD命令会设置镜像运行的指令，这个功能相当于docker 提交 -run <command> 将会执行在/bin/sh -c

	FROM ubuntu
	CMD echo "This is a test." | wc -
	
如果你希望你的容器每次都执行相同的可执行文件，那么你应该考虑使用ENTRYPOINT结合CMD，可以查看3.8 ENTRYPOINT.

>注意

>不要混淆运行CMD。RUN CMD，RUN必须提交结果，CMD在构建镜像是并不执行，但在镜像构建成功后会执行

##3.5 EXPOSE

	EXPOSE <port> [<port>...]
	
EXPOSE指令会对连接着开放端口使用，这个功能相当于运行docker提交 -run '{"PortSpecs": ["<port>", "<port2>"]}',相当于docker run -p 

##3.6 ENV

	ENV <key> <value>
	
ENV指令设置环境变量key对应值value,这个值将会被传递给后边使用的指令，这个功能相当于在命令前面加了<key>=<value>

>注意：

>环境变量会持续到一个镜像的生成

##3.7 ADD

	ADD <src> <dest>
	
ADD指令是将<Src>复制新文件，并将它们添加到容器的文件系统<dest>。

<src>必须是一个文件或者目录相对于源目录（也就是构建的环境）或者一个远程文件的URL。

<dest> 是容器将被复制的目录地址。

所有新创建的文件和目录都是0755权限，uid和gid 0。

>注意

>如果你有stdin标准输入流创建(docker build - < somefile),没有建立环境，所以Dockerfile只能包含一个基于URL添加语句。

>注意

>如果你的URL文件需要身份认证，你将需要使用运行wget，运行curl或者其他工具添加到容器是不支持身份验证的

副本必须遵循一下规则：

<src>路径必须是声称到环境中，你不能添加../something/someting,因为docker build第一步会发送环境目录（和子目录）到dicker后台进程

如果<src>是URl，并将<dest>不已斜线结尾，然后将从URl中下载并复制到<dest>

如果<src>是URL，并且<dest>以斜线结尾，则文件名是从URL推断然后文件会下载到<dest>/<filename>,例如，ADD http://example.com/foobar/将会创建file/foobar，这个URL必须是一个平常的路径，使用合适的文件名，一下情况下是无法工作的(http://example.com)

如果<src>是目录，将整个目录复制，包含文件的原始数据

如果<src>是压缩文件个事(譬如,gzip、bzip2或者xz)，那么它会解压到目录，从URL资源不解压

如果一个目录被复制或者解压，他们都会执行相同的行为tar -x 作为结果的集合

- 无论目标路径是否存在
- 源的tree内容是否存在

产生冲突的时候会按照2来解决

如果<src>是其它文件类型，她被单独复制连同它的数据。这种情况下，如果<dest>以斜线结尾，它就会被认为是一个目录，并将src中的写入<desr>/base（<src>）.

如果<desc>不已斜线结尾，它会被认为是一个常规文件，并且<src>的文件会被写入到<dest>文件中。

如果<dest>不存在，创建连同所有缺失的目录路径

##3.8 ENTRYPOINT

ENTRYPOINT有两种形式：

- ENTRYPOINT ["executable", "param1", "param2"] (像 exec, 首选)

- ENTRYPOINT command param1 param2 (作为shell执行)

Dockerfile中只能有一个ENTRYPOINT，如果你有多个ENTRYPOINT，则Dockerfile中只能最后一个会被执行

一个ENTRYPOINT会帮你配置容器作为容器的一个可执行文件，也就是说当你指定一个ENTRYPOINT，那么整个容器中都会运行这个可执行文件

ENTRYPOINT指令将会被当作一个不会被覆盖的命令参数传给运行的docker,不像CMD，这就是允许参数传递给入口，即docker run <image> -d 将会通过 -d参数来作为执行入口
	
你可以指定参数在 ENTRYPOINT JSON数组（如exec执行多个），或者通过使用一个CMD命令语句，这个入口的参数不会被docker run所重写，但是通过指定CMD的参数来重写docker run 参数

像CMD，你可以指定一个ENTRYPOINT的字符串，它将会在/bin/sh -c中执行

	FROM ubuntu
	ENTRYPOINT wc -l -

例如，Dockerfile的镜像经常会将stdin作为输入("-")或者但列出行数("-l"),如果你像让这个可选，默认情况下你可以使用CMD

	FROM ubuntu
	CMD ["-l", "-"]
	ENTRYPOINT ["/usr/bin/wc"]
	

##3.9 VOLUME

	VOLUME ["/data"]	
	
VOLUME指令会创建一个挂载点根据指定的名字和标记挂在外部的主机或者其他容器，更多实例可以查看docker安装说明，共享文档和目录
	
##3.10 USER

	USER daemon

USER质量设置镜像运行时的用户名或者Uid
	
##3.11 WORKDIR

	WORKDIR /path/to/workdir
	
WORKDIR命令执行工作目录的来执行CMD

#4. Dockerfile 例子

	# Nginx
	#
	# VERSION               0.0.1
	
	FROM      ubuntu
	MAINTAINER Guillaume J. Charmes <guillaume@dotcloud.com>
	
	# make sure the package repository is up to date
	RUN echo "deb http://archive.ubuntu.com/ubuntu precise main universe" > /etc/apt/sources.list
	RUN apt-get update
	
	RUN apt-get install -y inotify-tools nginx apache2 openssh-server
	
---

	# Firefox over VNC
	#
	# VERSION               0.3
	
	FROM ubuntu
	# make sure the package repository is up to date
	RUN echo "deb http://archive.ubuntu.com/ubuntu precise main universe" > /etc/apt/sources.list
	RUN apt-get update
	
	# Install vnc, xvfb in order to create a 'fake' display and firefox
	RUN apt-get install -y x11vnc xvfb firefox
	RUN mkdir /.vnc
	# Setup a password
	RUN x11vnc -storepasswd 1234 ~/.vnc/passwd
	# Autostart firefox (might not be the best way, but it does the trick)
	RUN bash -c 'echo "firefox" >> /.bashrc'
	
	EXPOSE 5900
	CMD    ["x11vnc", "-forever", "-usepw", "-create"]
	
---

	# Multiple images example
	#
	# VERSION               0.1
	
	FROM ubuntu
	RUN echo foo > bar
	# Will output something like ===> 907ad6c2736f
	
	FROM ubuntu
	RUN echo moo > oink
	# Will output something like ===> 695d7793cbe4
	
	# You'll now have two images, 907ad6c2736f with /bar, and 695d7793cbe4 with
	# /oink.

