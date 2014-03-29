#Hello word

---

##运行例子


所有的实例你都需要在你的机器中运行docker进程，后台运行docker进程，简单演示

	sudo docker -d &


现在你可以运行Docker客户端，默认情况下所有的命令都会经过一个受保护的Unix socket转发给docker进程,所以我们必须运行root或者通过sudo授权。

	sudo docker help

##hello word


>注：

>这个例子环境是假设你已经运行了docker进程，更多详细信息请查看运行例子，如果你不喜欢sudo，你可以用户授权命令和docker组

这个是docker的最基础的使用例子


下载ubuntu base镜像

	# Download an ubuntu image
	sudo docker pull ubuntu

---

(注：非官方)相对比较而且你可以下载busybox镜像，他是最小的linux系统，这个镜像可以从docker仓库获取！

	sudo docker pull busybox

---

	sudo docker run ubuntu /bin/echo hello world


这个命令会运行一个简单的echo 命令，这个时候我们的控制其就会输出"hello word"。

讲解:

- `sudo` 	   执行root权限
- `docker run` 运行一个新的容器
- `ubuntu`     我们想要在内部运行命令的镜像
- `/bin/echo`  我们想要在内部运行的命令
- `hello word` 输出的内容

##Hello World 进程

>注：

>这个例子环境是假设你已经运行了docker进程，更多详细信息请查看运行例子，如果你不喜欢sudo，你可以用户授权命令和docker组

这是有史以来最无聊的进程


这个例子假设是你已经安装了docker，并且你已经通过docker pull下载导入了ubuntu镜像，我们将会用到ubuntu镜像运行一些姜丹的例子hello word进程，这里的将会每秒钟打印出hello word一直到我们停止他

	CONTAINER_ID=$(sudo docker run -d ubuntu /bin/sh -c "while true; do echo hello world; sleep 1; done")


我们已经用ubuntu镜像新建一个容器并且运行了一个简单的hello world进程

- `sudo docker run -d` 运行提个新的容器，我们通过-d命令让他作为一个进程运行
- `ubuntu`             是一个我们想要在内部运行命令的镜像
- `/bin/sh -c`         是我们想要在容器内部运行的命令
- `while true; do echo hello world; sleep 1; done` 这是一个简单的脚本，我们仅仅只是每秒打印一次hello word 一直到我们结束它
- `$CONTAINER_ID` 我们运行命令将会返回一个容器id

	sudo docker logs $CONTAINER_ID

Check the logs make sure it is working correctly.

我们查看日志文件来确认它是否正常工作

- `docker logs`   返回容器的日志
- `$CONTAINER_ID` 我们想查看的容器ID

	sudo docker attach -sig-proxy=false $CONTAINER_ID

连接到容器实时查看结果


- `docker attach`    允许我们查看一个后台进程.
- `-sig-proxy=false` 不使用容器转发信号，允许我们使用ctrl -c来退出
- `$CONTAINER_ID` 	 我们要查看的容器id
 
	sudo docker ps

查看正在运行的进程

`docker ps`查看所有运行的docker管理进程

	sudo docker stop $CONTAINER_ID

当我们不需要时停止容器

- `docker stop`   停止一个容器
- `$CONTAINER_ID` 我们需要停止的容器id

确认它是否停止了

	sudo docker ps