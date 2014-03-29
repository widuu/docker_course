#在docker上运行.NET应用

这篇博客涵盖了在轻量级容器docker中使用mono(1)来运行简单的.net应用。我在windows中运行docker,这个工作非常快，您可以参照[安装说明来安装docker](http://www.widuu.com/docker/)

>(1)Mono 是一个由Novell 公司主持的项目。该项目的目标是创建一系列符合ECMA 标准（Ecma-334 和Ecma-335）的.NET 工具，包括C# 编译器和共通语言执行平台。与微软的.NET Framework 不同，Mono 项目不仅可以运行于Windows 系统上，还可以运行于Linux，FreeBSD，Unix，Mac OS X 和Solaris。

###创建基础镜像

第一步创建一个docker镜像来安装mono。我们将使用这个作为容器的基本镜像。获取最新的Mono版本，我们使用[Timotheus Pokorra创建的包](http://software.opensuse.org/download/package?project=home:tpokorra:mono&package=mono-opt)来安装在Ubuntu12.04的镜像上，下边是Dockerfile:

	FROM ubuntu:12.04
	MAINTAINER widuu
	
	RUN apt-get -y -q install wget
	RUN wget -q http://download.opensuse.org/repositories/home:tpokorra:mono/xUbuntu_12.04/Release.key -O- | apt-key add -
	RUN apt-get remove -y --auto-remove wget
	RUN sh -c "echo 'deb http://download.opensuse.org/repositories/home:/tpokorra:/mono/xUbuntu_12.04/ /' >> /etc/apt/sources.list.d/mono-opt.list"
	RUN apt-get -q update
	RUN apt-get -y -q install mono-opt

#####对应上边的步骤：

1. 安装wget
2. 添加apt-get源
3. 卸载wget
4. 添加openSUSE的镜像源列表
5. 安装mono


起初，我做了以上所有的步骤，而且我是按照上边的步骤一个一个来执行的。没有人会对安装wget感兴趣，最后我分裂成独立的RUN命令,因为这正是其他人所做的。

我们利用Dockerfile来创建镜像：

	$ docker build -t widuu/mono .

我们需要运行这个制作的镜像来检查mono是否安装成功

	vagrant@precise64:~/mono$ docker run -i -t widuu/mono bash
	root@0bdca65e6e8e:/# /opt/mono/bin/mono --version
	Mono JIT compiler version 3.2.6 (tarball Sat Jan 18 16:48:05 UTC 2014)

现在mono安装在/opt下并且工作了

###运行一个应用程序

首先我们需要部署一个简单的应用程序

	using System;
	
	namespace HelloWorld
	{
	        public class Program
	        {
	                static void Main(string[] args)
	                {
	                        Console.WriteLine("Hello World");
	                }
	        }
	}

这个例子的目的，我们使用VS命令和 msbuild来构建将要创建的应用程序，并且在容器内输出

	msbuild /property:OutDir=C:\tmp\helloworld HelloWorld.sln

或者,我们可以在容器内调用xbuild或gmcs。

运用Dockerfile运行这个应用程序非常简单：

	FROM widuu/mono
	MAINTAINER widuu
	
	ADD app/ .
	CMD /opt/mono/bin/mono `ls *.exe | head -1`

>注意：这个例子依赖于上边所创建的widuu/mono镜像，也认为编译后的应用程序在/app文件夹中：

	$ ls app
	HelloWorld.exe

CMD命令将简单地使用mono运行中发现的第一个可执行程序来构建输出，让我们创建这个镜像并运行：

	$ docker build -t widuu/helloworld-container .
	...
	$ docker run widuu/helloworld-container
	Hello World

It worked!

###WEB应用程序

运行一个web应用程序到[OWIN](http://www.asp.net/vnext/overview/owin-and-katana "OWIN")(2)然后做更多的工作,在这个例子中我们使用的简单源码来自[OWIN/Katana-on-Heroku post.](http://friism.com/running-owin-katana-apps-on-heroku)

>(2)OWIN(Open Web Interface for .NET)是在.net的web server和web应用之间定义了一套规范.Katana是微软实现了OWIN的一个Web Server的项目

	$ ls app/
	HelloWorldWeb.exe  Microsoft.Owin.Diagnostics.dll  Microsoft.Owin.dll  Microsoft.Owin.Host.HttpListener.dll  Microsoft.Owin.Hosting.dll  Owin.dll

Dockerfile会使容器开放5000端口，CMD命令会开启web应用程序并且监听5000端口

	FROM widuu/mono
	MAINTAINER widuu
	
	ADD app/ .
	EXPOSE 5000
	CMD ["/opt/mono/bin/mono", "HelloWorldWeb.exe", "5000"]

我们运行这个容器，并且将5000端口映射到我们主机的80端口上：

	$ docker run -p 80:5000 -t widuu/mono-hello-world-web

我们可以访问OWiN的网站了,http://localhost







