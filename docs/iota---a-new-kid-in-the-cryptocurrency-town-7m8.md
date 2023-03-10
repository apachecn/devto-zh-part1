# IOTA——加密货币小镇的新成员

> 原文：<https://dev.to/brightdevs/iota---a-new-kid-in-the-cryptocurrency-town-7m8>

在 Bright Inventions，我们总是努力保持对前沿技术和创新的关注。我们对加密货币及其在行业中的广泛应用特别感兴趣，而不仅仅是作为一种支付方式。作为一个基于区块链的应用平台，我们已经有了一些使用[以太坊](https://www.ethereum.org/)和[超级账本](https://www.hyperledger.org/)的经验，所以我们很好奇 [IOTA](http://iota.org/) 能提供什么。

IOTA 的营销产品非常突出。它自称为“物联网的骨干”，并承诺可扩展性、去中心化、模块化和无交易费。它的主要目标是实现“物联网经济”，即物联网设备可以进行安全、可验证和可靠的自动化交易的环境，即使是最小的金额。使用其他基于加密货币的技术是不可行的，因为交易费用可能大于交易金额。

## IOTA 是什么(或计划是什么)？

首先，[从技术上讲](https://xkcd.com/1475/)，与比特币、以太坊或 Hyperledger 相反，它不是一个基于[区块链的](https://en.wikipedia.org/wiki/Blockchain)解决方案，因为交易不需要形成区块，也不会创建单个区块链。相反，每个事务都是事务树形图中的一个单独的节点。每个事务引用树中存在的两个较早的事务，确认其有效性(通过验证其[工作证明](https://en.wikipedia.org/wiki/Proof-of-work_system))。IOTA 中的这个有向树形图叫做*纠结*。

[![IOTA Tangle visualization by David Sonstebo](img/7c71b0a2cefe75e23fb511708fe4ed72.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lFZ-VC1E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/12pu331gz89jheojqukp.png)

这样，至少在理论上，整个系统是自我验证的，并且批准过程与任何权威机构是分离的和分散的。这也是[消除交易费用](https://learn.iota.org/faq/how-can-there-be-no-transaction-fees)的一种方式-参与网络的所有节点都有动力验证其他交易的工作证明，因为这是向网络提交自己的交易所必需的。

IOTA 的下一个主要承诺——可伸缩性——在理论上得到保证，因为它不需要节点知道整个纠结树的状态——树可以在许多方向上异步增长。假设这些独立的分支机构最终会“纠缠”在一起，并通过未来的交易进行交叉验证。这种想法允许节点在非完全同步的状态下工作——这在物联网设备可能会因为各种原因而离线的情况下尤其有用。

## 当前的事态

让我们离开登录页面的营销嗡嗡声，看看所有这些承诺在今天的现实中是如何工作的。项目本身相当年轻——节点实现的[初始提交](https://github.com/iotaledger/iri/commit/f52d16723c78502110dde4b9662676707174aa94)甚至不到一年(2016 年 10 月)。虽然这个项目非常有活力，但它仍然处于非常早期的阶段。如果你指望任何详细的参考或深入的指示-你会失望。你所能找到的只是一份[理论学术白皮书](http://iota.org/IOTA_Whitepaper.pdf)、粗略和部分的[术语定义](https://iota.readme.io/v1.2.0/docs/glossary)、 [API 参考](https://iota.readme.io/v1.2.0/reference)和一个[充满活力的 Slack 社区](https://slack.iota.org/)，它充当公告板，是让节点连接到网络的唯一途径。对于所有其他的，你需要挖掘源代码或者依靠 Slack 上的社区来帮助你。

事实证明，其他理论目标在实践中也远未实现。

### 放权

纯 IOTA 缠结[易受攻击](https://medium.com/@ercwl/iota-is-centralized-6289246e7b4d),该攻击通过生成由虚假交易组成的足够大的树的子分支来实现无效交易的验证，使得该分支超过原始的合法交易分支，使其成为孤儿并导致双重花费交易的批准。

截至 2017 年 10 月，IOTA 网络中有一个[单一指定节点](https://blog.iota.org/the-transparency-compendium-26aa5bb8e260#dc3d)，称为协调器，它确保分类帐的一致状态，重新验证**所有**交易，并定期生成“里程碑标记”，旨在指示纠结树的“正确”分支。里程碑是一种特殊的事务，由指定的协调器节点发布，它将所有它直接或间接批准的事务标记为可信。

协调器的存在使得 IOTA 到目前为止还没有完全分散，并且阻止了拥有私有分类帐网络，因为它的地址被硬编码到 IRI(参考节点实现)中，并且它的来源没有被公开。

据说，协调器的存在是一种临时措施，直到协议稳定下来，网络达到一定规模，无法接管，但没有给出到期日期。

### 物联网 app 平台

IOTA 系统的想法是有可能在物联网设备上直接运行它。不过，在 Raspberry Pi 上运行完整的节点[不是](https://www.reddit.com/r/Iota/comments/6suctt/iota_on_raspberry_pi/) [还是](https://www.reddit.com/r/Iota/comments/6fhjvw/full_node_on_raspberry_pi/)可能的。但是，应该可以运行连接到外部节点的瘦客户端。

重要的是，只要没有[智能合约](https://www.reddit.com/r/Iota/comments/70fpew/smart_contracts_in_iota/)功能，IOTA 就不能作为一个应用平台。是有计划的，但是期望很快就能实现似乎不太合理。

## 接下来呢？

即使 IOTA 团队的清单上的待办事项数量很长，但肯定有一些部分甚至在今天仍然可以使用。

下一次，我们将研究我们可以与这种纠结互动的方式，即使是在它目前的成熟状态下。

最初发表于 2017 年 10 月 9 日 [brightinventions.pl](https://brightinventions.pl/blog/) 。

由亚当吧、网络佬@光明发明
[个人博客](https://whatwebcando.today/) [推特](https://twitter.com/NOtherDev) [邮箱](//adam.bar@brightinventions.pl)[Github](https://github.com/NOtherDev)[stack overflow](https://stackoverflow.com/users/142827/notherdev)