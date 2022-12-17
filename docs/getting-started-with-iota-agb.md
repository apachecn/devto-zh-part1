# IOTA 入门

> 原文：<https://dev.to/brightdevs/getting-started-with-iota-agb>

在[之前的帖子](https://dev.to/blog/iota-new-kid-in-cryptocurrency-town/)中，我们先看了一眼[的分类账。尽管计划很棒，但目前该项目仍处于酝酿阶段，大多数最有吸引力的功能还没有准备好投入使用。但是这不应该阻止我们玩我们今天已经拥有的东西。](http://iota.org/)

在我们深入研究代码之前，我们需要连接到两个 IOTA 网络中的一个。“适当的”网络叫做`mainnet`，还有一个[稍微宽松一点的网络叫做`testnet`](https://blog.iota.org/the-iota-testnet-training-wheels-for-the-community-fd65dbdddb3b)。顾名思义，后者是为了测试目的。每个网络由社区运行的多个节点组成。为了加入网络，我们要么需要运行我们自己的节点，要么使用它的公共 API 连接到现有的节点。

## 节点是节点吗？

如果我们足够精通技术，并且我们已经决定成为 IOTA 网络的活跃部分，我们需要让我们的节点启动并运行。IOTA 的术语避免使用“节点”名称来表示他们现在提供的服务。唯一能够作为网络的一部分运行的软件被称为“IOTA 参考实施”(IRI)——我的猜测是，它旨在明确表明该项目仍处于起步阶段，一切都可能随着时间的推移而改变。

IRI 是用 Java 写的，在 GitHub 上作为 [GPL 许可的](https://github.com/iotaledger/iri/blob/dev/LICENSE)开源软件公开发布。运行一个节点需要一个完整的 Java JRE，这可能是 IOTA 在真正的物联网应用中尚不可行的原因之一。我们可以选择从提供的 [JAR 文件运行它，或者——这听起来更吸引我——使用](https://github.com/iotaledger/iri/releases) [Docker 镜像](https://hub.docker.com/r/iotaledger/iri/)。

## 我们出发吧

要构建并运行 Docker 映像，请键入以下内容:

```
docker run -d --name iota-node \
  -p 14265:14265 -p 14777:14777/udp -p 15777:15777 \
  -v ~/iota.ini:/iri/iota.ini iotaledger/iri:v1.4.0 
```

Enter fullscreen mode Exit fullscreen mode

我们正在创建一个名为`iota-node`的容器，以便于将来访问。使用`-p`选项，我们将为外部通信提供三个自定义网络端口。通过`-v`，我们传递一个路径到我们节点的配置文件，看起来应该是这样的:

```
[IRI]
PORT = 14265
UDP_RECEIVER_PORT = 14777
TCP_RECEIVER_PORT = 15777
NEIGHBORS = [space-separated list of neighbors]
IXI_DIR = ixi
HEADLESS = true
DEBUG = true
DB_PATH = mainnetdb 
```

Enter fullscreen mode Exit fullscreen mode

这里我们再次指定了通信端口。它们值得一个解释，因为它的目的和用法并不明显，官方文档也没有帮助。

第一个——神秘地命名为`PORT`——用于公开我们节点的 HTTP API。配置、编排和监控我们的节点是有用的，但它不一定要暴露在公共互联网上。我们应该让它在 Docker 容器内部对我们可用，但是没有必要在我们的外部防火墙上暴露它。

`UDP_RECEIVER_PORT`和`TCP_RECEIVER_PORT`是需要通过防火墙暴露的端口——所以确保你的路由器或 ISP 允许你通过这些端口配置公共流量以到达你的节点。默认情况下，IRI 使用带有 TCP 回退的 UDP 从技术上讲，只需要到达其中一个通道。UDP 增加的开销较少，但 TCP 在某些情况下更容易设置和调试。

## 与邻居和睦相处

我们现在有一个停滞不前的进程。是时候找到我们可以连接的其他节点，并与它同步 Tangle 的状态了。这些其他节点被称为“邻居”，我们需要在我们和邻居之间保持双向通道。截至今天，获得一些邻居的唯一方法是向 T2 IOTA 的 Slack 频道 T3 上的友好人士寻求进入的途径。当在欧洲人的白天被问到时，我在不到一个小时内得到了一些回答。

[![IOTA neighborhood](img/ef9081ae597d9a76a2d892f83589332f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0iVIFu2w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k0q0di4c4pgd0yirr1ij.jpg)

当你已经和其他节点所有者成为朋友时，你需要给他们你的主机的公开地址——很可能看起来像`udp://12.23.34.45:14777`。注意，我们在这里指定 UDP 协议，并使用 INI 文件中相应的接收端口。我们的邻居应该给我们他们的地址，也是 UDP 协议(如果我们决定使用 UDP，也就是-对于 TCP，显然双方都应该使用 TCP 协议和端口)。

要添加邻居，请将其地址作为空格分隔列表包含在您的`iota.ini`文件的`NEIGHBORS`字段中，然后重新启动您的节点。或者，您可以使用您的节点的 [HTTP API 方法](https://iota.readme.io/v1.2.0/reference)来动态添加一个邻居。

完成之后，试着调用 [`getNeighbors`](https://iota.readme.io/v1.2.0/reference#getneighborsactivity) 来看看发生了什么。如果一切顺利，所有交易的数量应该会快速增长。现在只要去吃一顿长长的午餐(可能包括一顿晚餐)，当你回来时，你将与网络同步，成为 IOTA 生态系统的一部分。万岁！

## 光节点

还有钱包客户端 app(也叫“轻节点”)，但用途仅限于代币交易。它需要一个到现有节点的 HTTP API 的连接(在我的例子中，这个通过在`PORT` - 14265 中定义的端口公开)。但我们是极客，比起交易，我们更关心技术🤓。

再次说到节点的 HTTP API。如果有，我们也可以自己直接用它，对吗？我们当然可以。请继续关注[系列的下一篇文章](https://dev.to/blog/iota-hello-world/)，我们将从一个节点向 IOTA 提交一个 Hello World 事务。JS 应用。

最初发布于 [brightinventions.pl](https://brightinventions.pl/blog/)

由亚当吧、网络佬@光明发明
[个人博客](https://whatwebcando.today/) [推特](https://twitter.com/NOtherDev) [邮箱](//adam.bar@brightinventions.pl) [Github](https://github.com/NOtherDev)