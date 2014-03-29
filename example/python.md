#Python Web App

>注：

>这个例子环境是假设你已经运行了docker进程，更多详细信息请查看运行例子，如果你不喜欢sudo，你可以用户授权命令和docker组

这个例子目的是展示如何用父镜像创建自己的镜像，修改和保存一个新的镜像，我们会通过一个简单的web镜像做hello沙箱测试

##步骤

	sudo docker pull shykes/pybuilder

我们要下载shykes/pybuilder的docker镜像

	URL=http://github.com/shykes/helloflask/archive/master.tar.gz

我们通过设置URl来指向一个简单的helloflask web的简单应用

	BUILD_JOB=$(sudo docker run -d -t shykes/pybuilder:latest /usr/local/bin/buildapp $URL)

在shykes/pybuilder镜像内部有一个叫buildapp的命令，通过URL的值运行后创建一个新的容器，其中BUILD_JOB就是容器返回的id

	sudo docker attach -sig-proxy=false $BUILD_JOB

当容器运行时，我们可以连接容器来查看，参数-sig-proxy设置fasle允许我们连接和断开(CTRL -c)这个容器

	sudo docker ps -a

列出所有docker容器，如果容器运行完成，它就会出现在这里

	BUILD_IMG=$(sudo docker commit $BUILD_JOB _/builds/github.com/shykes/helloflask/master)

保存镜像的更改，并且新建镜像的名称是 _/builds/github.com/shykes/helloflask/master

	WEB_WORKER=$(sudo docker run -d -p 5000 $BUILD_IMG /usr/local/bin/runapp)

- `docker run -d` 创建一个新的容器，通过-d参数让他作为一个后台进程运行
- `-p 5000` 		  这个web应用运行时候的端口，所以这个端口必须从镜像映射到主机上
- `$BUILD_IMG`    我们要运行的容器
- `/usr/local/bin/runapp`是启动web应用的命令


用新创建的容器并且这个容器开放了5000端口，会返回容器id，并且存储了WEB_WORKER变量

	sudo docker logs $WEB_WORKER
 	  
		* Running on http://0.0.0.0:5000/


查看日志上新容器使用WEB_WORKER变量,如果一切按计划工作你应该看到在http://0.0.0.0:5000上运行的日志中输出。

	WEB_PORT=$(sudo docker port $WEB_WORKER 5000 | awk -F: '{ print $2 }')


查找私有端口内存储的web端口值来查看转发到主机的上的公共端口

	# install curl if necessary, then ...
	curl http://127.0.0.1:$WEB_PORT
	  Hello world!

通过curl命令来访问，如果正常工作你就会在你的控制台看到hello word