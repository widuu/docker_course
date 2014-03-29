#Building an Image with MongoDB

>注：

>这个例子环境是假设你已经运行了docker进程，更多详细信息请查看运行例子，如果你不喜欢sudo，你可以用户授权命令和docker组

这个例子是讲述如何创建你的已经安装号mongodb的docker镜像，我们会通过构造一个Dockerfile来下载一个基础镜像，增加一个apt源和在ubuntu安装数据库软件

##创建一个Dockerfile

创建一个空的Dockerfile文件

	touch Dockerfile
	
下一步，定义你你需要构建你的镜像的父级镜像，这里我们使用ubuntu(tag:latest)可从docker index获得:

	FROM    ubuntu:latest
	
这里我们想要运行最新的mongodb，我们需要添加10gen repo到我们的源

	# Add 10gen official apt source to the sources list
	RUN apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 7F0CEB10
	RUN echo 'deb http://downloads-distro.mongodb.org/repo/ubuntu-upstart dist 10gen' | tee /etc/apt/			sources.list.d/
	
这里我们不希望ubuntu报出init不可用，我们需要转移专一/sbin/initctl 到 /bin/true，认为一切都是可以正常工作的

	# Hack for initctl not being available in Ubuntu
	RUN dpkg-divert --local --rename --add /sbin/initctl
	RUN ln -s /bin/true /sbin/initctl
	
之后我们就更新存储库和安装mongodb

	# Install MongoDB
	RUN apt-get update
	RUN apt-get install mongodb-10gen
	
我们需要创建一个数据库目录运行mongodb(因为我们希望它运行还需要提供一个特殊的配置文件)

	# Create the MongoDB data directory
	RUN mkdir -p /data/db
	
最后我们需要开放mongodb运行的端口27107，我们用ENTRYPOINT指令来定义这个容器

	EXPOSE 27017
	ENTRYPOINT ["usr/bin/mongod"]
	
现在，我们通过我们制作的Dockerfile创建镜像

	sudo docker build -t <yourname>/mongodb .
	
现在我们需要确定运行mongodb作为一个后台进程，并且本地可以连接它

	# Regular style
	MONGO_ID=$(sudo docker run -d <yourname>/mongodb)
	
	# Lean and mean
	MONGO_ID=$(sudo docker run -d <yourname>/mongodb --noprealloc --smallfiles)
	
	# Check the logs out
	sudo docker logs $MONGO_ID

	# Connect and play around
	mongo --port <port you get from `docker ps`>
	
酷毙了！