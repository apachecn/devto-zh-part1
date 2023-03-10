# 移动到 OpenShift！

> 原文：<https://dev.to/matteojoliveau/moving-to-openshift-apo>

# 我终于成功了！

[![Moving to OpenShift!](img/9b41437b983a51c39b64086da0dc707f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kOGvIkWB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://matteojoliveau.com/conteimg/2017/08/openshift.png)

自从一个同事在工作中向我展示了我们的安装，我就梦想在我的服务器上安装 [OpenShift Origin](https://www.openshift.org/) 。让我开发的应用程序的构建、部署、编排和扩展自动化的想法对我来说太棒了。

**但是**，大问题出现了。

## 旧服务器

有一天，我和几个朋友一起租了一台服务器。厌倦了又小又破的 VPS，我们从 [Hetzner](https://www.hetzner.de/) 租了一个巨大的**专用服务器**，类似于**四核至强处理器**和 **32 GB 内存**。这一切都很棒，我们运行了一个 [Docker](https://www.docker.com/) 引擎来运行我们的应用程序、数据库和容器内的东西，并在它们前面运行了一个 [NGINX](https://nginx.org/en/) 反向代理来路由来自外部世界的流量。
故事结束。

然后我们意识到我们需要将近一半的计算能力，每月支付 40 欧元的专用服务器对一些玩系统管理员游戏的人来说似乎有点太多了。

所以我们决定将我们的服务器从 [Contabo](https://nginx.org/en/) (仍然是一台非常合理的机器，有 6 个内核，24 GB 的 RAM 和高达 600 GB 的 SSD 存储)**转移到一个便宜得多的 VPS 上，这让我可以安装我期待已久的 OpenShift 平台:D
T5【让我告诉你，安装是一件非常痛苦的事情。首先， [Ansible installer](https://github.com/openshift/openshift-ansible) 没有成功完成它的工作，但由于它似乎是安装程序本身的一个错误，使用一个更新的补丁似乎可以修复它。

然后集成的 Docker 注册表和 HAProxy 路由器无法部署，因为我没有将节点(也就是主节点，也就是我的集群中唯一的服务器)标记为“基础架构”。

然后 Docker 注册表无法写入其内部目录，挂载为 NFS 共享，因为一些错误的 POSIX 权限。**

## 但是现在一切都完成了

如果你在不久的将来阅读这篇文章，并且我们还没有再次迁移，那么**这篇博客实际上是作为 docker 容器中的 OpenShift 服务**运行的，使用另一个拥有 MariaDB 引擎的服务作为存储后端。

[![Moving to OpenShift!](img/994329de03db96caec34cdb0c4c87dd6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--i9nrLp70--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://matteojoliveau.com/conteimg/2017/08/ghost-pod.png)

如果需要(也不会需要)，我可以将这个容器扩展**上百次**,我将能够处理高峰时段华盛顿邮报和维基百科的总流量。

或者也可能不是，我想副总裁们会在博客之前死去。

但仍然，你得到了主意。

我也在平台上运行一些有用的服务，比如一个私有的 [Gogs](https://gogs.io) 仓库服务器和一个用于我的 [Maven](https://maven.apache.org/) 项目的 [Sonatype Nexus](http://www.sonatype.org/nexus/) 仓库管理器。

我的朋友在过去开发了一些 [Telegram](https://telegram.org) 机器人，OpenShift 让他能够简单地将新代码推送到 Git，以立即部署和激活更改。(顺便说一句，去看看他那令人讨厌的[标签机器人](https://t.me/tagalertbot)，如果你用电报的话，这是一个巨大的进步)

## open shift 万岁

所以我对这次部署很满意。我们需要的 99%的东西运行起来没有问题，还有 1%的东西没有某种变通办法，我们可以试着让它工作。所以耶，欢迎来到 PaaS 世界！