# 大使和 Istio:边缘代理和服务网格

> 原文：<https://dev.to/datawireio/ambassador-and-istio-edge-proxy-and-service-mesh-nla>

大使是一个用于微服务的 Kubernetes-native API 网关。Ambassador 部署在您的网络边缘，将传入流量路由到您的内部服务(也称为“南北”流量)。 [Istio](https://istio.io/) 是一个面向微服务的服务网格，旨在为服务到服务的流量(也称为“东西”流量)增加 L7 可观察性、路由和弹性。Istio 和 Ambassador 都是用[特使](https://www.envoyproxy.io)建造的。

大使和 Istio 可以一起部署在 Kubernetes。在此配置中，来自集群外部的传入流量首先通过 Ambassador 进行路由，然后再将流量路由到 Istio。Ambassador 处理认证、边缘路由、TLS 终止和其他传统的边缘功能。

这使得运营商可以两全其美:高性能、现代化的边缘服务(Ambassador)与最先进的服务网络(Istio)相结合。Istio 的基本 [ingress 控制器](https://istio.io/docs/tasks/traffic-management/ingress.html)，ingress 控制器非常有限，不支持认证或 Ambassador 的许多其他功能。

## 让大使与 Istio 合作

让大使与 Istio 合作很简单。在这个例子中，我们将使用来自 Istio 的`bookinfo`示例应用程序。

1.  按照默认指令在 Kubernetes 上安装 Istio。
2.  接下来，按照[说明](https://istio.io/docs/guides/bookinfo.html)安装 Bookinfo 示例应用程序。
3.  验证示例应用程序是否按预期运行。

默认情况下，Bookinfo 应用程序使用 Istio 入口。要使用大使，我们需要:

1.  安装大使。参见[快速入门](https://www.getambassador.io/user-guide/getting-started)指南。
2.  更新`bookinfo.yaml`清单以包含必要的大使注释。见下文。