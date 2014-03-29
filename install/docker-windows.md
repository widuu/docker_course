#Docker教程-在windows下的安装

**非官方备注：你的CPU一定要支持VT才可以，笔者的笔记本T6400不支持VT是装不上的，所以一定要支持VT，好在笔者的MAC很OK，公司电脑也给力**

> 上一节我们介绍了在ubuntu和centos下的安装，当然都是基于64位系统的，在学习过程中，你可能没有这些东西，当然你可以用virtualbox或者Vmware虚拟化出来，今天我们介绍的是官网给我们提供的using vagrant!

####介绍

>Docker可以用虚拟主机技术运行在Windows上像virtualbox，你可以运行linux在虚拟主机里！

####安装

#####警告

1. 官方申明docker还是在开发完善中，不建议在运营的产品中使用它，但是现在离正式版越来越接近了，请关注我们的博客http://blog.docker.io/2013/08/getting-to-docker-1-0/

1. windows的安装是社区贡献出来的，唯一的官方的安装方法是ubuntu就是上一节我们介绍的，这个版本可能是过期的因为它取决于一些二进制包的更新和发布

#####安装前准备

1. 安装virtualbox官网下载地址：https://www.virtualbox.org一路next就可以了（非官方备注：当然你不会安装或者感觉下载速度慢，可以用360或者QQ软甲管家下载自动安装）

1. 安装vagrant从官网：http://www.vagrantup.com，选择安装路径一路next就可以了

1. 下载安装git（带有ssh功能的）从官网：http://git-scm.com/downloads下载和安装，这个其实也是一路next（向github提交过代码的应该最清楚）

>官方推荐至少有2GB的磁盘空间和2GB的内存！

#####运行命令提示符

首先要打开cmd命令提示符，你可以同时按住windows键(非官方备注：ctrl键旁边那个微软图标)+R，然后输入cmd,按回车（Enter）就可以了，当然你也可以在你的计算机中搜索cmd.exe（非官方备注：如果你跟我一样用win8，可以windows键+x选择命令提示符管理员那个）

当然你可以用Cygwin终端或者git bash这些命令行都可以，操作都是一样的

#####安装一个Ubuntu virtual server

>让我们下载和运行一个已经安装好了ubuntu的docker的二进制文件

	git clone https://github.com/dotcloud/docker.git
	cd docker
	vagrant up

![安装docker](http://yun.widuu.com/docker/1.png)

![下载文件](http://yun.widuu.com/docker/2.png)

![更新内核](http://yun.widuu.com/docker/3.png)

---

#####官方文档没有的但是你会遇到的

>更新内核完成后，就出现一些字段,譬如升级完内核可能出现 vagrant halt的字样，这个时候你就要输入`vagrant halt`，然后再输入vagrant up可能会会出现如下

![更新内核](http://yun.widuu.com/docker/10.png)

这个时候你就要输入`vagrant provision`然后会检测继续更新安装，然后再`vagrant ssh`就可以了

---

>这里你要稍等比较长的时间，去打个游戏玩会吧！因为它会下载很多东西，而且我们访问美国的网速一般都比较慢，所以我建议你还是先干点别的！

我发现我安装的场景跟官方提供的显示一点都不一样，不过安装好了之后是一样的，我就拿实际的给大家看！

![](http://yun.widuu.com/docker/12.png)


>出现上边的截图后，输入`vagrant halt`,然后输出`vagrant up`来开启机器，当然你可以在virtualbox里边去关闭它！

![](http://yun.widuu.com/docker/13.png)

**Congratulations! You are running an Ubuntu server with docker installed on it. You do not see it though, because it is running in the background**

>接官方-现在你可以庆祝了，你正在运行着装好docker的unbuntu服务器了，但是你看不到它，因为它一直在后台运行（非官方备注：但是你可以从你的virtualbox中看到它）

![](http://yun.widuu.com/docker/22.png)

####登录你的unbuntu服务器

#####现在登录你的ubuntu服务器，你现在有两个选择

- 运用vagrant的命令行来操作
- 运用ssh（我用的putty）

#####运用windows命令行来操作

	vagrant ssh

>这个时候你可能看到错误信息**“ssh executable not found”.**,错误的原因是你的ssh没有加入到可执行PATH路径中，这个时候，你可以用set命令来添加路径，譬如你的ssh.exe在你的“C:Program Files (x86)Gitbin”这个目录中，你就只要输入命令

	set PATH=%PATH%;C:\Program Files (x86)\Git\bin

然后就OK了`vagrant ssh`,登录之后是这样的

![](http://yun.widuu.com/docker/15.png)

如果这个时候你出现错误*“The program ‘docker’ is currently not installed”*，那就很遗憾你只能从头开始重新安装了

#####运用ssh客户端登录 

>首先，你要拿到你登录的IP和端口，输入

	`vagrant ssh-config`

>这个时候,你会看到输出了hostname就是你登录的ip,端口号`2222`,用户默认的vagrant,密码一样都是vagrant,然后你就可以用ssh登录了，我用的是putty，官方用的也是putty

![ssh登录](http://yun.widuu.com/docker/16.png)

![ssh登录](http://yun.widuu.com/docker/17.png)

>当然如果你用git bash这种终端运行的时候也可以输入命令,然后账号密码也都是vagrant

	ssh vagrant@127.0.0.1 –p 2222

#####运行docker


####首先获得root
	
	sudo su

#####这个时候你就可以运行demo hello word了

![docker运行](http://yun.widuu.com/docker/19.png)

**下边是我用virtualbox安装的ubuntu然后用ubuntu安装的docker**

![docker运行ubuntu](http://yun.widuu.com/docker/21.png)


本文由widuu贡献翻译，转载请注明来自[http://www.widuu.com](http://www.widuu.com)~
