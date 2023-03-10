# Ruby 中非常简单的区块链实现

> 原文：<https://dev.to/takp/very-simple-blockchain-implementation-in-ruby-2129>

## 1。为什么我用 Ruby 实现它

我用 Ruby 创建了简单版的区块链。

这是因为我读了这篇文章[“通过建造一个来了解区块链”](https://hackernoon.com/learn-blockchains-by-building-one-117428612f46)，我对此印象深刻。它用不到 200 行代码用 Python 创建了简单的区块链。然后我想为什么不用 Ruby 呢？

对于工程师来说，通过编写代码来创建简单的东西更容易弄明白，不是吗？

## 2。在 Ruby 中实现区块链

用 Ruby 一个一个实现就行了。

完整版->[https://github.com/takp/blockchain-ruby](https://github.com/takp/blockchain-ruby)

它看起来很长，因为它都在一个文件中，但它很简单，不到 200 行代码。

## 3。奔跑

### 3.1 运行准备

git 将其克隆到本地后，运行`bundle install`并安装必要的 gem。

```
$ git clone git@github.com:takp/blockchain-ruby.git
$ cd blockchain-ruby/
$ bundle install

$ bundle exec ruby blockchain.rb
[2018-01-09 21:49:25] INFO WEBrick 1.4.2
[2018-01-09 21:49:25] INFO ruby 2.5.0 (2017-12-25) [x86_64-darwin16]
== Sinatra (v2.0.0) has taken the stage on 4567 for development with backup from WEBrick
[2018-01-09 21:49:25] INFO WEBrick::HTTPServer#start: pid=12067 port=4567 
```

Enter fullscreen mode Exit fullscreen mode

这将启动 Sinatra，并准备好接受请求集。

### 3.2 区块链的状态

您可以通过请求`GET /chain`来了解区块链的状态。此时，什么都不做，所以它只返回 genesis 块。

```
$ curl http://localhost:4567/chain

{
  "chain":[
    {
      "index":1,
      "timestamp":"2018-01-09 21:59:51 +0700",
      "transactions":[],
      "proof":1,
      "previous_hash":100
    }
  ],
  "length":1
} 
```

Enter fullscreen mode Exit fullscreen mode

### 3.3 采矿！

您可以通过请求`GET /mine`来执行采矿。从响应中，您可以看到哈希值被添加到其中。

```
$ curl http://localhost:4567/mine

{
  "message":"New Block Forged",
  "index":2,
  "transactions":[
    {
      "sender":"0",
      "recipient":"caedf4a87f7841838061dd7dffca2916",
      "amount":1
    }
  ],
  "proof":94813,
  "previous_hash":"84e609b88e68764ac4546cb807d7cf0e"
} 
```

Enter fullscreen mode Exit fullscreen mode

### 3.4 添加新交易

您可以通过请求`POST /transactions/new`添加新交易。交易内容由`curl`的`-d`选项发送。

```
$ curl -X POST -H "Content-Type: application/json" -d '{
 "sender": "d4ee26eee15148ee92c6cd394edd974e",
 "recipient": "someone-other-address",
 "amount": 5
}' "http://localhost:4567/transactions/new"

{
  "message":"Transaction will be added to Block 3"
} 
```

Enter fullscreen mode Exit fullscreen mode

在这里，再次进行采矿，并为区块链增加更多的交易。

```
$ curl http://localhost:4567/mine
{
  "message":"New Block Forged",
  "index":3,
  "transactions":[
    {
      "sender":"d4ee26eee15148ee92c6cd394edd974e",
      "recipient":"someone-other-address",
      "amount":5
    },
    {
      "sender":"0",
      "recipient":"caedf4a87f7841838061dd7dffca2916",
      "amount":1
    }
  ],
  "proof":183390,
  "previous_hash":"91d2d1296158c7faf58a37ddc152593d"
} 
```

Enter fullscreen mode Exit fullscreen mode

### 3.5 检查区块链的状态

尽管响应看起来更长，但让我们再次研究它。你可以发现第一个街区到第三个街区是连锁的。

```
$ curl http://localhost:4567/chain
{
  "chain":[
    {
      "index":1,
      "timestamp":"2018-01-09 22:04:06 +0700",
      "transactions":[],
      "proof":1,
      "previous_hash":100
    },
    {
      "index":2,
      "timestamp":"2018-01-09 22:04:11 +0700",
      "transactions":[
        {
          "sender":"0",
          "recipient":"caedf4a87f7841838061dd7dffca2916",
          "amount":1
        }
      ],
      "proof":94813,
      "previous_hash":"84e609b88e68764ac4546cb807d7cf0e"
    },
    {
      "index":3,
      "timestamp":"2018-01-09 22:06:40 +0700",
      "transactions":[
        {
          "sender":"d4ee26eee15148ee92c6cd394edd974e",
          "recipient":"someone-other-address",
          "amount":5
        },
        {
          "sender":"0",
          "recipient":"caedf4a87f7841838061dd7dffca2916",
          "amount":1
        }
      ],
      "proof":183390,
      "previous_hash":"91d2d1296158c7faf58a37ddc152593d"
    }
  ],
  "length":3
} 
```

Enter fullscreen mode Exit fullscreen mode

## 4。反射

用简单的代码写下来**非常直观**。我们可以知道它如何添加事务，并通过计算哈希值来确认块。

在最初的版本中，有共识实现，但我跳过了它。能实现就实现！

Github 中的存储库:[https://github.com/takp/blockchain-ruby](https://github.com/takp/blockchain-ruby)

[@takp](https://takp.me/about)