# 在只有 2 个 AZ 的 AWS 区域中有 kops 的多 AZ Kubernetes

> 原文：<https://dev.to/peterfication/multi-az-kubernetes-with-kops-in-aws-regions-with-only-2-az>

目前，我们正在 store2be 设立 Kubernetes。我们是一家来自德国的公司，我们的大部分业务都发生在德国。因此，我们希望我们的 AWS 服务器在法兰克福。

我们使用 [kops](https://github.com/kubernetes/kops) 进行 Kubernetes 集群管理，我们希望我们的集群是一个多可用性区域(AZ)部署。考虑到这一点，我们尝试了以下命令:

```
$ kops create cluster \
    --node-count 4 \
    --zones eu-central-1a,eu-central-1b \
    --master-zones eu-central-1a,eu-central-1b \
    --node-size t2.medium \
    --master-size m3.medium \
    --yes \
    kubernetes-cluster.example.org 
```

Enter fullscreen mode Exit fullscreen mode

但是结果并不乐观

```
There should be an odd number of master-zones, for etcd's quorum.  Hint: Use --zones and --master-zones to declare node zones and master zones separately. 
```

Enter fullscreen mode Exit fullscreen mode

所以不幸的是，目前看来在一个只有 2 AZ 的 AWS 区域中使用 kops 开箱即用是不可能的。但是经过一番挖掘之后，我们发现了一个描述我们问题的 Github 问题，甚至还有一个针对这个问题的 T2 解决方案。所以下面的步骤是基于[卡米尔·赫里斯托夫](https://github.com/kamilhristov)提供的解决方案。

## 1。为带有 3 个 AZ 的 AWS 区域创建一个 cluster.yml

在欧盟西部创建一个集群(因为它有 3 个 AZ):

```
$ kops create cluster \
    --node-count 4 \
    --zones eu-west-1a,eu-west-1b,eu-west-1c \
    --master-zones eu-west-1a,eu-west-1b,eu-west-1c \
    --node-size t2.medium \
    --master-size m3.medium \
    --yes \
    kubernetes-cluster.example.org 
```

Enter fullscreen mode Exit fullscreen mode

转储集群和实例组配置并附加到:

```
$ kops get cluster --name=kubernetes-cluster.example.org --output yaml > cluster.yml && \
  echo "\n---\n" >> cluster.yml && \
  kops get instancegroups --name=kubernetes-cluster.example.org --output yaml >> cluster.yml 
```

Enter fullscreen mode Exit fullscreen mode

## 2。调整集群. yml

*   用欧盟中部替换所有出现的欧盟西部
*   用 eu-central-1a 替换 eu-central-1c 的所有分区定义
*   将 eu-central-1c 的所有名称定义替换为不同于 eu-central-1a 的名称定义，例如 eu-central-1a2(否则会有名称冲突)

## 3。使用调整后的 cluster.yml 创建分类

 `# 这里我们必须使用 replace，因为集群规范已经有了

# 已在步骤 1 中创建。

$ kops replace-f cluster . yml
$ kops create secret-name kubernetes-cluster.example.org ssh public key admin-I ~/。ssh/id_rsa.pub
$ kops 更新集群-是
`

 `在✨，法兰克福有一个多阿兹星团🎉

我写这篇文章是因为我们花了一段时间才找到这个解决方案，我希望我们能帮助一些人。感谢[卡米尔·赫里斯托夫](https://github.com/kamilhristov)分享解决方案。感谢[汤姆·霍利](https://github.com/tomhoule)审阅这篇文章。

* * *

*大家好，我们是 [store2be](https://www.store2be.com) ，一家总部位于柏林的初创公司，为短期零售空间建立了一个支持 SaaS 的市场。如果你喜欢我们发布的内容，你可能想看看[store 2 be 技术页面](https://tech.store2be.com)或者关注我们的[媒体频道](https://medium.com/store2be-tech)。*``