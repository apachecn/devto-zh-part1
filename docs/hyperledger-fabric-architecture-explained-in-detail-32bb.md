# Hyperledger Fabric 架构:详细解释

> 原文：<https://dev.to/skcript/hyperledger-fabric-architecture-explained-in-detail-32bb>

[![Hyperledger Fabric Architecture](img/c59f0253e0a2f3ca7a14a96707fd20c6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--p9Zggdva--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/1f4d7932dd80030947c2a10baac3aea8b4d70c3a/b23c4/svrmedia/heroes/f/1-1.jpg)

### 域

这是项目的顶级命名空间。假设您正在为供应链构建一个网络，那么通常项目名称或域名会被用作 Hyperledger Fabric 的域。

### 订购者

在一个域中，有一些排序者(可以是多个实例)，他们负责确保网络中的所有对等体都提交了一个事务。当一个对等体提出并提交一个事务时，定序者被告知新的事务，它将这个块转发并提交给所有相邻的对等体。

注:订购者不依赖于一个组织。但是，建议有多个订购者，以降低故障率。

### 组织

组织是对等体和各自证书颁发机构(CA)的容器。每个组织都有自己的 CA 和对等方列表。通常，组织用于区块链网络的物理隔离，使用您产品的每个组织都可以设置他们的物理机并加入您的网络。

### 证书颁发机构

证书颁发机构负责创建用户证书。它用于验证网络中的所有权。每个证书颁发机构都与一个组织相关联。

### 同行

对等体是连接到客户端并负责向世界状态提交事务的节点。每个对等体在一个`couchdb`数据库中都有自己的事务副本。一个组织可以有多个同级。虽然建议在一个 orderer 中有多个对等点以避免数据丢失，但是拥有 3 个或 4 个以上的对等点可能会导致更高的延迟率。