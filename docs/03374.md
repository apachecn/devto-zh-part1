# 用 fpm 分发 go 二进制文件

> 原文：<https://dev.to/dzeban/distributing-go-binaries-with-fpm-57om>

Go 有一个很好的工具——构建系统、交叉编译、依赖管理甚至格式化工具。最后，你会得到一个二进制数。

现在有了一个二进制文件，如何在服务器上分发它呢？我的意思是，你如何解决以下问题:

1.  确定生产中当前运行的版本？
2.  升级二进制文件？
3.  降级到已知版本？
4.  用二进制文件分发额外的东西——数据文件、服务定义等等？

上述所有问题的共同点是版本控制。您需要分配并跟踪 Go 程序的版本，以保持产品的完整性。

其中一个解决方案是 docker——你将二进制文件放入`scratch`映像，将任何你想要的东西放在二进制文件中，标记映像，上传到注册表，然后在服务器上用 docker 工具使用它。

听起来既合理又新潮。但是操作码头工人是[而不是](https://thehftguy.com/2016/11/01/docker-in-production-an-history-of-failure/) [一个轻松](https://www.threatstack.com/blog/why-docker-cant-solve-all-your-problems-in-the-cloud/)的行当。与 docker 联网很难，docker 正在升级等。虽然从长远来看，这是值得的，因为它可以让你过渡到像 Kubernetes 这样的好平台。

但是如果不想用 docker 呢？如果您不希望安装 docker 工具并保持 docker 守护进程在您的产品上运行，只是为了一个二进制文件，该怎么办？

如果你不使用 docker，那么在 golang 的情况下，你就进入了一个充满敌意的地方。Go tooling 以`go get`的形式给你一个解决方案。但是`go get`只从 HEAD 获取，需要你手动使用 git 切换版本，然后调用`go build`来重建程序。此外，将开发环境保留在生产基础设施上是愚蠢的。

相反，我有一个简单得多、经过实战检验的解决方案——包。是的，像“deb”和“rpm”这样简单熟悉的发行版包。它有版本，它有很好的工具允许你查询，升级和降级软件包，提供任何额外的数据，甚至用 postinst 之类的东西编写安装脚本。

因此，我们的想法是将 go 二进制文件打包成一个包，并使用包管理实用程序将其安装在您的基础设施上。虽然构建包有时会令人害怕，但是在一个叫做 [`fpm`](https://github.com/jordansissel/fpm) 的神奇工具的帮助下，打包单个文件(带元数据)真的很简单。

允许你从各种来源创建目标包，如“deb”或“rpm ”,如普通目录、tarballs 或其他包。以下是来自 github 的来源和目标列表[:](https://github.com/jordansissel/fpm#things-that-should-work)

来源:

*   gem(甚至自动为你下载)
*   python 模块(为您自动下载)
*   pear(也为您下载)
*   目录
*   焦油(。广州)档案馆
*   每分钟转数
*   黛比
*   节点包(npm)
*   pacman (ArchLinux)软件包

目标:

*   黛比
*   每分钟转数
*   solaris
*   freebsd
*   水手
*   目录
*   Mac OS X .pkg 文件(osxpkg)
*   pacman (ArchLinux)软件包

要打包 Go 二进制文件，我们将使用“目录”源，并将其打包为“deb”和“rpm”。

让我们从“rpm”开始:

```
$ fpm -s dir -t rpm -n mypackage $GOPATH/bin/packer
Created package {:path=>"mypackage-1.0-1.x86_64.rpm"} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个有效的包！

```
$ rpm -qipl mypackage-1.0-1.x86_64.rpm
Name : mypackage
Version : 1.0
Release : 1
Architecture: x86_64
Install Date: (not installed)
Group : default
Size : 87687286
License : unknown
Signature : (none)
Source RPM : mypackage-1.0-1.src.rpm
Build Date : Mon 06 Nov 2017 07:54:47 PM MSK
Build Host : airblade
Relocations : / 
Packager : <avd@airblade>
Vendor : avd@airblade
URL : http://example.com/no-uri-given
Summary : no description given
Description :
no description given
/home/avd/go/bin/packer 
```

Enter fullscreen mode Exit fullscreen mode

但是，您可以看到，它将文件和路径放在一起，在我的例子中，放在我的 GOPATH 下。我们可以像这样告诉 fpm 把它放在目标系统的什么地方:

```
$ fpm -f -s dir -t rpm -n mypackage $GOPATH/bin/packer=/usr/local/bin/
Force flag given. Overwriting package at mypackage-1.0-1.x86_64.rpm {:level=>:warn}
Created package {:path=>"mypackage-1.0-1.x86_64.rpm"}

$ rpm -qpl mypackage-1.0-1.x86_64.rpm
/usr/local/bin/packer 
```

Enter fullscreen mode Exit fullscreen mode

这很好。

顺便说一下，因为我们把它做成 rpm 包，所以由于包压缩，我们的大小减少了 80%:

```
$ stat -c '%s' $GOPATH/bin/packer mypackage-1.0-1.x86_64.rpm
87687286
16097515 
```

Enter fullscreen mode Exit fullscreen mode

如果你使用的是基于 deb 的发行版，你所要做的就是将目标改为`deb` :

```
$ fpm -f -s dir -t deb -n mypackage $GOPATH/bin/packer=/usr/local/
bin/
Debian packaging tools generally labels all files in /etc as config files, as mandated by policy, so fpm defaults to this behavior for deb packages. You can disable this default behavior with --deb-no-default-config-files flag {:level=>:warn}
Created package {:path=>"mypackage_1.0_amd64.deb"}

$ dpkg-deb -I mypackage_1.0_amd64.deb
 new debian package, version 2.0.
 size 16317930 bytes: control archive=430 bytes.
     248 bytes, 11 lines control              
     126 bytes, 2 lines md5sums              
 Package: mypackage
 Version: 1.0
 License: unknown
 Vendor: avd@airblade
 Architecture: amd64
 Maintainer: <avd@airblade>
 Installed-Size: 85632
 Section: default
 Priority: extra
 Homepage: http://example.com/no-uri-given
 Description: no description given

$ dpkg-deb -c mypackage_1.0_amd64.deb
drwxrwxr-x 0/0 0 2017-11-06 20:05 ./
drwxr-xr-x 0/0 0 2017-11-06 20:05 ./usr/
drwxr-xr-x 0/0 0 2017-11-06 20:05 ./usr/share/
drwxr-xr-x 0/0 0 2017-11-06 20:05 ./usr/share/doc/
drwxr-xr-x 0/0 0 2017-11-06 20:05 ./usr/share/doc/mypackage/
-rw-r--r-- 0/0 135 2017-11-06 20:05 ./usr/share/doc/mypackage/changelog.gz
drwxr-xr-x 0/0 0 2017-11-06 20:05 ./usr/local/
drwxr-xr-x 0/0 0 2017-11-06 20:05 ./usr/local/bin/
-rwxrwxr-x 0/0 87687286 2017-09-06 20:06 ./usr/local/bin/packer 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我正在 Fedora 上创建 deb 包，这是一个基于 rpm 的发行版！

现在，您只需将二进制文件上传到您的 repo 中，就可以开始了。