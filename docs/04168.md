# Docker:限制入站和出站网络流量

> 原文：<https://dev.to/andre/docker-restricting-in--and-outbound-network-traffic-67p>

想象一个场景，在你的依赖图中可能有一个很臭的模块。一种想做坏事的依赖——恶意软件。"但是我在运行时隔离了 Docker 容器中的所有东西！"你可能会说。事实上，当邪恶模块试图搞乱您的文件系统或其他与主机相关的方面时，这很有帮助。但是如果模块想要呼叫总部呢？"

我今天思考了这个问题，并想与你分享我的方法。在我直接进入修补程序之前，我创建了以下验收标准:

1.  容器应该接受来自和去往已知网络的入站和出站流量
2.  容器应该阻止进出所有其它网络的流量
3.  容器中的应用程序应该作为非特权用户运行

"特权用户是限制网络流量的必要条件."这是我的第一个想法，与第三个接受标准相冲突。咩！

在实现了一些测试探针之后，我采用了以下解决方案:

*   提供非特权用户和`ENTRYPOINT`脚本附带的基本映像
*   当容器启动时，`ENTRYPOINT`脚本被执行，定义 [iptables](http://www.netfilter.org/projects/iptables/index.html) 规则，然后作为配置的非特权用户启动给定的应用程序。

让我们来看看实际的解决方案。 **Dockerfile** :

```
FROM node:8-alpine

LABEL maintainer="AndrÃ© KÃ¶nig <andre.koenig@gmail.com>"

RUN apk add --update curl iptables sudo && \
    addgroup -S app && adduser -S -g app app

COPY entrypoint.sh /entrypoint.sh

ENTRYPOINT ["/entrypoint.sh", "--"] 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，非常简单:使用一个基本映像——在本例中是 Node.js 8 Alpine 映像，添加一个新用户组并创建一个新用户。第二部分将`entrypoint.sh`脚本复制到容器中，并将其定义为一个[入口点](https://docs.docker.com/engine/reference/builder/#entrypoint)。这里没什么特别的。那么让我们来定义实际的`entrypoint.sh` :

```
#!/usr/bin/env sh

#
# iptables configuration
#
# The following allows in- and outbound traffic
# within a certain `CIDR` (default: `192.168.0.0/24`),
# but blocks all other network traffic.
#
ACCEPT_CIDR=${ALLOWED_CIDR:-192.168.0.0/24}

iptables -A INPUT -s $ACCEPT_CIDR -j ACCEPT
iptables -A INPUT -j DROP
iptables -A OUTPUT -d $ACCEPT_CIDR -j ACCEPT
iptables -A OUTPUT -j DROP

#
# After configuring `iptables` as root, execute
# the passed command as the non-privileged `app` user.
#
sudo -u app sh -c "$@" 
```

Enter fullscreen mode Exit fullscreen mode

## 用法举例

在通过`docker build -t node-sandbox .`构建映像之后，让我们测试一下这个新的沙盒环境。

例如，为了测试是否真的没有出站流量，可以尝试:

```
docker run --privileged -it --rm node-sandbox "curl https://google.com" 
```

Enter fullscreen mode Exit fullscreen mode

`curl`应该在一段时间后放弃，你会看到一个`curl: (6) Could not resolve host: google.com`错误。

另一方面，与本地网络中的系统通信应该是可行的。因此，如果您生活在`192.168.0.0/24`网络中，并且您在`192.168.0.1`(可能是您的 wifi 路由器)上运行一些东西，您应该会在执行时看到一个响应:

```
docker run --privileged -it --rm node-sandbox "curl http://192.168.0.1" 
```

Enter fullscreen mode Exit fullscreen mode

细心的读者可能已经注意到，`entrypoint.sh`检查环境变量`ALLOWED_CIDR`是否被设置，并采用`CIDR`标记的网络而不是回退:

```
docker run --privileged -it --rm -e "ALLOWED_CIDR=10.0.0.0/8" node-sandbox "curl http://10.0.0.1" 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

我的一个客户不得不在一个基于 Node.js 的应用程序中反复转换高度敏感的用户数据。由于该组织内部的高安全标准，他们预期会有一个隔离的网络环境，在该环境中，任何可能的恶意依赖都无法向外部系统发送敏感信息。尽管应用程序不能在容器 orchestrator 中运行，而在容器 orchestrator 中，您可以对网络堆栈进行细粒度控制，但是在用户机器上，这似乎是一个非常有效的解决方案。如果你面临类似的情况，这种方法可能会帮助你，我很高兴阅读你的观点。