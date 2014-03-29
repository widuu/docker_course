#PostgreSQL Service

>注：

>这个例子环境是假设你已经运行了docker进程，更多详细信息请查看运行例子，如果你不喜欢sudo，你可以用户授权命令和docker组

##在docker安装PostgreSQL

运行一个docker容器shell

	sudo docker run -i -t ubuntu /bin/bash
	
升级依赖包

	apt-get update
	
安装 python-software-properties, software-properties-common, wget and vim.

	apt-get -y install python-software-properties software-properties-common wget vim
	
添加PostgreSQL的存储库，它包含了PostgreSQL最新稳定版本9.3。

	wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | apt-key add -
	echo "deb http://apt.postgresql.org/pub/repos/apt/ precise-pgdg main" > /etc/apt/sources.list.d/pgdg.list
	apt-get update

最后，我们安装PostgreSQL9.3

	apt-get -y install postgresql-9.3 postgresql-client-9.3 postgresql-contrib-9.3
	
现在，创建一个PostgreSQL超级用户，来创建数据库和其他工作，Vagrant’s 惯例角色名字是docker并且把docker的密码分配给它

	su postgres -c "createuser -P -d -r -s docker"
	
创建一个测试数据库名字也叫docker所有者是上边我们创建的docker角色

	su postgres -c "createdb -O docker docker"
	
调整PostgreSQL配置,远程连接到数据库。确保在 /etc/postgresql/9.3/main/postgresql.conf有以下行

	host    all             all             0.0.0.0/0               md5
	
另外, 在 /etc/postgresql/9.3/main/postgresql.conf 取消监听地址，像这样：

	listen_addresses='*'
	
>注意

>这对于开发postgresql设置是唯一的，请参考postgresql文档如何调整这些设置，以确保安全

退出
	
	exit
	
创建一个容器，给它添加名字。容器<container_id> 在bash中有提示；你可以本地使用docker ps -a获得。

	sudo docker commit <container_id> <your username>/postgresql
	
最后通过docker运行postgresql服务

	CONTAINER=$(sudo docker run -d -p 5432 \
	  -t <your username>/postgresql \
	  /bin/su postgres -c '/usr/lib/postgresql/9.3/bin/postgres \
	    -D /var/lib/postgresql/9.3/main \
	    -c config_file=/etc/postgresql/9.3/main/postgresql.conf')
	  
使用psql PostgreSQL服务器(您将需要连接到PostgreSQL客户机上安装这台机器。对于ubuntu,使用sudo apt-get安装postgresql-client)。

	CONTAINER_IP=$(sudo docker inspect -format='{{.NetworkSettings.IPAddress}}' $CONTAINER)
	psql -h $CONTAINER_IP -p 5432 -d docker -U docker -W
	
在这之前如果你需要，请创建用户或者数据库

	psql (9.3.1)
	Type "help" for help.
	
	docker=# CREATE DATABASE foo OWNER=docker;
	CREATE DATABASE
	
另外，提交你的新创建的镜像到docker index

	sudo docker login
	Username: <your username>
	[...]
	
	sudo docker push <your username>/postgresql
	
PostgreSQL服务自动启动

运行我们的镜像似乎很复杂，我们必须指定docker运行整个命令，让我们简化服务器启动时自动运行的命令。

	sudo docker commit -run='{"Cmd": \
	  ["/bin/su", "postgres", "-c", "/usr/lib/postgresql/9.3/bin/postgres -D \
	  /var/lib/postgresql/9.3/main -c \
	  config_file=/etc/postgresql/9.3/main/postgresql.conf"], "PortSpecs": ["5432"]}' \
	  <container_id> <your username>/postgresql
	  
从现在开始，只需要输入 docker run  <your username>/postgresql ，PostgreSQL就会自动启动了


