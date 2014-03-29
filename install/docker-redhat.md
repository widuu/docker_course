#Red Hat Enterprise Linux安装

##官方警告

1. 官方申明docker还是在开发完善中，不建议在运营的产品中使用它，但是现在离正式版越来越接近了，请关注我们的博客http://blog.docker.io/2013/08/getting-to-docker-1-0/

1. windows的安装是社区贡献出来的，唯一的官方的安装方法是ubuntu就是上一节我们介绍的，这个版本可能是过期的因为它取决于一些二进制包的更新和发布

docker可以在RHEL的EPEL上运行。docker指令可以在RHEL和Centos上工作，他们可能会和其他二进制EL6所兼容工作，但是我们没有去测试！

请注意，这个包是有社区一起努力创建和维护的，作为一个RHEL的额外包！

还要注意docker的局限性，docker只能运行在64位系统上！

##安装

首先，你需要安装EPEL仓库，请看这里[EPEL installation instructions.](https://fedoraproject.org/wiki/EPEL#How_can_I_use_these_extra_packages.3F)

docker的包是由EPEL提供的

如果你已经安装了(不相关)的docker包，它会跟docker-io有冲突，有一个错误报告，如果想继续安装docker-io，请先删除docker.

下一步，来让我们安装docker-io在我们的主机上

	sudo yum -y install docker-io

升级docker-io包

	sudo yum -y update docker-io

现在我们就安装好了，让我们开始docker进程

	sudo service docker start

如果你想让他开机启动，我们需要这样做

	sudo chkconfig docker on

现在让我们确认一下docker是否工作了 

	sudo docker run -i -t fedora /bin/bash

运行了，OK你现在去运行hello word的实例吧

问题？

如果你有一些问题，你可以去这个地址报告[Red Hat Bugzilla for docker-io component.](https://bugzilla.redhat.com/enter_bug.cgi?product=Fedora%20EPEL&component=docker-io)