#Arch Linux安装

##官方警告

1. 官方申明docker还是在开发完善中，不建议在运营的产品中使用它，但是现在离正式版越来越接近了，请关注我们的博客http://blog.docker.io/2013/08/getting-to-docker-1-0/

1. windows的安装是社区贡献出来的，唯一的官方的安装方法是ubuntu就是上一节我们介绍的，这个版本可能是过期的因为它取决于一些二进制包的更新和发布

在Arch Linux可以从社区通过包来安装：

- [docker](https://www.archlinux.org/packages/community/x86_64/docker/)

或者选择AUR包

- [docker-git](https://aur.archlinux.org/packages/docker-git/?setlang=zh_CN)

docker计划安装最新的docker版本，docker-git包从当前主分支获得

##相关性


docker需要指定几个依赖的包，核心依赖关系：

- bridge-utils
- device-mapper
- iproute2
- lxc
- sqlite

##安装

一个简单的普通安装包

	pacman -S docker

包含了所有你需要的

通过AUR包执行

	yaourt -S docker-git

这里的指令需要先安装yaourt，如果你之前没有安装，需要去看一下用户安装包和[Arch User Repository](https://wiki.archlinux.org/index.php/Arch_User_Repository#Installing_packages)的信息

##运行docker

系统中已经安装好了docker服务，我们需要运行docker服务

	sudo systemctl start docker

设置开机启动

	sudo systemctl enable docker

##设置网络环境

IPv4数据包转发在缺省值的情况下是禁用的，所以容器可能是不起作用的，

需要开启转发，作为root用户运行在主机系统上

	sysctl net.ipv4.ip_forward=1

允许IPv4修改主机的/etc/sysctl.d/docker.conf，需要重新启动:

	net.ipv4.ip_forward=1







