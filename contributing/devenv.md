#设置一个开发环境

更容易的使用docker，我们提供了一个标准的开发环境，同样的测试、构建和发布环境是非常重要的。标准开发环境定义所有构建的系统库和二进制文件、go环境、go依赖环境等等

##Step 1: 安装Docker

docker构建环境本身就是一个docker容器，所以你第一步需要在你的主机上安装docker

你可以按照相关系统的安装说明来安装，为了确保正常工作，你需要升级到最新的docker安装版本，然后再执行下一步

##Step 2: 获取资源文件

	git clone http://git@github.com/dotcloud/docker
	cd docker
	
##Step 3: 构建环境

下面命令会使用当前目录下的dockerfile来建立开发环境，从本质上将，它将安装所有运行时 测试docker的所有依赖包，这个命令在第一次执行时候需要一点时间

	sudo make build
	
如果编译成功，祝贺你！你有纯净的docker,封装了一个标准的构建环境

##Step 4: 创建docker的二进制文件

创建docker二进制文件，运行下边的命令

	sudo make binary
	
这将会在 ./bundles/<version>-dev/binary/创建二进制文件

##用你创建的二进制文件

允许在容器外执行的目录是./bundles/<version>-dev/binary/. 你可以交换docker的可执行文件与二进制文件进行现场测试 例如在ubuntu上

	sudo service docker stop ; sudo cp $(which docker) $(which docker)_ ; sudo cp ./bundles/<version>-dev/binary/docker-<version>-dev $(which docker);sudo service docker start

>Note：Its safer to run the tests below before swapping your hosts docker binary.

##Step 5: 执行测试

执行测试，运行这个命令

	sudo make test
	
>注意：如果你在vagrant中运行测试，你需要手动指定dns条目(或者编辑Makefile文件，或者手动运行):

	sudo docker run -dns 8.8.8.8 -privileged -v `pwd`:/go/src/github.com/dotcloud/docker docker hack/make.sh test

如果测试成功，输入的结构应该是这样的

	--- PASS: TestWriteBroadcaster (0.00 seconds)
	=== RUN TestRaceWriteBroadcaster
	--- PASS: TestRaceWriteBroadcaster (0.00 seconds)
	=== RUN TestTruncIndex
	--- PASS: TestTruncIndex (0.00 seconds)
	=== RUN TestCompareKernelVersion
	--- PASS: TestCompareKernelVersion (0.00 seconds)
	=== RUN TestHumanSize
	--- PASS: TestHumanSize (0.00 seconds)
	=== RUN TestParseHost
	--- PASS: TestParseHost (0.00 seconds)
	=== RUN TestParseRepositoryTag
	--- PASS: TestParseRepositoryTag (0.00 seconds)
	=== RUN TestGetResolvConf
	--- PASS: TestGetResolvConf (0.00 seconds)
	=== RUN TestCheckLocalDns
	--- PASS: TestCheckLocalDns (0.00 seconds)
	=== RUN TestParseRelease
	--- PASS: TestParseRelease (0.00 seconds)
	=== RUN TestDependencyGraphCircular
	--- PASS: TestDependencyGraphCircular (0.00 seconds)
	=== RUN TestDependencyGraph
	--- PASS: TestDependencyGraph (0.00 seconds)
	PASS
	ok      github.com/dotcloud/docker/utils        0.017s

##Step 6: 使用Docker

你可以新建一个容器，运行一个交互式回话

	sudo make shell
	
	# type 'exit' or Ctrl-D to exit

##Extra Step: 创建文档

如果你想从你的本地网站来阅读文档，会进行更改，你可以构建文档服务

    sudo make docs
	# when its done, you can point your browser to http://yourdockerhost:8000
    # type Ctrl-C to exit