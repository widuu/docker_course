#升级

通过docker升级到一个新的版本，通过安装方式来选择你如何升级

*新版本version0.5.3（现在已经不是）*你希望使用docker用户组，避免使用sudo(可以查看basic里边)

##apt-get安装

如果你的docker使用apt-get或者vagrant安装，你就必须用apt-get 升级

最新的version 0.6版本，你需要先添加仓库信息到你的系统

	# 将docker仓库秘钥添加到本地
	sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 36A1D7869245C8950F966E92D8576A8BA88D21E9
	
	# 将docker仓库添加到你的软件源
	sudo sh -c "echo deb https://get.docker.io/ubuntu docker main > /etc/apt/sources.list.d/docker.list"
	
	# 升级
	sudo apt-get update
	
	# 安装
	sudo apt-get install lxc-docker

##手动安装

如果你是用二进制文件安装的请参考如下几个步骤

	# 杀死docker正在运行的进程
	killall docker


	#获取最新更新的docker版本
	wget http://get.docker.io/builds/Linux/x86_64/docker-latest -O docker
	
	# 给docker执行权限
	chmod +x docker

	使用-d模式开启和断开docker进程，后台运行进程使用参数(&).保证你使用./docker的版本是最新版本的路径而不是上一个版本的路径

	# 执行新的版本
	sudo ./docker -d &

	或者你可以替换在/usr/local/bin的下的docker