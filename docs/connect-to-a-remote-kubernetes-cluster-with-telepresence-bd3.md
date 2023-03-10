# 通过网真连接到远程 Kubernetes 集群

> 原文：<https://dev.to/datawireio/connect-to-a-remote-kubernetes-cluster-with-telepresence-bd3>

在本教程中，您将看到 [Telepresence](https://www.telepresence.io) 如何允许您从本地进程透明地访问远程集群。
这允许您在笔记本电脑上使用本地工具与集群内部的进程进行通信。

您应该从运行集群中的服务开始:

```
$ kubectl run myservice --image=datawire/hello-world --port=8000 --expose
$ kubectl get service myservice
NAME        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE myservice   10.0.0.12    <none>        8000/TCP   1m 
```

运行服务器的 pod 可能需要一两分钟才能启动并运行，这取决于您的群集的速度。

现在，您可以使用 Telepresence 运行一个可以访问该服务的本地流程，即使该流程是本地的，但该服务是在 Kubernetes 集群中运行的:

```
$ telepresence --run curl http://myservice:8000/
Hello, world! 
```

(如果 hello world pod 还没有启动，这将不起作用...如果是，请再试一次。)

发生了什么事:

1.  网真创建了一个新的`Deployment`，它运行一个代理。
2.  网真在本地运行，通过 T1 代理网络。
3.  由`curl`完成的 DNS 查找和 HTTP 请求通过代理进行路由，并透明地访问集群...即使`curl`在本地运行。
4.  当`curl`退出时，新的`Deployment`将被清理。

要了解更多关于网真代理的信息，您可以阅读相关的[参考文档](/reference/proxying.html)。

### 附加资源

如果你有兴趣自己尝试[网真](https://www.telepresence.io/)，你可以[用自制软件、apt 或 dnf 在本地安装](https://www.telepresence.io/reference/install)。

或者查看其他教程

*   [调试 Kubernetes 上的服务](https://www.telepresence.io/tutorials/kubernetes)
*   [通过谷歌容器引擎使用网真](https://cloud.google.com/community/tutorials/developing-services-with-k8s)
*   【OpenShift 和网真入门
*   [网真和迷你库](https://www.telepresence.io/tutorials/minikube-vpn)

有问题吗？在网真 [Gitter 聊天室](https://gitter.im/datawire/telepresence)提问或者[在 GitHub](https://github.com/datawire/telepresence/issues/new) 上提出问题。