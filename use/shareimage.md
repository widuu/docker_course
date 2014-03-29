#通过存储库来分享镜像

存储库是托管带标签的镜像，是容器创建的文件系统在一起的集合。库的名称是一个标签，指示存储卡的来源，即创造源文件的所在地。

你可以找到一个和多个存储库在主机的注册表中。这里可以有一个显性或者隐性主机名作为标记库的一部分。隐性位于index.docker.io的注册中心，是"顶级"存储库和索引中心。这个注册表中可能还包含公共“用户”存储库。

所以Dockerfile不仅是一种工具，用于创建和管理自己的容器，Docker也是分享的工具.Docker项目提供了一个公共仓库中央主机注册，由用户命名空间，这个中央索引仓库提供用户身份认证，并且提供公共存储库的搜索服务。你也可以用你自己的注册表，docker会作为一个客户端运行docker search,pull login和push来提供服务


##公共存储库

这里有两种形式的公共信息库：顶级的库是有docker团队或者用户个人贡献的自己创建的库。任何人都可以读取这些仓库，他们可以帮助人们快速开发！你可以授权权限来控制某些人可以访问你的镜像，但我们只会用公共仓库为例子:

- 顶级仓库很容易被认出来，因为他们的名字没有/隔开，这些仓库都可以信任。

- 用户总是用<username/<repo_name>的方式来存储库，这是你提交的镜像推送到共公中央注册表中。

- 只有通过身份验证的用户可以把他们的命名镜像推送到中央注册表中。

- 用户镜像是不会被检查，因此你要绝对是否信任这个镜像的创造着

##在中央索引中查找公共库

你可以在命令行中执行命令通过网络在中央索引中搜索镜像，通过镜像名字，用户名字或者描述来搜索镜像：

	$ sudo docker help search
	Usage: docker search NAME
	
	Search the docker index for images
	
	  -notrunc=false: Don't truncate output
	$ sudo docker search centos
	Found 25 results matching your query ("centos")
	NAME                             DESCRIPTION
	centos
	slantview/centos-chef-solo       CentOS 6.4 with chef-solo.
	...
	
上边例子你可以看到两个搜索结构：centos和slantview/centos-chef-solo，第二个例子来自用户slantview/仓库提供的公共库，第一个结果，没有列出详细信息，它是来自中央仓库的可信任库，/来分割用户库和镜像名称。

一旦你找到镜像名称，你可以下载它：

	# sudo docker pull <value>
	$ sudo docker pull centos
	Pulling repository centos
	539c0211cd76: Download complete
	
你可以用这个镜像做什么？你可以查看example（例子）如果你准备创建自己的镜像，回到这里学习如何分享它。

##在中央注册中心贡献

任何人都可以pull在中央注册中心下载镜像，但是如果你想分享你的镜像，你必须第一步去注册一个用户,你可以在这里[central Docker Index online](https://index.docker.io/account/signup/)注册和登录你创建的用户,或者运行

	docker run login
	
这里会提示你输入用户名，这将成为公共存储库的命名空间。如果您的用户名可用,docker会提示你输入一个密码和你的邮箱地址，然后自动保存你的登录记录，然后在你准备提交你创建的镜像的时候使用！

##提交一个命名的容器

当你在一个镜像内做了一些改变然后退出，这些修改会被记录在容器的文件系统，然后你可以将容器提交成一个镜像，除了将一个容器转换成为镜像，这也是你给镜像命名的机会，具体包含你central Docker Index的用户作为你上边登录的和一个有意义的镜像名字，

	# format is "sudo docker commit <container_id> <username>/<imagename>"
	$ sudo docker commit $CONTAINER_ID myname/kickassapp

##提交一个镜像到存储库

为了推送镜像到存储库，你需要把你的容器来命名镜像（见上文），现在你可以把这个镜像的名称或者标签推送到你指定的库了

	# format is "docker push <username>/<repo_name>"
	$ sudo docker push myname/kickassapp

##可信任的创建

通过github自动化构建可信任的镜像建设和更新，直接到docker.io的服务，他会通过层架一个commit的钩子到你选择的存储库，当你推送的时候会引发一个新的构建和更新

##可信任构建的步骤

1. 创建一个账户并且登录

1. 通过链接账户菜单连接你的github账户

1. 创建一个可信任的构建

1. 选择一个github项目来构建你想要的Dockerfile。

1. 选择你想要创建的分支（默认是主分支）

1. 为受信任的建立一个名称。

1. 指定一个可选的docker tag来构建

1. 指定Dockerfile所在地，默认是/

一旦信任的构建被配置，他就会被自动的触发构建，在几分钟内，如果没有错误，你可以在docker index 看到一个新的信任构建，他将会一直同步你的github一直到你取消信任构建

如果你像查看你的信任构建状态，你可以docker index中你的[Trusted Builds page](https://index.docker.io/builds/)中查看，你可以看见构建的状态和构建的历史

一旦你创建了一个信任的连接，你可以删除和注销它。但是你不能push命令，这个金金只是通过github上的提交的代码来管理的

你可以创建多个信任的存储仓库，配置他们指定的Dockerfile或者git分支

##私有仓库

现在(version 0.6)私有库只能通过托管到自己的登记处，pull或者push你自己的登记处，你必须定义一个tag前缀来作为登记处的主机地址
，像这样：

	# Tag to create a repository with the full registry location.
	# The location (e.g. localhost.localdomain:5000) becomes
	# a permanent part of the repository name
	sudo docker tag 0u812deadbeef localhost.localdomain:5000/repo_name
	
	# Push the new repository to its home location on localhost
	sudo docker push localhost.localdomain:5000/repo_name

一旦你注册中心存储库的主机名作为标记的一部分，你可以像任何push或者pull像其他的存储库，但是你不能从中央索引搜索到它，没有用户名进行检查，你的登记功能将独立中央索引

##授权文件

这个授权文件用json存储，在你的本地家目录下的.dockercfg,它支持多个登记信息

docker login 将会创建 “https://index.docker.io/v1/” key.

docker login https://my-registry.com 将会创建 “https://my-registry.com” key.

举个例子

	{
	     "https://index.docker.io/v1/": {
	             "auth": "xXxXxXxXxXx=",
	             "email": "email@example.com"
	     },
	     "https://my-registry.com": {
	             "auth": "XxXxXxXxXxX=",
	             "email": "email@my-registry.com"
	     }
	}
	
身份验证是通过base64(<username>:<password>)