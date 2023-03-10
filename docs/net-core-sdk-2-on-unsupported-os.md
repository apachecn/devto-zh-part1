# 。不支持的操作系统上的 NET Core SDK 2

> 原文：<https://dev.to/dgg/net-core-sdk-2-on-unsupported-os>

我已经有一段时间没有合适的开发笔记本电脑了。但这个事实并没有阻止我在我的旧 iMac 上到处乱闯。

你知道是什么阻止了我的黑客行为吗？事实越新*。NET SDK 2.0* 无法安装在我的非 Sierra 系统中。

但是它没有，想知道是怎么回事吗？

## 生活在过去

我可能会被认为是一个“视窗家伙”，因为这是我运行最多的。然而，我对标签的厌恶和对不同事物的热爱让我做了一些事情，比如让一台 iMac 作为家用电脑，让一台双启动(Windows 10，不管它的版本名称是什么，以及 Ubuntu Mate Xenial)二手联想笔记本电脑作为家里其他人的工作笔记本电脑。iMac 放在家里公共区域的桌子上，所以我最近经常使用它。

我将一些培训材料升级到当前的时代，很少有东西比现在的。净竞技场比。NET 核心，因为 2.0 仍然是新鲜和温暖的烤箱，在我的制造，有意义的去与它。

我出发去安装*。NET Core SDK 2.0* ( *SDK2* 从现在开始)在我 2010 年年中的 iMac 上，当我发现 Yosemite 不被支持，因此，人们不能在 Mac 上比 Sierra 旧的任何东西上安装 *SDK2* 。

碰巧的是，我运行 Yosemite 是因为任何比 10.10 更新的东西都会让我的老电脑爬行，让我想杀死等待的人。相信我，我尝试了新版本，不得不忍受回到以前的 MacOS 版本的痛苦，因为谋杀仍然不酷。因此，约塞米蒂将会一直存在下去，直到电脑要么屈服，要么被卖掉。

## 展望未来

我会折起来跑到我的笔记本电脑前，安装上 *SDK2* 然后带着它承认失败吗？见鬼不要。

我做了一个简单的观察: *SDK2* 就是“只是”一堆命令行 utils。

我脑子里有了这样的想法:有没有一种简单且性能足够好的方法，可以在我不支持的机器之外运行这些实用程序，同时仍然在本地编辑文件？

给你个提示:我选择的答案涉及到思考一个*盒子*里面的**。**

### 加入热潮

我说的*盒子*指的是容器。

[安装 *SDK2* 的方法之一](https://www.microsoft.com/net/core#dockercmd)并不涉及在你的系统中“安装”它。你可以运行一个内置了 *SDK2* 的“东西”,这样你就可以打开它的外壳，并且仍然能够在你的机器上使用你最喜欢的编辑器时访问你的文件系统。那个“东西”是一个容器。

通过在你的机器上运行 *Docker* ，你可以下载一个带有 *SDK2* (或者，基本上，无论什么)的图像，并在一个远程 shell 中运行`dotnet ...`命令，绕过底层系统，我的老古董 iMac，不支持这样的软件。

以及所有这一切都不涉及千兆字节大小的虚拟机和虚拟硬盘。

什么是不喜欢？

#### 露出(命令)线

首先要做的是从 [Docker Hub](https://hub.docker.com/) 中提取正确的图像。从可用的[网络图片](https://hub.docker.com/r/microsoft/dotnet/)的财富来看，由于我们正在构建软件，我们需要一个标记为**的 sdk** 和一个更新的 *stretch* Linux，因为我真的不需要瞄准经典。NET 框架。

出于我自己的原因，我喜欢给图像加上一个较短的名称标签，以便在后续的命令中使用短名称。

```
> docker pull microsoft/dotnet:2.0-sdk
# tag pulled image for easier usage
> docker tag microsoft/dotnet:2.0-sdk sdk2 
```

提取图像需要一些时间，这取决于您的连接速度和计算能力。

有了系统中的映像，我们需要运行基于它的容器，与终端交互。如果这样的命令是从项目文件夹中运行的，那么这样的文件夹将从运行的容器中挂载并可用。

容器也是为了方便而命名。

```
> cd {project-folder}
> docker run -ti --name sdk2 --mount type=bind,source="$(pwd)",target=/root/{project-folder} sdk2
# this shell is in the running container
root@container-id# 
```

一旦进入终端，`dotnet`命令就可以愉快地运行了。运行完命令后，可以用`exit`结束会话，这将关闭容器终端，停止容器，让我们回到 shell。

当需要运行更多命令时，只需在启动后附加到容器上即可:

```
# close container session (container will stop)
root@container-id# exit
...
# reconnect to the stopped container
> $ docker start -a sdk2 
```

少了一个拥抱命令行的借口。