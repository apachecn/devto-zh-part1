# IOTA - Hello World

> 原文：<https://dev.to/brightdevs/iota---hello-world-2f7g>

在之前的 IOTA 系列中，我们已经研究了[这个有前途的加密货币系统](https://dev.to/blog/iota-new-kid-in-cryptocurrency-town/)的假设，并且我们已经[用我们自己的节点](https://dev.to/blog/getting-started-with-iota/)连接到网络。是时候通过编程与 IOTA 进行交互了。

IOTA 节点(或者，实际上，系统的创建者更喜欢称之为 IOTA 参考实现)公开了一个 HTTP API，我们可以与之交互，并且——与大多数 IOTA 生态系统不同——有一个可用的[实际文档](https://iota.readme.io/v1.2.0/reference)。但是，尽管研究这个引用是不可或缺的，我们还是宁愿使用客户端库来与我们的节点对话。

目前有三个官方客户端库，最成熟的显然是我们想要使用的——一个叫做 [iota.lib.js](https://github.com/iotaledger/iota.lib.js) 的 JavaScript 库。它基本上是 API 端点的包装器。

## 使用节点与节点交谈

让我们从空节点开始。我们可以使用`npm init`引导的 JS 项目。然后用`npm install iota.lib.js --save`安装 IOTA 的库，跳转代码。

为了建立连接，我们需要指定我们的 IRI 在哪里，包括公共 API 端口(在节点的配置文件中指定为`PORT`，如果从命令行传递，则指定为`-p`)。请注意，默认情况下，我们不需要任何凭证——这就是为什么让我们的节点的 API 端口对外部网络隐藏或者用`--remote-auth`和`--remote-limit-api` [配置选项](https://iota.readme.io/v1.2.0/docs/install-iri)进行适当配置是很重要的，以避免每个人都来捣乱我们心爱的节点。

```
const IOTA = require('iota.lib.js')
const iota = new IOTA({
    host: 'http://192.168.1.162',
    port: 14265
}) 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们看看我们的节点使用 [`getNodeInfo`](https://iota.readme.io/v1.2.0/reference#getnodeinfo) 调用告诉我们什么。所有的 API 调用都依赖于笨拙的节点。JS [回调传球惯例](https://www.joyent.com/node-js/production/design/errors) :

```
iota.api.getNodeInfo((error, nodeInfo) => {
    if (error) {
        console.error('getNodeInfo error', error)
    } else {
        console.log('getNodeInfo result', nodeInfo)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

除了节点的版本和足迹信息之外，我们将得到的回报是处理状态——是否有任何待传输的事务，以及我们的节点所知的最新里程碑是什么。如果我们的节点与网络同步，后一个值应该每隔几分钟改变一次。

## 我的钱包呢？

IOTA 中的地址可以理解为可以存储 IOTA 令牌的不同钱包。这些字符串足够长，所以完全可以自己生成，并且在实践中要确保其唯一性。

为了声明一个给定地址的所有权，我们需要拥有产生它的种子——把种子想象成你的装有钱包的盒子的私钥。种子[需要安全地生成](https://iotasupport.com/gui-newseed.shtml)，可能不需要使用公共网站为您生成。最简单的可能是使用 macOS/Linux 终端并运行:

```
cat /dev/urandom | LC_ALL=C tr -dc 'A-Z9' | fold -w 81 | head -n 1 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们拿回来的东西，看起来很像`FNCWNXJWJIVDGPRWNZYKOMKNIIATPPDKEVCZEWSZTEVIWJFCOUV9PJD9AUCEVQLFEAI9UBUAVQKVEBLKN`，是我们的种子。我们负责安全和保密地存储它，因为它提供了对我们使用它创建的所有地址(钱包)的完全访问权。

为了实际生成我们可以用来发送事务的地址，让我们使用我们的 API:

```
const seed = 'FNCWNXJWJIVDGPRWNZYKOMKNIIATPPDKEVCZEWSZTEVIWJFCOUV9PJD9AUCEVQLFEAI9UBUAVQKVEBLKN' // keep it secure!
iota.api.getNewAddress(seed, (error, address) => {
   if (error) {
       console.error('getNewAddress error', error)
   } else {
       console.log('new address generated: ' + address)
   }
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 消费代币

我们现在准备提交我们的第一个事务，或者 API 怎么称呼它——转移。除了货币价值(可以为零)，我们还可以在交易中附加一条消息。该消息需要进行 [tryte 编码](https://learn.iota.org/faq/trytes-and-trits)——幸运的是，我们不需要太在意它，因为我们有一个用于该任务的助手方法: [`iota.utils.toTrytes`](https://github.com/iotaledger/iota.lib.js#totrytes) 。

将我们的事务发送到 IOTA 的 tangle 的代码如下:

```
const Depth = 3 // constant defined by IOTA - how deep to look for the tips in the Tangle
const MinWeightMagnitude = 16 // constant defined by IOTA - the difficulty of PoW

const transfers = [
    {
        // where are we sending the transaction to?
        address: 'CHNLHJCYBZCYUI9DTHINDDWHNJWFCHQOTGABXFVZQHXF9BROTOIJZJSBXOVKCDGCXZTDDJUVTYBJZYAOH',

        // how many tokens are we transferring?
        value: 42,

        // do we want to comment on this transaction?
        message: iota.utils.toTrytes('Hello World!')        
    }
]

const options = {
    // addresses of the wallets we're using to fund this transfer
    inputs: ['ZISNLNSKMPDOORSSFRCBGQOPY9BI9SONMTDHJJDWBTTCYLFV9PQ9VSWNI9FHEAEFGROGZ9YHSMZYOGFQG']
}

iota.api.sendTransfer(seed, Depth, MinWeightMagnitude, transfers, options, (error, transactions) => {
  if (error) {
     console.error('sendTransfer error', error)
  } else {
     console.log('transactions sent!', transactions)
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

我们需要指定将带有消息的令牌发送到哪里，以及令牌来自哪个(我们的)钱包(如果我们发送非零值)。在响应中，我们为提交的每个`transfer`对象获取一个`transaction`对象。

[![Attaching to the IOTA's Tangle](img/74cc221b9f3aac9a3bee0078414c63d3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--37QtA6uv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d6qmt4n63a5uap8omhia.jpg)

如果幸运的话，我们应该能够获得交易的`hash`属性，将其粘贴到在线纠结查看器的[中，并查看我们交易的细节。它很可能处于“待定”状态。这意味着事务被正确地附加到了 Tangle，尽管它还没有被 Tangle 树中的其他事务验证，我们需要等待一段时间。通常情况下，它会在几分钟内进入“确认”状态，我们可以称之为一天。](https://thetangle.org)

但是我们可能没有那么幸运，我们的事务可能会连接到树中永远不会被验证的部分，或者是因为 Tangle 树中有太多的 tip 等待验证，所以它被 tip 选择算法“遗忘”(tip 选择算法偏向于从树的顶部开始的事务)，或者它碰巧连接到产生错误的子树。

在这些情况下，我们的事务永远不会离开“挂起”状态，我们需要通过将我们的传输(称为“捆绑”)重新附加到 Tangle 树的另一部分来解决这个问题。为了做到这一点，我们可以定期运行如下代码:

```
iota.api.getLatestInclusion([hash], (error, inclusionStates) => {
  if (error) {
     console.error('getLatestInclusion error', error)
  } else if (inclusionStates[0]) {
     console.log('transaction is included (confirmed)! yay!') 
  } else {
     iota.api.replayBundle(hash, Depth, MinWeightMagnitude, (error, replayTransactions) => {
         // ad infinitum...?
     })
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

这个过程可能看起来很奇怪，因为我们实际上在向 Tangle 添加越来越多的副本——replay 事务是一个独立的事务。我们现在可能需要跟踪原始事务和重放事务的“包含状态”(status)。我们还需要再次重放它，以防它在几分钟内没有得到验证。所有这些都伴随着发布新交易的成本，但这实际上对 IOTA 网络整体有利，因为通过这样做，我们确认了另一对交易。并且实现了双重花费验证方案，确保只有一个交易将被最终确认。

最初发布于 [brightinventions.pl](https://brightinventions.pl/blog/)

由亚当吧、网络佬@光明发明
[个人博客](https://whatwebcando.today/) [推特](https://twitter.com/NOtherDev) [邮箱](//adam.bar@brightinventions.pl) [Github](https://github.com/NOtherDev)