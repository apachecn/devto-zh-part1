# 从容器访问 Docker 主机

> 原文：<https://dev.to/adamkdean/access-docker-host-from-container-2id5>

Docker 1.0 刚刚发布，我期待的一件事是内省 API[Solomon Hykes 说它将在 1.0](https://github.com/dotcloud/docker/issues/1143#issuecomment-22354479) 中推出。不幸的是，看起来并没有。这是一个遗憾，因为它将允许容器以一种限定范围和受控的方式与主机通信。

对于我刚刚参与的一个小型 R&D 项目，我有一个“网关”容器，它可以接收 HTTP 流量并将其路由到其他容器。为此，它需要访问主机上的 docker，以便能够索引容器并访问它们的配置，例如本地 IP 地址。

我让它工作的方法是在容器上安装 docker，假设这是`ubuntu-docker`docker 文件:

```
FROM ubuntu

# Install Docker
RUN apt-get install docker.io -y
RUN ln -sf /usr/bin/docker.io /usr/local/bin/docker

CMD /bin/bash 
```

Enter fullscreen mode Exit fullscreen mode

一旦构建好了，我在运行容器时将`docker.sock`挂载到容器，如下所示:

```
docker run -d -v /var/run/docker.sock:/var/run/docker.sock ubuntu-docker 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果您要在容器上运行 docker 命令，您实际上是在主机上运行它们。请记住，这不是一个完整的操作方法，如果有人需要，我可以写一个完整的演练。