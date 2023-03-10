# 什么是区块链、比特币、以太坊、智能合约

> 原文：<https://dev.to/tonioriol/what-is-blockchain-bitcoin-ethereum-and-smart-contracts-354i>

[![](img/47712a1831b0daa87cd7e910aa4c25f2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TW1-kh6P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AYl8N6V5UghPCmpqVuobO8A.jpeg) 

<figcaption>由[利兹](https://dribbble.com/Theysaurus)</figcaption>

你可能听说过区块链，也可能没有，但它是最大的金融革命背后的技术。

比特币是第一种去中心化的加密货币，于 2009 年由中本聪创造。公平地说，不完全是。

[![](img/bed2eef56993fed48055eb5ea357de60.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GxpF-Bhw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/960/1%2Ag_YFZ_KStedWTe35tvpvlQ.png)

中本聪是设计比特币并创建其原始参考实现的人使用的名字。

中本聪的身份是科技领域最引人注目的故事之一。2008 年，中本聪用白皮书推出比特币；2011 年，就在项目进展顺利的时候，他消失了，他频繁的论坛帖子和电子邮件逐渐消失。(在他最后一封为人所知的信件中，他告诉一名比特币开发者，他已经“转向了其他事情。”).

我们需要知道谁是中本聪——纽约客

中本聪之谜值得为自己写一整篇文章，但现在让我们把注意力集中在比特币上。

### 比特币是什么？

比特币是一个像 Visa 或 MasterCard 一样的全球支付网络，但由于任何数字化的东西都可以被一次又一次地复制，困难的部分是确保没有人花同样的钱超过一次，这是一个被称为[重复消费](https://en.m.wikipedia.org/wiki/Double-spending)问题的已知问题。传统上，这是通过一个可信的中央机构(如 PayPal)来验证所有交易来实现的。

但比特币有一个本质的区别:没有公司拥有网络的所有权或控制权。取而代之的是，遍布世界各地的计算机在一个巨大的点对点网络中合作，维护一个被称为区块链的共享交易记录。这就形成了一个支付不可逆、账户不能冻结、交易费用低得多的系统。

### 比特币的关键方面

### **余额**

区块链是一个共享的公共账本，整个比特币网络都依赖于它。所有确认的交易都包含在区块链中。通过这种方式，比特币钱包可以计算他们的可消费余额，新交易可以被验证为消费实际上属于消费者的比特币。区块链的完整性和时间顺序是通过加密来实现的。

### **交易**

交易是比特币钱包之间的价值转移，包含在区块链(记住，共享公共账本)中。比特币钱包保存了一份被称为私钥或种子的秘密数据，用于签署交易，提供了来自钱包所有者的数学证明。签名还防止交易一旦发出就被任何人修改。所有交易都在用户之间广播，通常在接下来的 10 分钟内开始被网络确认，通过一个叫做挖掘的过程。

### **处理**

挖掘是一个分布式共识系统，用于通过将等待的事务包含在区块链中来确认它们。它在区块链中强制执行时间顺序，保护网络的中立性，并允许不同的计算机就系统的状态达成一致。为了得到确认，交易必须被打包在一个符合非常严格的加密规则的块中，这些规则将被网络验证。这些规则防止前面的块被修改，因为这样做会使后面的所有块无效。采矿还创造了一种类似于竞争性彩票的东西，阻止任何个人轻易在区块链连续增加新的区块。这样，任何个人都无法控制区块链中包含的内容或替换区块链的部分内容来缩减自己的支出。

[比特币是如何运作的？——比特币](https://bitcoin.org/en/how-it-works)

[![](img/a4bd243f5f51c33ff9b3d393107251f4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hvVNqa2x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Akw3e09Wi8UWGDzMhn9YvmA.gif) 

<figcaption>这是区块链实时的样子。</figcaption>

[![](img/dfd697b561a453df3f32eea923f95304.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--owlwUoY_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/960/1%2AftZj495_M2CwUCr7_-FRzw.png) 

<figcaption>某笔交易的明细。</figcaption>

[比特币区块探索者-区块链](https://blockchain.info)

[![](img/f80575c36913316c90c1b3ea4b880289.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Pl8TNsVY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/960/1%2AED_6_36yVuIqWaOhwsUXKg.png) 

<figcaption>比特币价格图表</figcaption>

这是比特币自诞生以来的价格演变图，你可以看到在 2013 年 11 月 23 日出现了一个巨大的峰值，当时一个比特币的价格升至 1242 美元的历史高点。

[![](img/a2c254ba76860fcc1dc5d9bb09821074.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6EfDLm61--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/960/1%2AeG3-bAj4VAtPGPASc1OxFA.png)

几乎 1600 万是已经被开采的比特币总数；换句话说，目前网络上的比特币供应量。

相当于 100 亿美元。一个比特币等于 618.38 美元。

但我想谈谈区块链，不仅仅是比特币。为什么？

因为在比特币受欢迎程度上升后，有一堆其他加密货币使用同样的区块链方法激增。比如:

[![](img/5477f4ff50a78c01ae60892e8ea625c3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wVThK3GM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/960/1%2Asm13F2Mgvc--XDD173dXyQ.png)

莱特币，灵感来自比特币，技术上与比特币几乎相同。

[![](img/fba4945218c3bd2dd6a2460c216600a7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--q5f6WEbG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/960/1%2AjHoSMlOPdglVnMZs7qk5Og.png)

Dogecoin，以互联网上“Doge”的一只柴犬为标志。作为一种“玩笑货币”，Dogecoin 迅速发展了自己的在线社区，并在 2014 年 1 月达到了 6000 万美元的资本总额。

我想和大家分享一下他的宣传片，我觉得很有教育意义。

[https://www.youtube.com/embed/000al7ru3ms](https://www.youtube.com/embed/000al7ru3ms)

但是这里我想说的是区块链。

### 以太坊

所有现代加密货币都依赖于这项技术来保持一切正常，只是在这个过程中进行了一些小的修改。但是还有一种加密货币更进了一步，它叫做以太坊。

[![](img/43ec1a2087c8c1e654a02cb6c0630189.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--X59BA7xO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/980/1%2AjxWxj_WSiJC6WhfzAMJKOw.png)

比特币网络是为验证和记录支付而定制的。2014 年，一位名叫 Vitalik Buterin 的 20 岁程序员意识到，他可以创建一个类似比特币的网络，可以执行更广泛的计算任务。如果说比特币是 Visa 或 MasterCard 的分布式版本，那么以太坊就有点像亚马逊和微软这样的公司运营的云计算平台的分布式版本。

[![](img/0442b4c95cfa0925cd172ea449287135.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uJICfAx1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/960/1%2Au418xZhQZFowr-9Z9si9Pg.png) 

<figcaption>维塔利克·布特林，以太坊的创造者。</figcaption>

您不仅可以使用以太坊进行以太计价的电子支付，还可以在以太坊网络上运行程序。

以太坊是一个非常不寻常的云计算网络。每一次计算都是由全球数千台计算机同时执行的，这使得它的效率比传统的在线服务器低几千倍。而且因为这些计算的结果都存储在以太坊区块链上，所有的数据都是公开的。所以以太坊对于运行 web 服务器这样的传统应用来说是一个可怕的选择。

但以太坊的分布式结构也让它有了得天独厚的优势:一旦一个程序开始运行，没有人有权力修改或停止它。这意味着你可以使用以太坊做出有约束力的长期承诺——这就是以太坊项目被称为“智能合约”的原因

[![](img/1fbf4cc1868b88c489e3212ee5b5c8bd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8UWObyIe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/960/1%2AX_dPkyuP0ZRh3XfuTJWqEA.png)

举例说明以太坊的功能是一个很好的方法。比特币最大的挑战之一是货币的波动性；以太坊为这个问题提供了一个潜在的解决方案:一个对冲货币波动的智能合约。

两个用户可能分别向智能合同提交价值 1000 美元的乙醚。一个月后，智能合约将查找当前的美元/乙醚汇率，以新的汇率向一个用户支付价值 1000 美元的乙醚(可能比最初提交的乙醚多或少)，并将剩余的乙醚发送给第二个用户。

这与传统的对冲合约一样，但有一个重要的区别:该合约是由以太坊网络上运行的计算机程序执行的，而不是由法院执行的。程序一旦提交，任何一方都不能修改，所以任何一方都不必信任另一方。

### 以太坊允许一种全新类型的组织

以太坊契约有很多不同的使用方式，但是最吸引人的应用是虚拟组织。从根本上来说，一个组织就是一群人之间的一系列协议——股东、员工、债权人等等。在大多数组织中，这些都是由法院系统强制执行的传统合同。以太坊允许创建分散的自治组织，其合同和章程由以太坊智能合同执行。

这不仅仅是理论上的可能性。一个名为 DAO 的虚拟组织在过去几个月里筹集了超过 1.5 亿美元。从技术上讲，DAO 只是一个由运行在以太坊区块链上的计算机程序控制的特定以太坊地址。人们把乙醚送到这个地址，并取回组织的股份。

一旦筹资阶段完成，这些股东将能够投票决定如何处置这些资金。这个想法是 DAO 将作为以太坊社区的一种风险投资基金。程序员和公司将向 DAO 提交详细的项目提案。然后，道股东将投票决定为哪些提案提供资金。

[以太坊，解释:为什么比特币的陌生堂兄现在身价 10 亿美元](http://www.vox.com/2016/5/24/11718436/ethereum-the-dao-bitcoin)

这只是智能合同所能实现的几个例子。互联网上已经有很多应用程序在使用这项技术了！我认为，这将是永远改变我们社会的最大事件之一。

[![](img/641be7f03780a73b550718545674b7b2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---Oc8owgR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/960/1%2ACKOOoh-CpKEwL3U7oIpXew.png) 

<figcaption>你喜欢吗？</figcaption>

给我发些比特币到这个地址！

> 12 XXX 2 TCU 2 unfgh 1 V7 zzjgwncfsn 7 yvnju

这篇文章基于我做的一个演示，这就是为什么它充满了图片(来自幻灯片)，并且有许多文字是从文章中借用的。我试着把我用过的所有资源联系起来。T3】