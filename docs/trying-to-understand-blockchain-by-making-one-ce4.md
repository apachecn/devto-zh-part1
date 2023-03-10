# 试图通过制作一个来了解区块链！

> 原文：<https://dev.to/damcosset/trying-to-understand-blockchain-by-making-one-ce4>

## 简介

比特币和加密货币最近闹得沸沸扬扬。我对加密货币的转变感到相当失望，从一个惊人的概念变成了另一种赚快钱的方式(或者不是...).

但是我对加密货币的技术产生了浓厚的兴趣，显然还有区块链的概念。这个概念很吸引人，而且不仅限于比特币和朋友。我们可以想象这种技术的许多应用。因此，以一个合适的开发人员的方式，我决定编写一个区块链，或者我认为是一个区块链，以便更好地理解它是什么。

## 简单的项目

那么，我们需要什么来创造一个非常简单的区块链呢？

*   一个街区

积木是区块链的组成部分。在我们的例子中，一个块将由一个日期、一个索引、一些数据(在我们的例子中是一条消息)和前一个块的散列组成。

*   密码系统

为了保证信息安全，我们需要加密我们的数据。对于我们的小项目，我们将使用 js-sha256 包。这个过程将创建一个 64 个字符的字符串。最终，我们的区块链将是一系列散列，每个散列由 64 个字符组成。如前所述，我们使用前一个块的散列来加密新的块(这就是为什么我们称它为链)。

*   困难和暂时

我们不只是为每个块创建一个散列，仅此而已。散列必须是有效的。在我们的例子中，如果散列的前四个字符是 0，那么散列将是有效的。如果我们的哈希以“0000 ......”开头，它被视为有效。这就叫难度。难度越高，得到一个有效的 hash 需要的时间就越长。

但是，如果散列第一次无效，我们使用的数据中一定有什么东西改变了，对吗？如果我们一遍又一遍地使用相同的数据，我们会一遍又一遍地得到相同的散列，我们的散列将永远无效。你是对的，我们在 hash 中使用了一种叫做 *nonce* 的东西。它只是一个数字，每当散列无效时，我们就增加这个数字。我们得到我们的数据(日期、消息、以前的散列、索引)和一个 nonce 1。如果我们得到的散列无效，我们尝试使用 nonce 2。并且我们递增 nonce，直到我们获得有效的散列。

*   创世街区

他们肯定是我们链条中的第一个环节。它被称为创世纪板块。当然，这个块不能使用前一个块的 hash，因为它不存在。我们只是给它一些任意的数据来创建它的散列。

这正是我们的区块链所需要的。

## 战法

我们需要一些方法来制作功能性区块链:

*   初始化我们的区块链= >创建创世块
*   散列我们的块= >一个负责创建有效散列的函数
*   检查哈希的有效性= >我们的哈希以‘OOOO’开头吗？
*   获取最后一个散列= >我们需要前一个散列来创建一个新块
*   添加一个新的块= >如果我们想要一个链，我们需要在一点上这样做

## 咕咕！！

让我们开始编码吧。

对于这个小项目，我将创建两个文件，一个名为 *index.js* ，另一个名为 *blockchain.js* 。第二个将持有我们的小模块，以创建一个区块链。很直白，我们来看看:

```
const sha256 = require('js-sha256').sha256

const blockchain = (function(){
  const blocks = []

  const initBlockchain = () => {
    const data = 'Hello World!'
    const timestamp = new Date()
    const previousHash = 0
    const index = 0
    hashBlock(data, timestamp, previousHash, index)
  }

  const hashBlock = (data, timestamp, prevHash, index) => {
    let hash = '', nonce = 0

    while( !isHashValid(hash) ){
      let input = `${data}${timestamp}${prevHash}${index}${nonce}`
      hash = sha256(input)
      nonce += 1
    }
    console.log(nonce)
    blocks.push(hash)
  }

  const getLastHash = blocks => blocks.slice(-1)[0]

  const isHashValid = hash => hash.startsWith('0000') // Difficulty

  const addNewBlock = data => {
    const index = blocks.length
    const previousHash = getLastHash(blocks)
    hashBlock(data, new Date(), previousHash, index)
  }

  const getAllBlocks = () => blocks

  return {
    initBlockchain,
    getLastHash,
    blocks,
    getAllBlocks,
    addNewBlock
  }
})()

module.exports = blockchain 
```

