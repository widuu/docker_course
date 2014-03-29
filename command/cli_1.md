#Docker命令行帮助

>列出可用的命令，或者运行不带参数的docker或者执行docker帮助

	sudo docker
	
	useage of docker
	
		-D   默认false 允许调试模式(debug mode)
		
		-H   默认是 unix:///var/run/docker.sock tcp://[host[:port]]来绑定 或者 unix://[/path/to/socket]来使用(二进制文件的时候)，当主机ip host=[0.0.0.0],(端口)port=[4243] 或者 path=[/var/run/docker.sock]是缺省值，做为默认值来使用
		
		-api-enable-cors 默认flase 允许CORS header 远程api
		
		-b   默认是空，附加在已存在的网桥上，如果是用'none'参数，就禁用了容器的网络
		
		-bip 默认是空，使用提供的CIDR（Classless Inter-Domain Routing-无类型域间选路）标记地址动态创建网桥(dcoker0),和-b参数冲突
		
		-d   默认false 允许进程模式(daemon mode)
		
		-dns 默认是空，使docker使用指定的DNS服务器
		
		-g   默认是"/var/lib/docker":作为docker使用的根路径
		
		-icc 默认true，允许inter-container来通信
		
		-ip  默认"0.0.0.0" ：绑定容器端口的默认Ip地址
		
		-iptables 默认true 禁用docker添加iptables规则
		
		-mtu 默认1500 : 设置容器网络传输的最大单元(mtu)
		
		-p   默认是/var/run/docker.pid 进程pid使用的文件路径
		
		-r   默认是true 重启之前运行的容器
		
		-s   默认是空 ，这个是docker运行是使用一个指定的存储驱动器
		
		-v   默认false 打印版本信息和退出 


![](http://yun.widuu.com/docker/docker-docker.png)


>当你的进程使用-d标识的时候,docker使用一个持久的进程来管理容器，docker使用相同的进程和客户端.

>docker使用 -d -s 来映射存储程序，从而迫使docker运用映射的存储器来存储驱动程序.

>docker使用 -d -dns 8.8.8.8，来设置所有的docker容器的DNS服务器.

>docker使用 -d -D参数，来让进程输出debug信息

>docker客户端，也可以使用DOCKER_HOST的环境变量参数来改变docker -H的参数设置

	docker -H tcp://0.0.0.0:4243 ps
	# or
	export DOCKER_HOST="tcp://0.0.0.0:4243"
	docker ps
	# both are equal
	
####attach

	usage : docker attach CONTAINER
	
		attach 来运行一个容器
		
		  -nostdin   默认参数false 不要附加stdin（输入）
		  -sig-proxy 默认true Proxify所有接收信号流程(即使在non-tty模式)
		  
		  
>你可以把docker从容器中分离出来运行，然后用CTRl -c来退出或者CTRL -\来获得一个异常堆栈的docker退出，当这个容器退出过程会将退出代码返回给客户端

>使用docker stop来停止一个容器

>使用docker kill来杀死一个容器


#####attach examples

	 ID=$(sudo docker run -d ubuntu /usr/bin/top -b)
	 sudo docker attach $ID
	 
![](http://yun.widuu.com/docker/attach.png)


本文由widuu贡献翻译，转载请注明来自[http://www.widuu.com](http://www.widuu.com)~