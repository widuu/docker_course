#SSH Daemon Service

>注：

>这个例子环境是假设你已经运行了docker进程，更多详细信息请查看运行例子，如果你不喜欢sudo，你可以用户授权命令和docker组

---

>官方视频可以官方查看，但是我也做了视频你可以查看中文版的我做的视频

---

你可以直接获得sshd容器

	sudo docker pull dhrp/sshd

密码是screencast

##视频讲解内容

	# 大家好！我们试着安装sshd并且让它作为一个服务来运行
	# 我们需要先用pull获得一个base ubuntu镜像.
	$ docker pull ubuntu
	# 我下载它非常快(因为视频的在美国)
	# 现在我们用-i -t命令来运行镜像的bash
	# .
	$ docker run -i -t ubuntu /bin/bash
	# 好的！我们进来了
	# 让我们安装openssh-server
	$ apt-get update
	$ apt-get install openssh-server
	# 好的，让我们看看如何运行它.
	$ which sshd
	# 我们需要建立授权目录
	$ mkdir /var/run/sshd
	$ /usr/sbin/sshd
	$ exit
	# 我们需要提交它
	# 我们应该提交哪一个?
	$ docker ps -a |more
	$ docker commit a30a3a2f2b130749995f5902f079dc6ad31ea0621fac595128ec59c6da07feea dhrp/sshd
	# 我给这个容器命名 dhrp/sshd
	# 我们可以继续运行
	$ docker run -d dhrp/sshd /usr/sbin/sshd -D # D for daemon mode
	# 它运行了吗?
	$ docker ps
	# 是的!
	# 让我们停止它
	$ docker stop 0ebf7cec294755399d063f4b1627980d4cbff7d999f0bc82b59c300f8536a562
	$ docker ps
	# 我们重新连接并且指定端口
	$ docker run -d -p 22 dhrp/sshd /usr/sbin/sshd -D
	$ docker port b2b407cf22cf8e7fa3736fa8852713571074536b1d31def3fdfcd9fa4fd8c8c5 22
	# 现在我们可以通过端口连接
	# 我们可以使用公共ip来连接
	$ hostname
	# *ifconfig* 来查看我们的公网ip地址
	$ ifconfig
	$ ssh root@192.168.33.10 -p 49153
	# 哦不好意思我们忘记了设置密码
	$ docker commit b2b407cf22cf8e7fa3736fa8852713571074536b1d31def3fdfcd9fa4fd8c8c5 dhrp/sshd
	$ docker ps -a
	$ docker run -i -t dhrp/sshd /bin/bash
	$ passwd
	$ exit
	$ docker commit 9e863f0ca0af31c8b951048ba87641d67c382d08d655c2e4879c51410e0fedc1 dhrp/sshd
	$ docker run -d -p 22 dhrp/sshd /usr/sbin/sshd -D
	$ docker port a0aaa9558c90cf5c7782648df904a82365ebacce523e4acc085ac1213bfe2206 22
	# *ifconfig* 我们查看公网地址
	$ ifconfig
	$ ssh root@192.168.33.10 -p 49154
	# 谢谢你查看，如果有问题可以发送邮件到 thatcher@dotcloud.com