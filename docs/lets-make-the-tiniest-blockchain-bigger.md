# 让我们把最小的区块链变大

> 原文：<https://dev.to/aunyks/lets-make-the-tiniest-blockchain-bigger>

[![Banner](img/500bf56a1abb30b8e7067848781b1e8b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FuCZZj3I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AUBjF_ql6iUiT6bWRiwDpNg.jpeg)

*注意:本文假设你已经阅读了[第一部分](https://medium.com/crypto-currently/lets-build-the-tiniest-blockchain-e70965a248b)。*
最小的区块链极其简单，也相对容易制作。但是，它的简单也带来了一些缺陷。第一，SnakeCoin 只在一台单机上运行，所以远谈不上分布式，更谈不上去中心化。第二，可以像主机创建 Python 对象并将其添加到列表一样快地将块添加到链中。在一个简单的区块链的情况下，这不是一个问题，但我们现在要让 SnakeCoin 成为一种真正的加密货币，所以我们需要控制一次可以创建的块(和硬币)的数量。

从现在开始，SnakeCoin 的数据将是交易，因此每个块的数据字段将是一些交易的列表。我们将如下定义一个事务。每笔交易都是一个 JSON 对象，详细描述了硬币的发送者、接收者以及被转移的 SnakeCoin 的数量。*注意:事务是 JSON 格式的，原因我将很快详述。*

```
{
  "from": "71238uqirbfh894-random-public-key-a-alkjdflakjfewn204ij",
  "to": "93j4ivnqiopvh43-random-public-key-b-qjrgvnoeirbnferinfo",
  "amount": 3
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们知道了我们的事务是什么样子，我们需要一种方法将它们添加到我们区块链网络中的一台计算机上，称为节点。为此，我们将创建一个简单的 HTTP 服务器，以便任何用户都可以让我们的节点知道发生了新的事务。一个节点将能够接受一个以事务(如上)作为请求体的 POST 请求。这就是为什么事务是 JSON 格式的；我们需要在请求体中将它们传输到我们的服务器。

```
pip install flask # Install our web server framework first 
```

Enter fullscreen mode Exit fullscreen mode

```
from flask import Flask
from flask import request
node = Flask(__name__)

# Store the transactions that
# this node has in a list this_nodes_transactions = []

@node.route('/txion', methods=['POST'])
def transaction():
  if request.method == 'POST':
    # On each new POST request,
    # we extract the transaction data
    new_txion = request.get_json()
    # Then we add the transaction to our list
    this_nodes_transactions.append(new_txion)
    # Because the transaction was successfully
    # submitted, we log it to our console
    print "New transaction"
    print "FROM: {}".format(new_txion['from'])
    print "TO: {}".format(new_txion['to'])
    print "AMOUNT: {}\n".format(new_txion['amount'])
    # Then we let the client know it worked out
    return "Transaction submission successful\n"

node.run() 
```

Enter fullscreen mode Exit fullscreen mode

厉害！现在我们有一种方法来记录用户互相发送蛇币的时间。这就是为什么人们称区块链为公共的分布式账本:所有的交易都存储在网络的每个节点上，供所有人查看。

但是，一个问题出现了:人们从哪里得到蛇币？还没有。现在还没有蛇币这种东西，因为还没有一种硬币被创造和发行。为了创造新的硬币，人们不得不*开采*新的蛇币块。当他们成功开采新的区块时，一种新的蛇币就会产生并奖励给开采该区块的人。一旦矿工将蛇币发给另一个人，硬币就开始流通。

我们不希望开采新的蛇币块太容易，因为那会创造太多的蛇币，它们将没有什么价值。反过来，我们不希望开采新的区块太难，因为没有足够的硬币供每个人消费，而且对我们来说太贵了。为了控制挖掘新蛇币的难度，我们将实现一个[工作证明](https://en.bitcoin.it/wiki/Proof_of_work) (PoW)算法。工作验证算法本质上是一种生成难以创建但易于验证的项目的算法。这个项目被称为证据，正如它听起来的那样，它证明了计算机执行了一定量的工作。

在 SnakeCoin 中，我们将创建一个有点简单的工作证明算法。要创建一个新的区块，矿工的计算机必须增加一个数字。当这个数字被 9(SnakeCoin 中的字母数)和最后一块的证明数整除时，将会开采出一个新的 SnakeCoin 块，矿工将会得到一个全新的 snake coin。

```
# ...blockchain
# ...Block class definition 
miner_address = "q3nf394hjg-random-miner-address-34nf3i4nflkn3oi"

def proof_of_work(last_proof):
  # Create a variable that we will use to find
  # our next proof of work
  incrementor = last_proof + 1
  # Keep incrementing the incrementor until
  # it's equal to a number divisible by 9
  # and the proof of work of the previous
  # block in the chain
  while not (incrementor % 9 == 0 and incrementor % last_proof == 0):
    incrementor += 1
  # Once that number is found,
  # we can return it as a proof
  # of our work
  return incrementor

@node.route('/mine', methods = ['GET'])
def mine():
  # Get the last proof of work
  last_block = blockchain[len(blockchain) - 1]
  last_proof = last_block.data['proof-of-work']
  # Find the proof of work for
  # the current block being mined
  # Note: The program will hang here until a new
  #       proof of work is found
  proof = proof_of_work(last_proof)
  # Once we find a valid proof of work,
  # we know we can mine a block so 
  # we reward the miner by adding a transaction
  this_nodes_transactions.append(
    { "from": "network", "to": miner_address, "amount": 1 }
  )
  # Now we can gather the data needed
  # to create the new block
  new_block_data = {
    "proof-of-work": proof,
    "transactions": list(this_nodes_transactions)
  }
  new_block_index = last_block.index + 1
  new_block_timestamp = this_timestamp = date.datetime.now()
  last_block_hash = last_block.hash
  # Empty transaction list
  this_nodes_transactions[:] = []
  # Now create the
  # new block!
  mined_block = Block(
    new_block_index,
    new_block_timestamp,
    new_block_data,
    last_block_hash
  )
  blockchain.append(mined_block)
  # Let the client know we mined a block
  return json.dumps({
      "index": new_block_index,
      "timestamp": str(new_block_timestamp),
      "data": new_block_data,
      "hash": last_block_hash
  }) + "\n" 
```

Enter fullscreen mode Exit fullscreen mode

现在可以控制一定时间段内开采的块数，可以发行新币让网络中的人互相发送。但是就像我们说的，我们只在一台计算机上做这个。如果区块链是分散的，我们如何确保相同的链在每个节点上？为了做到这一点，我们让每个节点向其他节点广播它的链版本，并允许它们接收其他节点的链。之后，每个节点都必须验证其他节点的链，这样网络中的每个节点才能对最终的区块链达成一致。这被称为[共识算法](https://en.wikipedia.org/wiki/Consensus_%28computer_science%29)。

我们的一致性算法相当简单:如果一个节点的链不同于另一个节点的链(即存在冲突)，那么网络中最长的链会保留，而所有较短的链会被删除。如果我们网络中的链之间没有冲突，那么我们继续下去。

```
@node.route('/blocks', methods=['GET'])
def get_blocks():
  chain_to_send = blockchain
  # Convert our blocks into dictionaries
  # so we can send them as json objects later
  for block in chain_to_send:
    block_index = str(block.index)
    block_timestamp = str(block.timestamp)
    block_data = str(block.data)
    block_hash = block.hash
    block = {
      "index": block_index,
      "timestamp": block_timestamp,
      "data": block_data,
      "hash": block_hash
    }
  # Send our chain to whomever requested it
  chain_to_send = json.dumps(chain_to_send)
  return chain_to_send

def find_new_chains():
  # Get the blockchains of every
  # other node
  other_chains = []
  for node_url in peer_nodes:
    # Get their chains using a GET request
    block = requests.get(node_url + "/blocks").content
    # Convert the JSON object to a Python dictionary
    block = json.loads(block)
    # Add it to our list
    other_chains.append(block)
  return other_chains

def consensus():
  # Get the blocks from other nodes
  other_chains = find_new_chains()
  # If our chain isn't longest,
  # then we store the longest chain
  longest_chain = blockchain
  for chain in other_chains:
    if len(longest_chain) < len(chain):
      longest_chain = chain
  # If the longest chain wasn't ours,
  # then we set our chain to the longest
  blockchain = longest_chain 
```

Enter fullscreen mode Exit fullscreen mode

我们马上就要完成了。运行完[完整 SnakeCoin 服务器代码](https://gist.github.com/aunyks/47d157f8bc7d1829a729c2a6a919c173)后，在您的终端中运行以下命令。*假设你安装了 cURL。*

1.  创建交易。

```
curl "localhost:5000/txion" \
     -H "Content-Type: application/json" \
     -d '{"from": "akjflw", "to":"fjlakdj", "amount": 3}' 
```

Enter fullscreen mode Exit fullscreen mode

1.  开采新的区块。

```
curl localhost:5000/mine 
```

Enter fullscreen mode Exit fullscreen mode

1.  看看结果。从客户端窗口，我们看到了这个。![Console Output](img/f03a0ba6e8eafd195e97432edf686aa9.png)

通过一点漂亮的打印，我们看到在采矿后，我们在新区块上获得了一些很酷的信息。

```
{
  "index": 2,
  "data": {
    "transactions": [
      {
        "to": "fjlakdj",
        "amount": 3,
        "from": "akjflw"
      },
      {
        "to": "q3nf394hjg-random-miner-address-34nf3i4nflkn3oi",
        "amount": 1,
        "from": "network"
      }
    ],
    "proof-of-work": 36
  },
  "hash": "151edd3ef6af2e7eb8272245cb8ea91b4ecfc3e60af22d8518ef0bba8b4a6b18",
  "timestamp": "2017-07-23 11:23:10.140996"
} 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！此时，我们已经制作了一个相当大的区块链。现在，SnakeCoin 可以在多台机器上启动，创建一个网络，可以开采真正的 snake coin。请随意修改[snake coin 服务器代码](https://gist.github.com/aunyks/47d157f8bc7d1829a729c2a6a919c173),问尽可能多的问题！**在下一部分，我们将讨论创建一个蛇币钱包，这样用户就可以发送、接收和存储他们的蛇币。**

[![Thank You](img/b43709364925eb0e0c9a1c62f738f5c7.png)T2】](https://i.giphy.com/media/3otPoUkg3hBxQKRJ7y/giphy.gif)

非常感谢您的阅读！
[Twitter](https://twitter.com/aunyks) ， [Github](https://github.com/aunyks) ， [Snapchat](https://snapchat.com/add/aunyks) ， [Medium](https://medium.com/@aunyks) ， [Instagram](https://instagram.com/aunyks)