##Using Puppet

>注意

>请注意这是一个社区贡献的安装方式，官方使用ubuntu安装，这个版本可能随时过期。

##要求

使用本指南你需要从Puppetlabs安装Puppet。

目前模块还是使用官方PPA，也只能运行在ubuntu上

##安装

[Puppet Forge](https://forge.puppetlabs.com/garethr/docker/)有可用的模块，并且你可以使用内置工具安装。

	puppet module install garethr/docker

如果你想下载源代码，你可以可以在[Github](https://www.github.com/garethr/garethr-docker)上找到它。

##使用

模块提供一个puppet类来安装docker,并且两种方式来管理镜像和容器

###安装
	
	include 'docker'

###镜像

下一步是安装一个docker镜像，我们可以使用如下的方法来定义：

	docker::image { 'ubuntu': }

这相当于运行

	docker pull ubuntu

注意只有镜像的名字不存在时，这将会下载一个大的二进制文件所以第一次运行需要一段时间，出于这个原因，关闭定义默认的5分钟超时。请注意，你也可以删除你不需要的镜像：

	docker::image { 'ubuntu':
	  ensure => 'absent',
	}

##容器

现在你有一个容器，你可以用docker的run命令来管理容器

	docker::run { 'helloworld':
	  image   => 'ubuntu',
	  command => '/bin/sh -c "while true; do echo hello world; sleep 1; done"',
	}

这就相当于运行了下面的命令

	docker run -d ubuntu /bin/sh -c "while true; do echo hello world; sleep 1; done"

运行还可以包含一些可选的参数：

	docker::run { 'helloworld':
	  image        => 'ubuntu',
	  command      => '/bin/sh -c "while true; do echo hello world; sleep 1; done"',
	  ports        => ['4444', '4555'],
	  volumes      => ['/var/lib/counchdb', '/var/log'],
	  volumes_from => '6446ea52fbc9',
	  memory_limit => 10485760, # bytes
	  username     => 'example',
	  hostname     => 'example.com',
	  env          => ['FOO=BAR', 'FOO2=BAR2'],
	  dns          => ['8.8.8.8', '8.8.4.4'],
	}

注意，上边可以用数组的方式定义ports、env、卷等等