# Docker Hack:删除那些不需要的东西。

> 原文：<https://dev.to/cyberomin/docker-hack-prune-those-unwanted-stuff>

很长时间以来我一直想这么做，今天，我克服了惰性。希望这是一个永无止境的系列中的第一个，我已经恰当地命名为 bits。这个系列的目的是分享我的发现、技巧和使用日常工具的捷径。坐好，放松，享受旅程。

在 bit 系列的第一集中，我将分享一个 Docker hack。Docker 是一款令人惊叹的软件，就像其他工具一样，它有自己的方式，作为工程师，我们必须学习这些方式。这篇文章并不是要讨论 Docker 是什么和不是什么，这里的目的是展示我本周发现的一个小特性。

当我们到处旋转容器、图像和卷时，我们冒着很快填满硬盘的风险。幸运的是，在 17.03.1 版本中，Docker 为我们提供了一个叫做`prune`的小巧的命令。这是它的工作原理。

#### 用法

`docker system prune [OPTIONS]` -删除未使用的数据

#### 选项

`--all, -a` -移除所有未使用的图像，包括悬挂的图像。

`--force, -f` -不提示确认*照做*。

上面的命令清除了从容器到映像以及包括卷在内的所有内容。虽然这可能并不总是您想要的，但 Docker 为我们提供了一个更细粒度的`prune`命令版本:容器、映像和卷修剪。

`docker container prune [OPTIONS]` -移除所有停止的容器`docker image prune [OPTIONS]` -移除所有未使用的映像`docker volume prune [OPTIONS]` -移除所有未使用的卷

对于以上所有三个命令，唯一可用的选项是`--force, -f`。`--force, -f`简单地要求 Docker 在没有确认的情况下继续。

我最近运行了`docker system prune -a -f`，这是我恢复了将近 25GB 的硬盘空间。

PS:小心使用这些命令。

*本帖原载于[cyber omin . github . io](http://cyberomin.github.io/bits/docker/2017/04/01/docker-hacks.html)T3】*