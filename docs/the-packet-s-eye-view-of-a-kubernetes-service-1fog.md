# Kubernetes 服务的数据包视图

> 原文：<https://dev.to/maelvls/the-packet-s-eye-view-of-a-kubernetes-service-1fog>

服务和入口分别为您的 pod 带来 L4 和 L7 流量。我之前谈到过[使用`hostPort`来避开 GKE 的负载均衡器](https://dev.to/avoid-gke-lb-with-hostport/)，但没有真正深入到每件事是如何工作的细节。在本文中，我将重点讨论流量是如何流入的，以及入口控制器和“service-lb 控制器”(创建外部负载平衡器的那个东西)之间的交互是什么。我还详细介绍了`hostPort`方法如何影响流量。

* * *

在 Kubernetes 中，服务对象表示一组 pod 如何与 TCP 或 UDP 连接相匹配。因为它是 L4 专用的，所以该服务只处理 IP 和端口。入口是服务的 L7 对应物:它处理 TLS、主机名、HTTP 路径和虚拟服务器(称为“后端”)。

> 注意:由于 Traefik 是一个入口控制器，您可能会看到一些关于入口的内容，但我不会描述 L7 层的流量处理。

入口控制器是作为简单部署运行的二进制文件，它监视所有入口对象并实时更新 L7 代理。Traefik 有资格作为入口控制器:它具有在一个二进制文件中嵌入“入口监视器”和 L7 代理的优良特性。HAProxy 和 Nginx 都使用独立的入口控制器。为了让 L4 流量到达 L7 代理，我们通常使用类型为`LoadBalancer`的服务。

我称“service-lb controller”为二进制文件，它监视具有`LoadBalancer`类型的服务对象。每当谷歌的 service-lb 控制器发现一项新的`LoadBalancer`服务，它就会启动一个网络负载平衡器，并设置一些防火墙规则。事实上，Google 的 service-lb 控制器是作为一个小型的闭源二进制程序运行的，它运行在您的主节点上(但看不到它是一个 pod ),充当 service-lb 控制器。

下图显示了 GKE 的 service-lb 控制器如何与入口控制器交互:

[![GKE service LoadBalancer controller and ingress controller<br>
interaction](img/49088780950c2029158ca4488864b18e.png)T2】](/packets-eye-kubernetes-service/kubernetes-service-controllers-with-gke-service.svg)

那么，外部流量是如何到达 pod 的？下图显示了如何将数据包转发到正确的 pod。注意发生了多少次 iptable 重写(对应于由 conntrack 管理的一个连接):

[![Packet coming from a user hitting the GKE network balancer and forwarded to the right pod](img/7f0ef5d7dd3598a6cf1ebd10942ae716.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hEp0rgLj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/kubernetes-traffic-with-gke-lb.svg)

现在，让我们看看使用 [Akrobateo](https://github.com/kontena/akrobateo) 时的效果如何(我在这里详细说明了)。我们不使用外部计算资源，而是使用节点的 IP 来允许流量进入。

注意:Akrobateo 已经停产，但是 K3s 的 [servicelb](https://github.com/rancher/k3s/blob/master/pkg/servicelb/controller.go) 和 [Metallb](https://github.com/metallb/metallb) 的工作方式非常相似，用正确的外部 IP 设置服务的`status.loadBalancer`字段。

[![Akrobateo service LoadBalancer controller and ingress controller<br>
interaction](img/19930c96bb326bd1495dfeed0f8911c8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--o4CZlJjz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/kubernetes-service-controllers-with-akrobateo.svg)

你可能想知道为什么在`status.loadBalancer`中使用“内部 IP”。我们可能希望在那里设置外部 IP。但是，由于谷歌的 VPC NAT 将节点的外部 IP 交换为节点的内部 IP，因此传入的数据包将内部 IP 作为源 IP。这就是为什么😁。

下图显示了来自用户的数据包被路由到具有 ClusterIP `10.24.3.12`的服务。注意数据包重写的次数:

1.  VPC 防火墙的 DNAT，
2.  节点 1 上的`hostPort`规则中的 DNAT，
3.  Akrobateo 舱里的 DNAT，
4.  节点 1 上服务群集 IP 规则的 DNAT，
5.  节点 1 上的`externalTrafficPolicy: Cluster`规则导致的 SNAT(跨节点的服务级别随机负载平衡)，
6.  Traefik 的用户空间 TCP 终止(即新拨号到 pod-ip)。

那是许多跳跃和重写！

src = " kubernetes-traffic-with-akrobateo . SVG " width = " 70% "/>

> 2020 年 4 月 28 日更新:修正了我在这张图表中犯的一个错误。我在“用户请求”中将源端口设置为 80。这个例子没有反映现实中发生的事情。当连接到远程主机时，TCP 栈选择一个大于或等于 32768 的随机临时 IP。内核称之为“本地端口”，见 [`inet_hash_connect` ( `inet_hashtables.c` )](https://github.com/torvalds/linux/blob/c60174717544aa8959683d7e19d568309c3a0c65/net/ipv4/inet_hashtables.c#L739-L740) ，[，`secure_ipv4_port_ephemeral` ( `secure_seq.c` )](https://github.com/torvalds/linux/blob/9c7db5004280767566e91a33445bf93aa479ef02/net/core/secure_seq.c) ，[，`ip-sysctl.txt` )](https://github.com/torvalds/linux/blob/29d9f30d4ce6c7a38745a54a8cddface10013490/Documentation/networking/ip-sysctl.txt#L907-L914) 。
> 
> 所以我把“用户的请求”源端口从 80 改成了 32345:
> 
> ```
> -src: 1.2.3.4:80 +src: 1.2.3.4:32345
>  dst: 35.211.248.124:80 
> ```

对于“Akrobateo”方法，我们只依赖 VPC 的防火墙规则。但是使用节点的 IP 并不完美:这可能会被视为一种安全风险，并且当涉及到设置 DNS `A`记录时，服务的`status.loadBalancer`中结束的许多 IP 并不理想:

*   当一个节点消失时，DNS 可能会将流量转发到一个不存在的节点，
*   将入口流量负载平衡从具有单个 IP 的负载平衡器卸载到依赖 DNS 记录并不理想，因为 DNS 记录留给您的关于如何平衡流量的选项非常少。

K3s 使用这种使用节点 IP 作为服务后端的方法，不需要任何外部负载平衡器。

2020 年 4 月 28 日更新:修正了上一张图中源端口的一个错误。添加了上图中发生的 DNAT 和 SNAT 重写的列表。我还重写了介绍。