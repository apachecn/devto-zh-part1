# Kubernetes -使用 ELB 和 nginx 入口控制器将 HTTP 重定向到 HTTPS

> 原文：<https://dev.to/tomhoule/kubernetes---redirect-http-to-https-with-elb-and-the-nginx-ingress-controller>

TL；DR 阅读最后一段，了解用 nginx 入口控制器实现 HTTPS 的最新方法，同时让 ELB 处理证书。

在我们与 Kubernetes 的探索性工作中，第一个障碍是配置正确的 HTTP 入口。起初，我们希望从我们的设置中获得三个属性:

*所有未加密的 http 流量立即被重定向到 https* 。
T3【入口控制器不处理 TLS 证书。相反，我们在 ELB 层面终止 TLS。
*重定向由入口控制器*处理，而不是单独的入口资源，因为我们总是希望 HTTP 流量被加密，并且它限制了出错的可能性。

事实证明，将这三者结合起来比预期的更复杂，因为 [ELB 不做 HTTP 重定向](https://aws.amazon.com/de/premiumsupport/knowledge-center/redirect-http-https-elb/)，而且[trfik](https://docs.traefik.io/toml/#kubernetes-ingress-backend)和 [nginx](https://github.com/kubernetes/ingress/tree/master/controllers/nginx) 入口控制器都不支持重定向，除非它们自己处理 TLS 终止。

## 我们的解决方案

DNS(路由 53)和 ELB 配置是最简单的。

我们首先为入口创建了一个负载平衡器服务:

```
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
  namespace: kube-system
  annotations:
    service.beta.kubernetes.io/aws-load-balancer-ssl-cert: <the arn for our wildcard TLS cert>
    service.beta.kubernetes.io/aws-load-balancer-backend-protocol: http
    service.beta.kubernetes.io/aws-load-balancer-ssl-ports: https
spec:
  selector:
    k8s-app: nginx-ingress-lb
  type: LoadBalancer
  ports:
  - name: http
    port: 80
    targetPort: 80
  - name: https
    port: 443
    targetPort: 80 
```

Enter fullscreen mode Exit fullscreen mode

应用后，该服务将为您创建 ELB 负载平衡器。

我们的 DNS 配置非常简单:一个指向负载平衡器的“网关”域。其他子域只是指向它的 CNAME 记录。“gateway”域是作为集群创建的一部分手动更新的，我们只需在需要时添加 CNAME 记录。我们计划在未来为“网关”域试用[外部 dns](https://github.com/kubernetes-incubator/external-dns) 来实现自动化。

最后一部分不太理想，因为 kubernetes 的 nginx 入口控制器(nginx 的人还有另外一个实现)不支持重定向到 https，除非它被配置为自己处理 https 流量(这是我们不希望的)。

幸运的是，控制器让您完全覆盖它的 nginx 配置文件，所以我们只是从源代码复制了默认文件，这当然不理想，并添加了我们想要的，这是一个基于 ELB 设置的`X-Forwarded-Proto`头的 301 重定向:

```
if ( $http_x_forwarded_proto = http ) {
  rewrite ^(.*) https://$host$1 permanent;
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们可以将修改后的配置模板放在一个 ConfigMap 中(在我们的例子中是 kube-system 名称空间中的 nginx-config ),并通过将它放在部署的`container`部分将其传递给 nginx:

```
args:
  - /nginx-ingress-controller
  - --default-backend-service=kube-system/default-http-backend
  - --publish-service=kube-system/nginx-service
  - --configmap=kube-system/nginx-config 
```

Enter fullscreen mode Exit fullscreen mode

## 怎么做*今天*

这个故事证明了 k8s 生态系统的发展速度。整整两周前，我们在 nginx 入口控制器版本 0 . 9 . 0-beta 1 上提出了这个解决方案。现在，当前版本是[0 . 9 . 0-beta 3](https://github.com/kubernetes/ingress/blob/master/controllers/nginx/Changelog.md#09-beta3)，并且添加了`ingress.kubernetes.io/force-ssl-redirect`注释，通过在每个入口资源中添加注释，可以实现相同的配置。

这个选项勾选了 3 个框中的 2 个，我们发现最后一点，将这个设置集中化，并不重要，不足以证明黑客的合理性。

*大家好，我们是 [store2be](https://www.store2be.com) ，一家总部位于柏林的初创公司，为短期零售空间建立了一个支持 SaaS 的市场。如果你喜欢我们发布的内容，你可能想看看[store 2 be 技术页面](https://tech.store2be.com)或者关注我们的[媒体频道](https://medium.com/store2be-tech)。*