Enter fullscreen mode Exit fullscreen mode

所以，在这个模块里，我有几个方法。在顶部，我导入了处理加密部分的模块。我有一个空数组来存放我的区块链块，称为*块*。

*initBlockchain* :该方法通过创建第一个块(genesis 块)来启动区块链。我给它一个时间戳、一条消息、块在区块链(0)中的索引和一个任意的先前散列，因为在链中还没有先前的块。有了这些信息，我现在可以创建创世块的散列了。

*hashBlock* :这个方法获取所有块的数据并创建一个 hash。正如您所看到的，我们第一次为一个特定的块运行函数时，*随机数*被设置为 0。我们加密我们的块，并用 *isHashValid* 检查散列是否有效。在我们的例子中，如果前四个字符是 0，散列就是有效的。这就叫难度。这是我们必须解决的问题，以确保这个街区能够成为区块链的一部分。一旦散列有效，我们就把它添加到 blocks 数组中。

*addNewBlock* :这个方法负责创建一个新的块。我们只需要将消息作为参数提供给它，因为所有其他参数(索引、previousHash 和时间戳)都可以在区块链中找到。该方法用数据调用 *hashBlock* 来创建和验证新的块。

*getLastHash* :我调用的获取前一个 Hash 的方法。我们总是需要先前的散列来创建新的块。

*getAllBlocks* :只返回当前区块链中的所有块

太好了，让我们转到 *index.js* 使用我们的新区块链吧！

```
const blockchain = require('./blockchain')

blockchain.initBlockchain()
blockchain.addNewBlock('First new block')
blockchain.addNewBlock('I love blockchains')
blockchain.addNewBlock('Make me a new hash!!')

console.log(blockchain.getAllBlocks()) 
```

Enter fullscreen mode Exit fullscreen mode

我们初始化区块链，然后创建三个新块。当我运行它时，我得到如下的响应链:

```
Initializing the blockchain
139355
30720
68789
51486
[ '0000d87875f12e8c00d60cdfc8c21c4867eb1e732d3bb0e4d60bd0febcfafbaf',
  '0000331d80f4e83461bad846e082baa08c5e739edfa19a4880c1dcbe4eed1984',
  '00000dcab247410050e357158edc20555cc0110429023fdadb1d8cda3e06da5e',
  '0000a16968811cf75c33d877e99f460d396c46b5485f669c8e55b193b862106d' ] 
```

Enter fullscreen mode Exit fullscreen mode

该数组代表四个块。如你所见，每一个都以四个零开始，所以每一个散列都是有效的。如果其中一个散列不是以四个零开始，我会马上知道该散列是无效的，因此，相应块中的数据可能不可信。

这里有四个号码: *139355* 、 *30720* 、 *68789* 、 *51486* 。这些是每个块的*随机数*。我把它们打印出来，看看函数 *hashBlock* 运行了多少次才得到一个有效的散列。

第一个块，genesis 块，运行了 139355 次才得到一个有效的散列！第二次，30720 次。第三次 68789 次，第四次 51486 次。

## 结论

这是一个非常简单的区块链的例子。我很确定我错过了一些东西。我也保持事情相当简单，因为嘿，我正在学习！这个小项目让我明白了一些事情:

*   如果一个人决定修改前一个程序块，她必须修改后一个程序块的每一个程序块。每个块都继承自其父块(之前的散列)，所以试图欺骗区块链似乎很复杂。

*   但是，如果大多数区块链的用户决定作弊，他们可以修改以前的区块，并同意相应地改变区块链的其余部分。区块链似乎只有在大多数人决定遵守规则的情况下才行得通。或者您可能会得到两个不同的区块链，一个用户决定使用原始数据，另一个用户决定使用修改后的区块链。

*   我听说过比特币在采矿方面的巨大用途。挖掘是解决加密数据时的困难问题的概念。您获得事务，并尝试为该块找到有效的散列。作为对你努力的奖励，你会得到一些比特币。我只能想象当区块链变得巨大时你会使用多少能量。

嗯，这大概就是我从那得到的。这让我明白了很多事情。如果我做错了，请随时纠正我！