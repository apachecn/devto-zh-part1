# 与普罗米修斯号操作员一起在库贝内特斯上监视特使和大使

> 原文：<https://dev.to/kelseyevans/monitoring-envoy-and-ambassador-on-kubernetes-with-the-prometheus-operator-j86>

在 Kubernetes 生态系统中，一个新出现的主题是应用程序如何最好地利用 Kubernetes 的各种功能。Kubernetes 社区也引入了新的概念，例如[自定义资源](https://kubernetes.io/docs/concepts/api-extension/custom-resources/)，使得构建 Kubernetes 本地软件变得更加容易。

2016 年末，CoreOS 推出了[算子模式](https://coreos.com/blog/introducing-operators.html)，并发布了[普罗米修斯算子](https://coreos.com/operators/prometheus/docs/latest/)作为该模式的工作实例。Prometheus 操作员自动创建和管理 Prometheus 监控实例。

运营商模型对于部署多种服务的云原生组织来说尤其强大。在这个模型中，每个团队可以根据需要部署他们自己的 Prometheus 实例，而不是依赖一个中央 SRE 团队来实现监控。

## 使节、大使和普罗米修斯

在本教程中，我们将展示如何使用 Prometheus 操作符来监控部署在边缘的 Envoy 代理。 [Envoy](https://www.envoyproxy.io) 是一个开源的 L7 代理。特使越来越受欢迎的(许多)原因之一是它强调可观察性。特使使用 [statsd 作为其输出格式](https://www.envoyproxy.io/docs/envoy/v1.5.0/intro/arch_overview/statistics.html)。

我们不用直接用 Envoy，而是用 [Ambassador](https://www.getambassador.io) 。Ambassador 是一个基于 Envoy 构建的 Kubernetes-native API 网关。与 Prometheus 操作符类似，Ambassador 在 Kubernetes 中配置和管理 Envoy 实例，因此最终用户不需要直接做这项工作。

## 先决条件

本教程假设您运行的是 Kubernetes 1.8 或更高版本，并且启用了 RBAC。

注意:如果你运行的是 Google Kubernetes 引擎，你需要给将要安装 Prometheus 和 Ambassador 的帐户授予`cluster-admin`特权。你可以用下面的命令做到这一点:

```
$ gcloud info | grep Account
Account: [username@example.org]
$ kubectl create clusterrolebinding my-cluster-admin-binding --clusterrole=cluster-admin --user=username@example.org 
```

Enter fullscreen mode Exit fullscreen mode

## 部署普罗米修斯操作员

Prometheus 操作员被配置为 Kubernetes `deployment`。我们将首先部署普罗米修斯操作员。