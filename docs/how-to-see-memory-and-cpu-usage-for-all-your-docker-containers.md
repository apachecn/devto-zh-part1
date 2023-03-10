# 如何查看所有 Docker 容器的内存和 CPU 使用情况(在 CentOS 6 上)

> 原文：<https://dev.to/rubberduck/how-to-see-memory-and-cpu-usage-for-all-your-docker-containers>

[*最初发布在我的博客上。*T3】](https://christopherjmcclellan.wordpress.com/2017/07/22/docker-container-memory-usage/)

我在一台内存有限的 CentOS 6 服务器上运行许多 Docker 容器。(我最近才把它从 0.5 提升到 1。)在我将另一个容器上线之前，我喜欢检查一下我还有多少空间。由于 Docker 的最新版本不适用于 CentOS 6，我运行的是一个旧版本，1.7 左右。在 Docker 的新版本中，运行`docker stats`将返回所有正在运行的容器的统计数据，但是在旧版本中，您必须向`docker stats`传递一个容器 id。这里有一个快速的一行程序，它显示了旧版本的所有正在运行的容器的统计信息。

```
$ docker ps -q | xargs  docker stats --no-stream
CONTAINER           CPU %               MEM USAGE/LIMIT     MEM %               NET I/O
31636c70b372        0.07%               130.8 MB/1.041 GB   12.57%              269.7 kB/262.8 kB
8d184dfbeeaf        0.00%               112.8 MB/1.041 GB   10.84%              45.24 MB/32.66 MB
a63b24fe6099        0.45%               50.09 MB/1.041 GB   4.81%               1.279 GB/1.947 GB
fd1339522e04        0.01%               108.2 MB/1.041 GB   10.40%              8.262 MB/23.36 MB 
```

Enter fullscreen mode Exit fullscreen mode

`docker ps -q`返回正在运行的容器 id 列表，然后我们通过管道将它传递到`xargs`和`docker stats`。添加`--no-stream`给我们的只是第一个结果，而不是不断更新统计数据，但是没有它也能很好地工作。

这是一个巧妙的小把戏。如果有人知道如何返回容器名称而不是 id，请在下面评论。

同样，这对于最新版本是不必要的。只需运行`docker stats`，您将获得几乎相同的输出。

~永远的认知