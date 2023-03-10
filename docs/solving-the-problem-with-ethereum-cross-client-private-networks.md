# 解决以太坊跨客户端专用网络的问题

> 原文：<https://dev.to/5chdn/solving-the-problem-with-ethereum-cross-client-private-networks>

这个帖子最初是在[以太币栈交易所](https://ethereum.stackexchange.com/a/25640)上的一个回答，但是它值得更多的曝光，因为我一直在和一次又一次遇到这个问题的人交谈。而且，让一个 [Geth](https://github.com/ethereum/go-ethereum/wiki/geth) 节点与一个定制的[奇偶校验](https://github.com/paritytech/parity/wiki)网络对话是一件非常耗时的事情，反之亦然。

### 跨客户端私有网络:问题

Geth 是为 [以太坊](https://ethereum.org/)编写的客户端*。这是最早的官方参考实现之一，除了以太坊，它从来不打算运行其他任何东西。如果您想为任何其他链配置配置 Geth，您将不得不派生源代码并基于您想要的规则集创建您自己的客户机实现。这种情况发生在 expense([Gexp](https://github.com/expanse-org/go-expanse))和 Ethereum Classic ( [Getc](https://github.com/ethereumproject/go-ethereum) )上。*

然而，奇偶校验比以太坊本身晚得多，是由一个最初参与 C++以太坊客户端( [Eth](https://github.com/ethereum/cpp-ethereum) )的团队创建的。在他们(加文·伍德等人)创建了 Ethcore(现在的[平价科技](https://parity.io/))之后，他们创造了一个视野更加广阔的客户；一个不应该只运行以太坊的客户端。

奇偶校验允许在内核中进行更多的抽象，因此，启动一个新链所需要的只是一个所谓的*链规范*文件，它描述了整个链的参数，包括共识规则、验证器引擎等等。最明显的结果是，以太坊、以太坊经典和 Expanse(仅举上面的例子)不需要维护它们自己的奇偶校验源代码副本来支持它们的项目和它们的共识规则。奇偶校验只是使用`--chain`参数`foundation`、`classic`或`expanse`进行开箱即用。

此外，`--chain`参数允许配置一个[完全定制的链](https://github.com/paritytech/parity/wiki/Chain-specification),带有可插拔的 consenus、定制转换和您喜欢的验证器引擎。

相比之下，Geth 只允许指定一个*定制起源*，这只是完整链规范的一部分。因此您将很难将 Geth 节点连接到自定义的奇偶校验节点网络。但是，您基本上有两种选择来运行带有 Geth 和奇偶校验节点的自定义跨客户端网络，如下所述。

### 最佳实践:“先得到”

现在，由于 Geth 中的链配置选项比奇偶校验中的少，我将把最明显的想法称为“Geth 优先”。您选择了一个网络 ID 并创建了一个[自定义 Genesis](https://github.com/ethereum/go-ethereum/wiki/Private-network#creating-the-genesis-block) ，并用以下内容初始化您的新网络:

```
geth init geth-genesis.json 
```

Enter fullscreen mode Exit fullscreen mode

此时，您可以启动一个 miner 线程和几个 Geth 节点来验证网络是否完全按照您的要求运行。

现在，您可以开始集成奇偶校验节点。有一个小小的 rust 工具 [keorn/parity-spec](https://github.com/keorn/parity-spec) 可以将你定制的 Geth genesis 文件转换成一个完整的奇偶链规范文件。

```
cargo run -- geth-genesis.json 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用`--chain`参数将输出`parity-spec.json`直接传递到您的奇偶校验节点。

```
parity --chain parity-spec.json 
```

Enter fullscreen mode Exit fullscreen mode

对于发现，您可以使用 [Geth 的`admin.addPeer()`命令](https://github.com/ethereum/go-ethereum/wiki/Connecting-to-the-network#static-nodes)或[奇偶校验的`--reserved-peers`功能](https://ethereum.stackexchange.com/q/12928)。

然而，Geth 和 Parity 都是积极开发的代码库，转换器工具容易出现错误，用户经常报告生成的链规范文件仍然会导致共识问题。如果这经常发生在你身上，看看下面的另一个选项。

### 最佳实践:“Ropsten 复活-复活”

运行一个与 Geth、Parity 和所有其他客户端兼容的专用网络，可能是最稳定、最明显的选择，我称之为“Ropsten revealed-revealed”。

就拿一个所有客户端都支持的已知的工作网络来说吧，比如 [Ropsten public testnet](https://github.com/ethereum/ropsten/blob/master/revival.md) ，稍微修改一下。为了简化这个过程，我在[5 chdn/cross client-chains EPC](https://github.com/5chdn/crossclient-chainspec)为 Geth Genesis 和奇偶校验链规范创建了预置。它包含一个`geth.json`和一个`parity.json`模仿一个具有不同网络 ID 1337 ( `0x539`)的 Ropsten revival。

```
# Geth

geth init --datadir ~/.ethereum/crossclient geth.json
geth --datadir ~/.ethereum/crossclient --networkid 1337 --port 31333 --rpcport 8538

# Parity

parity --chain parity.json --port 31337 --jsonrpc-port 8539 
```

Enter fullscreen mode Exit fullscreen mode

这样做的缺点可能是无法满足向您的定制私有开发网络添加更复杂修改的愿望。然而，预定义的 genesis 和 chain-spec 提供了一个很好的起点，让您可以根据自己的想法引导任何网络。