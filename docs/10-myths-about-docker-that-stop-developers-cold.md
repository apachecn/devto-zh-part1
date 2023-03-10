# Docker 的 10 个误区让开发者望而却步

> 原文：<https://dev.to/derickbailey/10-myths-about-docker-that-stop-developers-cold>

我在讨论 Docker 的成长，我不断听到一些在我看来不太正确的信息。

> *“Docker 天生就更有进取心”*
> 
> *“它只是暂时在 OSx 上工作，几乎不能在 Windows 上工作”*
> 
> *“我不确定自己能否顺利地在本地运行它”*
> 
> *…还有更多*

这些陈述中有一点点真实(例如，见下面的第 3 点和第 5 点)，但是这一点点真实往往很容易忽略什么是不真实的，或者不再真实。

还有一些文章，除了说些行话，需要大量的框架，还讨论了如何用 3 万个容器管理每秒 10 万亿个请求，自动化托管在 600 个基于云的服务器实例中的 5000 个微服务…

很容易理解为什么 Docker 有一个宏大的神话围绕着它。

[![](img/d562ff1ac374e0afc6ced825db041e60.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yGm5v9_X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://derickbailey.com/wp-content/uploads/2015/10/hidden-variable.jpeg)

不幸的是，神话和错误信息仍然存在。除了阻止开发者尝试 Docker，他们很少做更多的事情。

所以，让我们来看看最常见的神话——有些是我见过的，有些是我以前相信过的——并试图从中找到真相，以及解决方案(如果有的话)。

## 误区十:我不能*和 Docker 发展*…

### 因为我不能编辑 Dockerfile

作为一名开发人员，我在工作时对工具和环境配置有特定的需求。我也被告知(理应如此)我不能编辑生产 Dockerfile 文件来添加我需要的东西。

生产 Docker 映像应仅出于生产目的进行配置。

那么，我如何使用 Docker 来处理我的开发需求呢？如果我不能编辑 Docker 文件来添加我的工具和配置，我该如何在 Docker 中开发应用程序呢？

我可以将生产 docker 文件复制粘贴到我自己的 docker 文件中，然后根据需要修改该文件。但是，我们都知道复制是万恶之源。我们都知道复制是万恶之源。因为复制是万恶之源。

**解决方案**

与其复制 Docker 文件并可能导致更多问题，不如使用 Docker 模型从图像构建图像。

我已经在像“node:6”这样的基础上构建了我的生产应用程序映像。那么，为什么不创建一个“dev.dockerfile ”,并让它以我的应用程序的生产映像为基础进行构建呢？

### Dockerfile

```
FROM node:6

# ... production configuration 
```

Enter fullscreen mode Exit fullscreen mode

### 生产构建

```
$ docker build -t myapp . 
```

Enter fullscreen mode Exit fullscreen mode

### 开发文档文件

```
FROM myapp

# ... development configuration 
```

Enter fullscreen mode Exit fullscreen mode

### 开发构建

```
$ docker build -t myapp:dev -f dev.dockerfile . 
```

Enter fullscreen mode Exit fullscreen mode

现在我可以修改 dev.dockerfile 文件来满足我的开发需求，因为我知道它将使用生产映像中的确切配置。

**想看看开发人员的形象吗？**

查看关于[创建开发容器](https://sub.watchmecode.net/episode/creating-dev-container/)的 WatchMeCode 章节，这是在 Docker 中构建 Node.js 应用的[指南的一部分。](https://sub.watchmecode.net/guides/build-node-apps-in-docker/)

## 误解 9:我看不到这个容器里的任何东西

### 因为我根本看不到我的容器里面！

Docker 是[应用虚拟化](https://derickbailey.com/2016/08/29/so-youre-saying-docker-isnt-a-virtual-machine/)(容器化)，而不是用于一般计算目的的完整虚拟机。

但是开发人员通常需要将容器视为虚拟机。

我需要获取日志(除了我的应用程序的简单控制台输出)，检查调试输出，并确保我放入容器的文件和系统更改满足了我的所有需求。

但是，如果容器不是虚拟机，我怎么知道发生了什么？我如何在容器中看到文件、环境变量和我需要的其他部分？

**解决方案**

虽然 Docker 容器在技术上可能不是一个完整的虚拟机，但它确实在幕后运行 Linux 发行版。

是的，这个发行版可能是一个精简的最小发行版，比如 Alpine Linux，但是它仍然有基本的 shell 访问权限。将 Linux 发行版作为容器的基础给了我深入容器的选择。

根据具体情况，有两种基本方法可以做到这一点。

**方法 1:将外壳装入运行容器**

如果我已经启动并运行了一个容器，我可以使用“docker exec”命令进入该容器，并拥有完全的 shell 访问权限。

```
$ docker exec -it mycontainer /bin/sh 
```

Enter fullscreen mode Exit fullscreen mode

一旦我做到了这一点，我就在容器中了，就好像我被放入了任何 Linux 发行版中。

**方法 2:按照容器的命令运行 Shell**

如果我没有启动并运行一个容器——也不能让它运行——我可以从一个映像运行一个新的容器，使用 Linux shell 作为启动命令。

```
$ docker run -it myapp /bin/sh 
```

Enter fullscreen mode Exit fullscreen mode

现在我有了一个新的容器，可以用 shell 运行，让我可以轻松地四处查看。

**想看贝壳运动吗？**

从 WatchMeCode 的[学习 Docker 的指南](https://sub.watchmecode.net/guides/learn-docker/)和[在 Docker 中构建 Node.js 应用的指南](https://sub.watchmecode.net/guides/build-node-apps-in-docker/)中查看这两集。

*   [容器中的 Docker Exec 命令](https://sub.watchmecode.net/episode/docker-exec/)
*   [使用附加的调试器启动应用程序](https://sub.watchmecode.net/episode/start-app-w-debugger/)

## 误区 8:我必须在 Docker 容器内部编码？

### 而我不能用我喜欢的编辑器？！

当我第一次看到 Docker 容器，运行我的 Node.js 代码时，我对其可能性感到兴奋。

但是这种兴奋很快就消失了，因为我想知道在构建一个图像之后，应该如何将编辑过的代码移动到容器中。

我应该每次都重建图像吗？那会非常慢…而且不是一个真正的选择。

好的，我应该把外壳放入容器中用 vim 编辑代码吗？

那行得通。

但是，如果我想使用一个更好的 IDE /编辑器，我就不能。我必须一直使用类似 vim 的东西(这不是我喜欢的 vim 版本)。

如果我只能通过命令行/ shell 访问我的容器，我该如何使用我最喜欢的编辑器？

**解决方案**

Docker 允许我使用“卷挂载选项”将文件夹从主机系统挂载到目标容器中。

```
$ docker run -v /dev/my-app:/var/app myapp 
```

Enter fullscreen mode Exit fullscreen mode

这样，容器的“/var/app”文件夹将指向本地的“/dev/my-app”文件夹。在“/dev/my-app”中编辑代码——当然是用我最喜欢的编辑器——会改变容器看到和使用的代码。

**想看看在已装载的宗卷中进行编辑的效果吗？**

查看关于在容器中编辑代码[的 WatchMeCode 章节——这是在 Docker](https://sub.watchmecode.net/episode/edit-code-container/) 中构建 Node.js 应用的[指南的一部分。](https://sub.watchmecode.net/guides/build-node-apps-in-docker/)

## 误解 7:我必须使用命令行调试器…

### 和我*非常喜欢我 IDE 的调试器*

 *有了编辑代码并将其反映在容器中的能力，再加上封装到容器中的能力，调试代码只需一步之遥。

我只需要在编辑完相关代码后，在容器中运行调试器，对吗？

虽然这肯定是真的——我可以从 Docker 容器内部使用我的编程语言的命令行调试器——但这不是唯一的选择。

那么，怎么可能在我最喜欢的 IDE /编辑器中使用调试器，代码在一个容器中呢？

**解决方案**

简单的回答就是“远程调试”。

然而，长的答案很大程度上取决于使用哪种语言和运行时进行开发。

例如，使用 Node.js，我可以通过 TCP/IP 端口(5858)进行远程调试。为了通过 Docker 容器进行调试，我只需要从我的 Docker 映像(当然是“dev.dockerfile 映像”)中公开那个端口。

```
# ...

EXPOSE 5858

# ... 
```

Enter fullscreen mode Exit fullscreen mode

有了这个端口，我就可以使用 shell 进入容器，并在附加我最喜欢的调试器之前使用任何启动 Node.js 调试服务的典型方法。

**想看 Visual Studio 代码调试一个 Node.js 容器？**

查看关于在具有 Visual Studio 代码的容器中进行[调试的 WatchMeCode 章节——这是在 Docker](https://sub.watchmecode.net/episode/debug-container-vs-code/) 中构建 Node.js 应用的[指南的一部分。](https://sub.watchmecode.net/guides/build-node-apps-in-docker/)

## 误区六:我每次都得“码头工人跑”

### 我不记得所有那些“docker 运行选项…

毫无疑问，Docker 有大量的命令行选项。浏览 Docker 的帮助页面就像阅读一部来自灭绝文明的古代神话巨著。

到了“运行容器”的时候，毫不奇怪，我经常感到困惑或彻底的沮丧，从来没有第一次就做出正确的选择。

此外，每次调用“docker run”都会从映像创建一个新的容器实例。

如果我需要一个新的容器，这很好。

然而，如果我想运行我以前创建的容器，我不会喜欢“docker run”的结果…这是另一个新的容器实例。

**解决方案**

我不需要“每当我需要一个新容器时，docker 就运行一个新容器。

相反，我可以“停止”和“启动”有问题的容器。

这样做可以让我的容器像预期的那样停止和启动。

这也在两次运行之间保持了容器的状态，意味着我将能够从它停止的地方重新启动容器。如果我修改了容器中的任何文件，当容器再次启动时，这些更改将保持不变。

**想看看行动中的开始和停止吗？**

WatchMeCode 的[指南学习 Docker](https://sub.watchmecode.net/guides/learn-docker/) 和[指南在 Docker](https://sub.watchmecode.net/guides/build-node-apps-in-docker/) 中构建 Node.js 应用有很多集使用了这种技术。

但是，如果您对这个想法还不熟悉，我推荐您观看关于[基本映像和容器管理](https://sub.watchmecode.net/episode/basic-image-and-container-management/)的那一集，它涵盖了停止和重新启动单个容器实例。

## 神话 5: Docker 几乎不能在 macOS 和 Windows 上工作

### *我用的是*一台 Mac / Windows

直到几个月前，这在很大程度上还是真的。

过去，Mac 和 Windows 上的 Docker 需要使用一个完整的虚拟机，该虚拟机具有一个“docker-machine”实用程序和一个附加软件层，用于将工作代理进/出虚拟机。

它起作用了…但是它引入了巨大的开销，同时限制(或排除)了某些特性。

**解决方案**

幸运的是，Docker 理解主机操作系统不仅仅需要支持 Linux。

2016 年下半年，Docker 发布了官方的 *Docker for Mac* 和 *Docker for Windows* 软件包。

这使得在这两种操作系统上安装和使用 Docker 变得非常简单。通过定期更新，其特性和功能也几乎与 Linux 变种不相上下。几乎没有区别了，我不记得上次我需要这些版本中没有的选项或功能是什么时候了。

**想给 Mac 或 Windows 安装 Docker？**

WatchMeCode 为这两个版本(以及 Ubuntu Linux)提供了免费的安装剧集。)

*   [安装 Mac Docker](https://sub.watchmecode.net/episode/install-docker-for-mac/)
*   [为 Windows 安装 Docker](https://sub.watchmecode.net/episode/install-docker-for-windows/)
*   [在 Ubuntu Linux 上安装 Docker](https://sub.watchmecode.net/episode/install-docker-on-ubuntu/)

## 误解 4: Docker 只是命令行

### 我和*显著地*用视觉工具更有效率

由于它的诞生地是 Linux，Docker 更喜欢命令行工具也就不足为奇了。

然而，大量的命令和选项可能会让人不知所措。对于不经常在控制台/终端窗口上花费时间的开发人员来说，这可能是一个挫折和生产力损失的来源。

**解决方案**

随着 Docker 周围社区的成长，有越来越多的工具符合越来越多开发者的偏好——包括可视化工具。

Docker for Mac 和 Windows 包括与 Kitematic 的基本集成，例如——在我的机器上管理 Docker 图像和容器的 GUI。

使用 Kitematic，可以很容易地在 Docker 存储库中搜索图像，创建容器，并管理我已安装和正在运行的容器的各种选项。

想看看 Kitematic 的实际应用吗？

查看 WatchMeCode 的[学习 Docker](https://sub.watchmecode.net/guides/learn-docker/) 指南中的 Kitematic 插曲。

## 误解 3:我不能在容器中运行我的数据库。

### 无法正常缩放……*而且我会丢失数据！*

容器注定是短暂的——它们应该在需要时被销毁和重新创建，不能有片刻的犹豫。但是如果我将数据库中的数据存储在我的容器中，删除容器将会删除我的数据。

此外，数据库系统有非常具体的扩展方法——纵向扩展(更大的服务器)和横向扩展(更多的服务器)。

Docker 似乎擅长于向外扩展——当需要更多处理能力时，创建更多事物的实例。而另一方面，大多数数据库系统都需要特定和专业的配置和维护才能向外扩展。

所以…是的…是真的。在 Docker 容器中运行*生产*数据库并不是一个好主意。

然而，我在 Docker 上的第一次成功是在一个数据库上。

确切地说是甲骨文。

出于开发需要，我曾尝试将 Oracle 安装到虚拟机中，但失败了。我花了将近 2 周的时间(断断续续地)来完成它，但从未接近成功。

然而，在得知 Docker 有一个 Oracle XE 映像后的 30 分钟内，我就让 Oracle 启动并运行了。

在我的*开发*环境中。

**解决方案**

Docker 可能不太适合在生产环境中运行数据库，但对于开发来说却是奇迹。

我运行 MongoDB、MySQL、Oracle、Redis 和其他数据/持久性系统已经有一段时间了，对此我非常高兴。

说到“Docker 容器的短暂本质”?卷安装。

与代码编辑神话一样，卷挂载提供了一种在本地系统上存储数据和在容器中使用数据的便捷方式。

现在，我可以销毁一个容器，并根据需要重新创建它，因为我知道我将从中断的地方重新开始。

## 误解 2:我不能在我的项目中使用 Docker

### 因为 Docker 是*全有全无*

当我第一次看到 Docker 时，我认为这是真的——你要么用 Docker 开发、调试、部署和“开发一切(以及 200 个额外的工具和框架，使其自动运行),要么你根本不使用 Docker。

我在安装和运行数据库方面的经验，也是我在 Docker 上的第一次成功，向我展示了另一种情况。

任何要求全有或全无的工具或技术都应该用极端的显微镜重新评估。这是真的，这很罕见。即便如此，时间和金钱也不应该投入其中。

**解决方案**

Docker 和大多数开发工具一样，可以一点一点地添加。

从小处着手。

在容器中运行开发数据库。

然后在 docker 容器中构建一个单独的库，并了解它是如何工作的。

之后，在容器中构建下一个微服务——只需要几行代码的微服务。

从那里开始，转移到一个更大的项目，多个团队成员在其中积极地开发。

没有必要孤注一掷。

## 误区 1:我根本不会从 Docker 中受益…

### 因为 Docker 是“企业”，而“devops”

当我第一次看到 Docker 时，这是我必须克服的最大的心理障碍。

在我看来，Docker 是一个只有最高级的团队才需要处理的大问题，而我永远不会看到它。

我这样想也不奇怪。

当我环顾博客世界和会议讨论中的喧嚣和炒作时，我只看到“大公司如何通过 Docker、Kubernetes 和闪亮的新网飞规模工具集自动化 10，000，000 个微服务”。

Docker 可能擅长于“企业”和“开发”，但是普通的开发人员——就像你和我——可以利用 Docker 所提供的优势。

**解决方案**

给 docker 一个尝试。

同样，从小处着手。

我运行一个 12GB 内存的虚拟机，为一个客户端托管 3 个 web 项目。退一步说，这是一台很差的服务器。但是我正在考虑 Docker——只是普通的老式 Docker 本身——作为一种更有效地使用服务器的方法。

我还有第二个客户——总共有 5 名兼职开发人员(每周总共花费不到 1 个全职人员的时间),他们已经在使用 Docker 来自动化他们的构建和部署过程。

目前，我用 Docker 为 Node.js 应用程序构建了大部分开源库。

我正在寻找新的更好的方法来管理我需要每天使用 Docker 安装在笔记本电脑上的软件和服务。

请记住…

## 不要相信炒作或神话

围绕 Docker 的神话是有充分理由存在的。

从历史上看，它很难在 Linux 之外使用。直到今天，这对于企业和 devops 工作来说都是一个巨大的好处。

但是，不幸的是，这个神话对最有可能受益的开发者——你——没有什么帮助。

如果你发现自己看着这个神话、真相和解决方案的列表，仍然说，“是的，但是……”，我请你花一些时间重新评估你对 Docker 的看法，以及为什么。

如果你对一个*开发*环境如何利用 Docker 还有疑问或担心，[可以联系](https://derickbailey.com/about/)。很想听听大家的问题，看看有什么我能帮忙的。

如果你想学习 Docker 的基础知识或如何在其中开发应用程序，但不知道从哪里开始，请查看 WatchMeCode 的[指南以学习 Docker](https://sub.watchmecode.net/guides/learn-docker/) (从头开始)和[指南以在 Docker](https://sub.watchmecode.net/guides/build-node-apps-in-docker/) 中构建 Node.js 应用程序。*