# 十大 Docker 日志记录陷阱

> 原文：<https://dev.to/sematext/top-10-docker-logging-gotchas-1mlk>

[![top 10 docker logging gotchas](img/f00100683093d639224ab243aa60d469.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OxNZdF8Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://sematext.com/wp-content/uploads/2018/01/top-10-docker-logging-gotchas.jpg)

Docker 不仅改变了应用程序的部署方式，还改变了日志管理的工作流程。容器将日志写到控制台(stdout/stderr)而不是文件，Docker 日志驱动程序将日志转发到目的地。针对 Docker [Github issues](https://github.com/moby/moby/labels/area%2Flogging) 的检查很快显示，用户在处理 Docker 日志时存在各种问题。用 Docker 管理日志似乎很棘手，需要更深入地了解 Docker 日志驱动程序的实现和替代方案，以解决人们报告的问题。

[![](img/30d3ac347c43cdf9f2506b0418bbd7ae.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QkpkIsGv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://sematext.com/wp-content/uploads/2018/06/docker.svg) 在容器中执行命令，Docker 网络，数据清理等等…

[Docker 命令备忘单下载](https://sematext.com/docker-commands-cheat-sheet/?utm_medium=blogpost&utm_source=devto&utm_campaign=top-10-docker-logging-gotchas&utm_content=blog-docker-commands-cheatsheet)

### **那么每个 Docker 用户都应该知道的 10 大 Docker 日志陷阱是什么呢？**

首先，让我们从 Docker 日志记录驱动程序和选项的概述开始，这些驱动程序和选项将日志传送到集中式日志管理解决方案，如 Elastic Stack(以前的 ELK Stack)或 [Sematext Cloud](https://sematext.com/cloud/) 。

[![top 10 docker logging gatchas sematext](img/bb5b37fcc66f4b84c09aa46bfb2c1074.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8AwvesWp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://sematext.com/wp-content/uploads/2018/01/top-10-docker-logging-gatchas-sematext.png)

在 Docker 的早期，容器日志只能通过 Docker remote API 获得，即通过“docker logs”命令和一些高级日志运送程序。后来，Docker 引入了日志驱动作为插件，以开放 Docker 与各种日志管理工具的集成。这些日志驱动程序是作为 docker 守护进程中的二进制插件实现的。最近，插件架构得到了扩展，将日志驱动作为外部进程运行，可以注册为插件并通过 Unix 套接字检索日志。目前，docker 二进制文件附带的日志驱动程序是二进制插件，但这可能会在不久的将来改变。

Docker 日志记录驱动程序接收容器日志，并将它们转发到远程目的地或文件。默认的日志驱动程序是“json-file”。它在本地磁盘上以 JSON 格式存储容器日志。Docker 有一个日志驱动的插件架构，所以有开源工具和商业工具的插件:

*   日志–在系统日志中存储容器日志
*   系统日志驱动程序–支持 UDP、TCP 和 TLS
*   Fluentd–支持 TCP 或 Unix 套接字连接到 fluentd
*   Splunk–HTTP/HTTPS 转发到 Splunk 服务器
*   gelf–UDP 日志转发到 Graylog2

对于完整的日志管理解决方案，还需要其他工具:

*   用于构造日志的日志解析器，通常是日志传送程序(fluentd、rsyslog、logstash、logagent 等)的一部分
*   日志索引、可视化和警报:
    *   Elasticsearch 和 Kibana(弹性堆栈，也称为 ELK 堆栈)
    *   Splunk
    *   日志条目
    *   Loggl
    *   Sumologic
    *   Graylog 我们/企业版
    *   语义云/企业
    *   还有更多…

要将日志传送到一个后端，您可能需要选择一个支持您所选择的日志管理解决方案的日志驱动程序或日志工具。如果您的工具需要 Syslog 输入，您可以选择 Syslog 驱动程序。

### 让我们来看看每个 Docker 用户都应该知道的 10 大 Docker 日志记录陷阱。

#### **1。Docker logs 命令仅适用于 json 文件日志驱动程序**

默认的日志记录驱动程序“json-file”将日志写入本地磁盘，json-file 驱动程序是唯一一个与“docker logs”命令并行工作的驱动程序。一旦使用其他日志记录驱动程序，如 Syslog、Gelf 或 Splunk，Docker logs API 调用就开始失败,“Docker logs”命令会显示一个错误，报告限制，而不是在控制台上显示日志。在这种情况下，不仅 docker log 命令会失败，许多使用 docker 日志 API 的其他工具，如 Portainer 之类的 Docker 用户界面或 Logspout 之类的日志收集容器，也无法显示容器日志。

参见[https://github.com/moby/moby/issues/30887](https://github.com/moby/moby/issues/30887)

#### **2。Docker Syslog 驱动程序可以阻止容器部署**

将 Docker Syslog 驱动程序与 TCP 或 TLS 一起使用是一种可靠的交付日志的方式。但是，当容器启动时，系统日志记录驱动程序需要与系统日志服务器建立 TCP 连接。如果在容器启动时无法建立此连接，容器启动将失败，并显示如下错误消息

docker:来自守护程序的错误响应:无法初始化日志记录驱动程序:拨号 tcp

这意味着临时网络问题或高网络延迟可能会阻碍容器的部署。此外，Syslog 服务器的重新启动可能会导致所有通过 TCP/TS 记录到中央 Syslog 服务器的容器崩溃，这是绝对应该避免的情况。

参见:[https://github.com/docker/docker/issues/21966](https://github.com/docker/docker/issues/21966)

#### **3。当目的地关闭时，Docker 系统日志驱动程序会丢失日志**

与上面的问题#2 类似，导致日志丢失的原因是 Docker 日志记录驱动程序无法在日志无法传送到远程目的地时在磁盘上缓冲日志。

一个有趣的问题值得关注:[https://github.com/moby/moby/issues/30979](https://github.com/moby/moby/issues/30979)

#### **4。Docker 日志驱动不支持多行日志，如错误堆栈跟踪**

当我们想到日志时，大多数人会想到简单的单行日志，比如 Nginx 或 Apache 日志。但是，日志也可以跨多行。例如，异常跟踪通常跨越多行，所以为了帮助 Logstash 用户，我们分享了如何用 Logstash 处理堆栈跟踪。在容器的世界里，事情并没有变得更好，事情变得更加复杂，因为来自容器中运行的所有应用程序的日志被发送到同一个输出——stdout。难怪看到[问题#22920](https://github.com/moby/moby/issues/22920) 以“关闭。不在乎。”让那么多人失望。幸运的是，有像 Sematext [Docker Agent](https://github.com/sematext/sematext-agent-docker) 这样的工具可以开箱即用地解析多行日志，并应用定制的多行模式。

#### **5。Docker 服务日志命令因非 json 日志驱动程序而挂起**

虽然 json-files 驱动程序看起来很健壮，但不幸的是，其他日志驱动程序仍然会给 Docker Swarm 模式带来麻烦。

参见 Github 问题:[https://github.com/docker/docker/issues/28793](https://github.com/docker/docker/issues/28793)

#### **6。如果 fluentd 守护程序不存在且缓冲区已满，Docker 守护程序会崩溃**

另一种情况是，当远程目的地不可达时，日志驱动程序会带来麻烦——在这种[特殊情况下](https://github.com/moby/moby/issues/32567),日志驱动程序会抛出异常，导致 Docker 守护进程崩溃。

#### **7。Splunk 驱动程序故障时 Docker 容器卡在已创建状态**

如果 Splunk 服务器在容器启动时返回 504，则容器实际上已启动，但 docker 报告容器启动失败。一旦进入这种状态，容器就不再出现在 docker ps 下，并且不能用 docker kill 停止容器进程。停止该进程的唯一方法是手动终止它。

github:[https://github.com/moby/moby/issues/24376](https://github.com/moby/moby/issues/24376)

#### **8。Docker 日志跳过/丢失应用程序日志(日志驱动程序)**

原来，这个问题是由 journalid 速率限制引起的，当 Docker 为所有正在运行的应用程序创建日志时，需要增加速率限制，并且 journalid 可能会由于其速率限制设置而跳过一些日志。所以当你连接 Docker 的时候要注意你的日志设置。

#### **9。Gelf 驱动发布**

Gelf 日志记录驱动程序缺少 TCP 或 TLS 选项，并且只支持 UDP，当 UDP 数据包被丢弃时，可能会有丢失日志消息的风险。一些问题报告了 GELF 驱动程序的 DNS 解析/缓存问题，因此当您的 Graylog 服务器 IP 发生变化时，您的日志可能会被发送到“Nirvana ”,这可能会在使用容器部署时快速发生。

#### **10。Docker 不支持多个日志驱动**

将日志存储在本地服务器上，并有可能将它们发送到远程服务器，这将是一件好事。目前，Docker 不支持多个日志驱动，所以用户被迫选择一个日志驱动。知道这篇文章中列出的各种问题，这不是一个容易的决定。

### **就是这样！我的十大 Docker 日志捕获！**

### 接下来是什么？

您应该考虑到[不仅收集日志，还收集主机和容器指标，以及事件](https://sematext.com/docker)。

帖子[的十大 Docker 日志](https://sematext.com/blog/top-10-docker-logging-gotchas/)首先出现在[的语义](https://sematext.com)上。

* * *

 [![Kubernetes Cheat Sheet](img/013cd5102fe572e01d24d71e957a2cd7.png "The Essential Kubernetes Cheat Sheet")

T6】](https://sematext.com/kubernetes/cheatsheet/)

#### [你开始用 Kubernetes 了吗？](https://sematext.com/kubernetes-cheat-sheet/)

我们已经准备了一个 Kubernetes 备忘单，它将所有关键的 Kubernetes 命令(想想 kubectl)放在你的指尖。从资源管理(例如，创建或列出 pod、服务、守护程序)、查看和查找资源到监控和日志记录，按逻辑分组进行组织。 [**下载你的**](https://sematext.com/kubernetes-cheat-sheet/) 。

* * *