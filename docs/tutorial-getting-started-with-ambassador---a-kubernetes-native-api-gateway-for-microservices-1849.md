# 教程:Ambassador 入门——用于微服务的 Kubernetes 本地 API 网关

> 原文：<https://dev.to/datawireio/tutorial-getting-started-with-ambassador---a-kubernetes-native-api-gateway-for-microservices-1849>

Ambassador 是基于 [Envoy 代理](https://www.envoyproxy.io/)构建的微服务的 Kubernetes-native API 网关。大使是为自助服务设计的。开发人员应该能够管理大使的基本方面，而不需要操作。Ambassador 通过允许开发人员通过 Kubernetes 注释配置它来实现这一点。这使得开发人员可以使用他们现有的 Kubernetes 部署工作流轻松管理 Ambassador。

在本教程中，我们将先用一个演示配置快速浏览一下 Ambassador，然后再介绍如何用一个定制配置在 Kubernetes 中部署 Ambassador。

## 1。运行演示配置

默认情况下，Ambassador 使用演示配置来展示它的一些基本特性。让它与 Docker 一起运行，并在 8080 端口上暴露大使:

```
docker run -it -p 8080:80 --name=ambassador --rm datawire/ambassador:{VERSION} --demo 
```

Enter fullscreen mode Exit fullscreen mode

## 2。大使诊断

Ambassador 提供可通过网络浏览器查看的实时诊断。虽然这通常不会在公共网络中公开，但 Docker demo 在以下 URL 发布了诊断服务:

`http://localhost:8080/ambassador/v0/diag/`

一些最重要的信息——你的大使版本，大使配置更新的时间，以及特使最近一次向大使报告状态的时间——都在最上面。诊断概述可以显示您在配置图中看到的内容，以及根据您的配置创建了哪些特使对象。

## 3。瞬间服务的报价

由于 Ambassador 是一个 API 网关，它的主要目的是提供对微服务的访问。这个演示预先配置了一个映射，将`/qotm/`资源连接到“当前报价”服务——一个提供报价的演示服务。你可以在这里试试:

```
curl http://localhost:8080/qotm/ 
```

Enter fullscreen mode Exit fullscreen mode

这个请求将在`demo.getambassador.io`被路由到`qotm`服务，并在 JSON 对象中返回一个报价。

您也可以通过点击诊断概述中的`mapping-qotm.yaml`链接，或打开

`http://localhost:8080/ambassador/v0/diag/mapping-qotm.yaml`

## 4。证明

在诊断概述中，您还可以看到 Ambassador 被配置为进行身份验证——单击`auth.yaml`链接，或者打开

`http://localhost:8080/ambassador/v0/diag/auth.yaml`

更多信息请点击此处。Ambassador 在`demo.getambassador.io`使用一个演示认证服务来传递对当前报价的访问:简单地获得一个随机报价是允许的，无需认证，但是要获得一个特定的报价，您必须进行认证:

```
curl -v http://localhost:8080/qotm/quote/5 
```

Enter fullscreen mode Exit fullscreen mode

会返回一个 401，但是

```
curl -v -u username:password http://localhost:8080/qotm/quote/5 
```

Enter fullscreen mode Exit fullscreen mode

会成功的。(注意那是字面上的“用户名”和“密码”demo auth 服务故意不太安全！)

注意，由认证服务来决定什么需要认证——将 Ambassador 与认证服务结合起来可以根据您的需要灵活或严格。

## 5。驻 Kubernetes 大使

到目前为止，我们已经使用了一个演示配置，并在本地 Docker 实例中运行一切。我们现在将切换到 Kubernetes，使用服务注释配置 Ambassador 来映射`/httpbin/`到`httpbin.org`。

### 5.1 定义大使服务

大使被部署为 Kubernetes 服务。创建下面的 YAML，并把它放在一个名为`ambassador-service.yaml`的文件中。