# 如何用一台 Mac 支持多个操作系统

> 原文：<https://dev.to/dshearer/how-to-support-multiple-oses-with-one-mac-4145>

为您的项目制作特定于操作系统的包可以为您的用户提供很好的服务。但这也是一种痛苦。Linux 发行版使用的包管理系统不同，您需要花时间学习如何正确地使用它。最后，您编写的用于制作这些包的脚本实际上只是项目的一部分，它们应该和项目的其余部分一起被自动化测试所覆盖。

在企业环境中(或者有资金支持的开源项目)，我们将为我们支持的每个操作系统提供一个 Jenkins 服务器，构建和测试包将是我们持续集成例程的一部分。不幸的是，这不是大多数开源项目的选择。

在本文中，我给出了一个方案(实际上是一堆 Make 文件和一个特定的目录结构),可以在您自己的开发机器上为任意数量的不同(Unix)操作系统测试这些打包脚本。我是在为我的项目[批发商](https://dshearer.github.io/jobber/)制作包的时候想到的。

这个方案的一个重要好处是，它可以很容易地合并您可能有的任何自动化系统测试，使您很容易确保您的程序在您声称支持的所有操作系统上工作。

我做了一个使用这个系统的玩具项目: [polly](https://github.com/dshearer/polly) ，以我养的一只猫命名，它很乐意收拾行李，定期和我一起旅行。我将以它为例，展示如何使用这个方案来增加对 CentOS 7 和 Debian 9 的支持。

## 包装:超出范围

本文不会详细讨论如何为不同的操作系统制作包。然而，如果您需要为非守护程序制作 rpm 或 Debian 包，toy 项目确实提供了一个很好的起点。如果你想要一个如何为守护进程做这件事的例子，看看 [Jobber](https://github.com/dshearer/jobber/tree/master/packaging) 。

## 先决条件

以下是您需要的工具:

*   [GNU 制作](https://www.gnu.org/software/make/)
*   [流浪者](https://www.vagrantup.com/)
*   [虚拟盒](https://www.virtualbox.org/wiki/Downloads)

VirtualBox 是开源虚拟化软件。

流浪汉是这个计划的关键。这是一个工具，它可以很容易地自动创建，引导，关机等。的虚拟机。它就像虚拟机的 Docker。

toy 项目是用 Go 编写的，但是你的系统不需要 Go 编译器，因为我们会在虚拟机上完成所有的编译。

# 它有什么作用

首先，请克隆 polly，然后查看标签`initial`。

```
$ git clone https://github.com/dshearer/polly.git
$ cd polly
$ git checkout initial 
```

Enter fullscreen mode Exit fullscreen mode

这个精彩的围棋项目最初看起来是这样的:

```
|- src/github.com/dshearer/polly
    |- main.go
    |- meow.go
    |- meow_test.go 
```

Enter fullscreen mode Exit fullscreen mode

如果你安装了 Go，你可以使用它:

```
$ go test PASS
ok      .../polly   0.006s
$ go build
$ ./polly
meow! meow! meow! meow! meow! 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们看看我们的方案是如何增加对 CentOS 7 和 Debian 9 的支持的。请看大师的提示(`git checkout master`)。

项目现在看起来是这样的:

```
|- src/github.com/dshearer/polly
    |- Makefile
    |- main.go
    |- meow.go
    |- meow_test.go
    |- packaging/
        |- Makefile
        |- centos_7/
            |- Makefile
            |- Vagrantfile
            |- polly.spec
            |- sources.mk
        |- debian_9/
            |- Makefile
            |- Vagrantfile
            |- debian-pkg/
                ...
            |- sources.mk
        |- head.mk
        |- sources.mk
        |- tail.mk
    |- system_test/
        |- meow_test.sh
    |- sources.mk 
```

Enter fullscreen mode Exit fullscreen mode

是的，有很多新的垃圾，但是制作 Linux 包并不简单。大多数新文件都在目录`packaging`中，在这个目录中，我们希望支持的每个操作系统都有一个子目录——`centos_7`和`debian_9`。`packaging/centos_7/polly.spec`是我们将用来制作 CentOS 7 包的 RPM 规范文件，`packaging/debian_9/debian-pkg`包含制作 Debian 包所需的所有标准文件。

我们在`system_test/meow_test.sh`还有一个新文件。该脚本包含安装程序后应该在程序上完成的任何系统测试。

那么这给了我们什么呢？如果你已经安装了流浪者和 VirtualBox，试试这个:

```
$ make -C packaging -j test-vm 
```

Enter fullscreen mode Exit fullscreen mode

(“-j”选项使每个操作系统并行执行此操作。偶尔，我会看到这种情况，这会导致命令挂起一段时间。如果您遇到这种情况，请尝试不带"-j "的命令。)

当这个命令完成后，你会在`packaging/results/centos_9/polly-1.0-1.el7.centos.x86_64.rpm`发现一个闪亮的新 RPM，在`packaging/results/debian_9/polly_1.0-1_amd64.deb`发现一个闪亮的新 Debian 包。此外，这些软件包将被测试，以确保它们正确安装 polly，并且 polly 将被测试，以确保它可以在这些操作系统中的每一个上工作，使用`system_test/meow_test.sh`。您可以看到测试结果如下:

```
$ tail results/centos_7/test-vm.log
Installed:
  polly.x86_64 0:1.0-1.el7.centos                                               

Complete!
# run test
vagrant ssh --no-tty -c 'make -C polly-1.0/packaging/centos_7 test-local'
make: Entering directory `/home/vagrant/polly-1.0/packaging/centos_7'
"/home/vagrant/polly-1.0/system_test/meow_test.sh"
PASS
make: Leaving directory `/home/vagrant/polly-1.0/packaging/centos_7'

$ tail results/debian_9/test-vm.log 
(Reading database ... 41645 files and directories currently installed.)
Preparing to unpack polly_1.0-1_amd64.deb ...
Unpacking polly (1.0-1) ...
Setting up polly (1.0-1) ...
# run test
vagrant ssh --no-tty -c 'make -C polly-1.0/packaging/debian_9 test-local'
make: Entering directory '/home/vagrant/polly-1.0/packaging/debian_9'
"/home/vagrant/polly-1.0/system_test/meow_test.sh"
PASS
make: Leaving directory '/home/vagrant/polly-1.0/packaging/debian_9' 
```

Enter fullscreen mode Exit fullscreen mode

# 它是如何做到的(概述)

这整个过程是由 Make files 编排的。我知道 Make 已经不怎么用了，但它确实很好用。此外，如果您的项目可以用 Make 来构建和安装，那么构建包会容易得多。

在 CentOS 和 Debian 上制作包需要几个特定于操作系统的步骤和工具(即 CentOS 的 rpmbuild 和 Debian 的 dpkg-buildpackage)。我们当然需要自动化这些步骤。我们还需要自动化将在我们的主机上执行的步骤——例如，创建和启动虚拟机。我们可能期望能够将我们的自动化代码分成以下互斥的类别:

*   要在主机上运行的自动化代码
*   将在 CentOS 7 虚拟机上运行的自动化代码
*   将在 Debian 9 虚拟机上运行的自动化代码

但事实证明有一些重叠——具体来说，我们需要在虚拟机上运行第一类中的一些代码。因此，我们的解决方案采用了这种方法:

1.  制作一个制作文件的系统，为每个平台做我们需要的一切——构建程序，构建包，运行单元测试，等等。-忽略了并非所有这些命令都可以在同一个操作系统上运行的事实
2.  添加逻辑“神奇地”从主机切换到 Debian VM，然后在 VM 上继续执行

# 它是如何做到的(详情)

项目的根是主 Make 文件- `Makefile`。其重要目标是

*   `build`:构建程序(实际上是调用`go install`)
*   `install`:将程序安装到适当的位置(例如`/usr/local/bin`)
*   `check`:运行单元测试
*   `dist`:制作一个源 tarball

(`dist`目标是所有那些`sources.mk`文件的原因:那些文件列出了它们各自目录中的源文件，并且`Makefile`将它们全部导入，以形成要包含在 tarball 中的所有源文件的最终列表。)

重要的是，主 Make 文件本身不涉及制作包或任何其他特定于操作系统的活动。那个东西被`packaging/centos_7/Makefile`和`packaging/debian_9/Makefile`覆盖了。这两者都包含以下目标:

*   `pkg-local`:构建特定于操作系统的包(假设我们在一个虚拟机上)
*   `test-local`:运行`system_test/meow_test.sh`(假设我们在一个虚拟机上)
*   `pkg-vm`:“神奇地”在装有适当操作系统的虚拟机上运行`pkg-local`(假设我们在主机上)
*   `test-vm`:运行`pkg-vm`，然后在安装了适当操作系统的虚拟机上安装软件包，最后在虚拟机上“神奇地”运行`test-local`(假设我们在主机上)

我想搞清楚一些事情。`packaging/centos_7/Makefile``packaging/debian_9/Makefile`*包含*`test-local``pkg-vm``test-vm`，但是这些目标是`packaging/tail.mk`中*定义的*，这两个使文件导入。一般来说，`packaging/<some_os>/Makefile`应该只定义特定于操作系统的东西。

为了方便起见，这里是`packaging/debian_9/Makefile` :
中的唯一目标

```
.PHONY : pkg-local
pkg-local : ${WORK_DIR}/${SRC_TARBALL}
    cp "${WORK_DIR}/${SRC_TARBALL}" \
        "${SRC_ROOT}/../polly_${VERSION}.orig.tar.gz"
    cp -R debian-pkg "${SRC_ROOT}/debian"
    cd "${SRC_ROOT}" && dpkg-buildpackage -us -uc
    mkdir -p "${DESTDIR}/"
    mv "${SRC_ROOT}"/../*.deb "${DESTDIR}/" 
```

Enter fullscreen mode Exit fullscreen mode

最后，`packaging/Makefile`也有目标`pkg-vm`和`test-vm`，但是它们只是递归地调用每个特定于操作系统的子目录的 Make 文件中的相同目标。

## “神奇”的部分

“神奇”部分的实现实际上非常简单。当调用`make -C packaging/<some_os> pkg-vm`时，生成文件

1.  使用主 Make 文件的`dist`目标生成一个源 tarball
2.  使用所需的操作系统创建(或启动)虚拟机
3.  将 tarball 复制到虚拟机
4.  在虚拟机上扩展 tarball
5.  在虚拟机上运行`make -C packaging/<some_os> pkg-local`

`test-vm`以类似的方式在虚拟机上调用`test-local`。

流浪者在第 2-5 步进场。`packaging/centos_7/Vagrantfile`和`packaging/debian_9/Vagrantfile`指定我们希望启动的虚拟机映像以及设置它所需的步骤。为了您的方便，这里有一个 CentOS:

```
Vagrant.configure("2") do |config|
    config.vm.box = "centos/7"

    config.vm.network :forwarded_port, guest: 22, host: 2223, id: "ssh"

    config.vm.provision "shell", inline: <<-SHELL yum install -y epel-release rpm-build
        yum install -y golang SHELL
end 
```

Enter fullscreen mode Exit fullscreen mode

当 Make 文件使用`vagrant`命令创建、启动等。VM，流浪者向`Vagrantfile`寻求细节。

当你第一次做这些的时候，需要一段时间来下载虚拟机镜像并创建虚拟机。令人高兴的是，流浪者将缓存这些图像，以便后续运行不会再次下载它们。

此外，Make 文件会在制作完成后立即拍摄每个虚拟机的快照。如果您多次运行`make -C packaging/<some_os> pkg-vm`或`make -C packaging/<some_os> test-vm`，它们将重用虚拟机，并且之前运行的任何文件仍将在虚拟机上。要恢复到原始快照，在调用其他目标之前执行`make -C packaging/<some_os> clean`。

# 下一步

我在介绍中提到，这个方案自然支持在这些操作系统上运行系统测试。在 polly 中，`system_test/meow_test.sh`是一个微型玩具程序的微型玩具系统测试。在一个真实的项目中，不应该忽视将系统测试作为开发过程一部分的机会。

Jobber 有[一个很好的例子](https://github.com/dshearer/jobber/tree/master/platform_tests)，包含使用[机器人框架](http://robotframework.org/)的每个主要特性的测试。当我在 Jobber 上工作时，我只需要做`make -C packaging -j test-vm`并观察测试在每个支持的操作系统上并行运行。当他们完成后，会有一份漂亮的每个 OS 的机器人测试报告在`packaging/results`等着我。

# 结论

支持多种操作系统是很困难的，尤其是如果你没有自动化许多繁琐部分的基础设施。本文给出的方案提供了这样的基础设施，我希望它能对您自己的项目有所帮助。