# 保护您的弹性搜索实例

> 原文：<https://dev.to/preslavrachev/securing-your-elasticsearch-instances>

*本文首发于[我的博客](https://preslav.me/2017/02/03/securing-your-elasticsearch-instances/)*

### 保护您的 ElasticSearch 实例，享受所有乐趣

通常，我们选择方便胜过安全。许多现代工具，如 MongoDB 和 ElasticSearch，越来越受欢迎，部分原因是它们易于安装和修改。只需剥离一个实例，将浏览器指向正确的 URL，就可以开始发送查询了。

不幸的是，事情总是接踵而至，正如[我们最近看到的](http://www.zdnet.com/article/elasticsearch-ransomware-attacks-now-number-in-the-thousands/)，公开的 ElasticSearch 可能是一个易受攻击的目标，就像 MongoDB 在其全盛时期一样。鉴于[最近对世界各地许多开放的 ElasticSearch 实例的攻击](http://www.zdnet.com/article/elasticsearch-ransomware-attacks-now-number-in-the-thousands/)，我决定分享一个关于如何设置远程 es 实例的快速技巧，并通过不损害其易用性来保持它们的安全。

## 第一部分:限制对你的 ElasticSearch 实例的访问

我们开始吧。设置 ElasticSearch 实例最简单的方法是从默认的 Docker 图像
中旋转出一个容器

```
docker pull docker.elastic.co/elasticsearch/elasticsearch:<VERSION>

docker run -p 9200:9200 -e "http.host=0.0.0.0" -e "transport.host=127.0.0.1" elasticsearch:<VERSION> 
```

Enter fullscreen mode Exit fullscreen mode

运行上面的行，将创建一个从容器内的 9200 到主机上的端口 9200 的部分映射。这里的一个问题是，这样做也将它暴露给了外部世界。通过对您的主机:
运行`iptables`很容易看出这一点

```
iptables -t nat -L -n

# Outputs
...
target     prot opt source               destination 
DNAT       tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:9200 to:XXX.XXX.XXX.XXX:9200
... 
```

Enter fullscreen mode Exit fullscreen mode

事实上，Docker 承担了配置您的`iptables`防火墙的重任，但通常，这可能会导致配置过于宽松。我们应该做的是为端口映射配置提供一个特定的 IP。谢天谢地，Docker 支持这一点，所以我们所要做的就是修改上面的命令，使用`IP:host_port:container_port`映射:

```
docker run -p 127.0.0.1:9200:9200 -e "http.host=0.0.0.0" -e "transport.host=127.0.0.1" elasticsearch:<VERSION> 
```

Enter fullscreen mode Exit fullscreen mode

完美！放置`127.0.0.1`将保证容器在主机内部可用，但在外部不可访问。再次查看 iptables 可以快速证明这一点:

```
target     prot opt source               destination 
DNAT       tcp  --  0.0.0.0/0            127.0.0.1            tcp dpt:9200 to:XX.XXX.XXX.XXX:9200 
```

Enter fullscreen mode Exit fullscreen mode

如果您将浏览器指向端口 9200，您应该看不到任何内容，但是从主机内部执行“curl 127.0.0.1:9200”应该可以。

## 第二部分:以安全的方式访问您的 ElasticSearch 实例

我们所做的一切都很好，但是现在如何访问我们的 ElasticSearch 实例，而不失去在 ES 上快速测试的灵活性呢？简单，用*NIX 的瑞士军刀- `SSH`。SSH 是大多数程序员日常使用的工具，但是很少有人知道 SSH 允许本地和远程端口转发。这意味着 SSH 可以在您的机器和您的服务器之间创建一个加密的隧道，这样您就可以访问远程运行的服务，就像它们运行在 loclahost(本地转发)上一样。还有远程转发，或者，允许您从远程服务器安全地访问本地运行的服务。

虽然在我们的例子中我们将使用本地端口转发，但两者是相似的:

 ``ssh -L/-R <PORT_ON_THE_LOCAL/REMOTE_MACHINE>:<HOST_TO_MAP_TO>:<PORT_ON_THE_REMOTE/LOCAL_MACHINE> <USERNAME>@<REMOTE_IP>`` 

 ``在我们的特殊情况下，这看起来像这样:

 ``ssh -L 9200:127.0.0.1:9200 user@XX.XXX.XXX.XXX`` 

 ``这基本上是说:将我的本地端口`9200`映射到对`XX.XXX.XXX.XXX`服务器上的`127.0.0.1:9200`的调用。当你将浏览器指向`http://localhost:9200`时，你应该会看到熟悉的 ElasticSearch 输出，尽管像以前一样`XX.XXX.XXX.XXX:9200`没有返回任何内容。您可以让上述命令在后台运行，并作为守护程序运行。

## 结论

为了继续享受使用 ElasticSearch 或 MongoDB 的自由，你只需要这两个步骤，但是要以完全安全的方式进行。这个方法可以应用于几乎所有的服务。而且你甚至真的不需要 Docker。事实上，我在第一部分提到它，是因为它使设置变得容易，也让你不必自己动手修补。

**注意:**请记住，虽然运行 SSH 隧道对于测试和开发目的来说是完美的，但对于生产来说可能不是最佳解决方案。其原因是加密/解密数据并通过隧道对其进行混洗所导致的延迟。当许多传入请求并行运行时，这可能会成为瓶颈。我还没有对这个设置进行压力测试，并将在以后的帖子中分享我的观察结果。我还将分享更多关于如何安全地访问 ElasticSearch 实例的想法，而且是以一种高效的方式。