# 借助网真技术，针对远程 Kubernetes 集群在本地开发和调试服务

> 原文：<https://dev.to/kelseyevans/develop-and-debug-services-locally-against-remote-kubernetes-clusters-with-telepresence>

Telepresence 是一个开源工具，允许您在本地运行单个服务，同时将该服务连接到远程 Kubernetes 集群。这使得从事多服务应用的开发人员能够:

1.  对单个服务进行快速本地开发，即使该服务依赖于集群中的其他服务。对您的服务进行更改，保存，您可以立即看到新服务的运行。

2.  使用本地安装的任何工具来测试/调试/编辑您的服务。比如可以用调试器！

3.  让您的本地开发机器像您的 Kubernetes 集群的一部分一样运行。如果您的机器上有一个应用程序，您想要针对集群中的一个服务运行，这很容易做到。

网真可以在 Mac OS X 和 Linux 上运行，带有[操作系统原生包](https://www.telepresence.io/reference/install)。

## 工作原理

Telepresence 在 Kubernetes 集群中运行的 pod 中部署双向网络代理。这个 pod 将 Kubernetes 环境中的数据(例如，TCP 连接、环境变量、卷)代理到本地进程。本地进程的网络被透明地覆盖，因此 DNS 调用和 TCP 连接通过代理路由到远程 Kubernetes 集群。

这种方法提供了:

*   您的本地服务可以完全访问远程集群中的其他服务
*   您的本地服务完全访问 Kubernetes 环境变量、机密和配置图
*   您的远程服务完全访问您的本地服务

远程呈现如何工作将在[这里](https://www.telepresence.io/discussion/how-it-works)详细讨论。

## 网真的替代品

虽然有几种不同的方法来为微服务设置[您的开发环境，但网真让您拥抱混合模式。远程呈现的典型替代方案包括:](https://www.datawire.io/guide/development/development-environments-microservices/)

*   通过 Docker Compose 在本地运行整个多服务应用程序。这给了你一个快速的开发/调试周期。然而，这不太现实，因为您实际上并没有在 Kubernetes 中运行您的服务，而且您可能会使用一些云服务(例如，一个数据库),这些云服务在本地可能不容易使用。
*   迷你库贝。你不能用 minikube 本身进行实时编码/调试，但是你可以用 Telepresence。两者配合得很好。
*   在远程 Kubernetes 集群中运行一切。同样，您不能在远程 Kubernetes 集群中进行实时编码/调试...但是你可以用远程呈现。

## 入门

有许多关于使用网真的快速入门教程:

*   [调试 Kubernetes 上的服务](https://www.telepresence.io/tutorials/kubernetes)
*   [通过谷歌容器引擎使用网真](https://cloud.google.com/community/tutorials/developing-services-with-k8s)。
*   【OpenShift 和网真入门
*   [网真和迷你库](https://www.telepresence.io/tutorials/minikube-vpn)