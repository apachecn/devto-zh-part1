# 密码展示和讲述:迪菲-赫尔曼-默克尔的奇迹

> 原文：<https://dev.to/dwd/crypto-show-and-tell-the-wonders-of-diffie-hellman-merkle-2jah>

我对密码学几乎一无所知。我经常使用密码学，我对了解 RSA 等的属性相当有信心，我甚至设计了认证机制。但是这些东西实际上是如何工作的呢？是的，我一无所知。

于是我踏上了有趣的发现之旅。我希望你能加入。今天的课是在 Diffie-Hellman(有时，更恰当地说，被称为 Diffie-Hellman-Merkle)。这将充满错误，因为——正如我们已经确定的那样——我对此毫无头绪。

## 第一步:想一个数字

Diffie-Hellman 是一种密钥协商协议——一种让双方在不安全的信道上就某个特定值达成一致的方式，而其他任何人都不知道它是什么。一旦你做到了这一点，你就可以用这个密钥用 AES 或其他什么加密信息。

它是建立在一个交换群的基础上的，这个交换群显然是数学中的一个东西，由一个集合组成，在这个集合中，任意两个成员可以通过某种运算组合在一起，并产生另一个成员。在 Diffie-Hellman 中，原则上你可以使用任何有难以逆转的运算的交换群。

现在，我将使用 Python，而不是用数学符号来描述这一切，输入 Markdown 将是一场噩梦。Python 有助于支持“大”数字，所以这段代码将达到生产强度 DH——但是你真的不想在那个级别上尝试，所以我们将使用小得多的数字。

对于经典 DH，我们首先需要一些常数。这些是迪菲·赫尔曼的参数。我们需要一个生成器(它总是一个很小的数，比如 2 或 5)和一个质数(它是一个质数，应该很大，比如 2048 或 4096 位)。顺便说一下，这些数字不需要保密——事实上，它们通常在第一步就在网络上共享了。

```
 G = 2  # This is fine. 
P = 13  # Should be MUCH bigger. 
```

Enter fullscreen mode Exit fullscreen mode

好的开始。现在，我们需要一种在集合中获取随机数的方法。从这里你会对集合定义有一个很好的了解:

```
def gen_key():
    '''Generate a random integral number from 1 to P-1 inclusive.'''
    global P
    import random
    return random.randrange(1, P) 
```

Enter fullscreen mode Exit fullscreen mode

我们称之为`gen_key`的事实可能会给你一个提示——我们用它来生成一个 DH 私钥。

现在，我们只需要手术。这真的很简单。

```
def op(a):
   global P
   global G
   return (G ** a) % P 
```

Enter fullscreen mode Exit fullscreen mode

嗯，太棒了。将 G 提升到 a 的幂，然后将结果以 P 为模(即除以 P 并保留余数)。现在可以用`Ka = gen_key()`做一个私钥，然后用`Pa = op(Ka)`创建对应的公钥。简单。

那么我们能用这个做什么呢？嗯，不是很多，因为我们必须让那个`op`函数变得更好一点——有时，我们想使用 g 以外的东西

```
def op(a, b=None):
   global P
   global G
   e = b if b is not None else G
   return (e ** a) % P 
```

Enter fullscreen mode Exit fullscreen mode

现在，看这个魔术。让我们做一会儿爱丽丝，说我们想与鲍勃商定一把钥匙。Alice 需要一个私钥和一个公钥。

```
Ka = gen_key()
Pa = op(Ka) 
```

Enter fullscreen mode Exit fullscreen mode

鲍勃也有同样的对子:

```
Kb = gen_key()
Pb = op(Kb) 
```

Enter fullscreen mode Exit fullscreen mode

Alice 将她的公钥发送给 Bob，反之亦然。现在，魔法。爱丽丝是这样做的:

```
result = op(Ka, Pb) 
```

Enter fullscreen mode Exit fullscreen mode

鲍勃做了相反的动作:

```
result = op(Kb, Pa) 
```

Enter fullscreen mode Exit fullscreen mode

现在`result`两边是一样的。

这里的数学很简单，一点也不神奇。没有动物被牺牲，没有粉笔标记是必要的。如果你愿意，你可以点蜡烛，但不必是那种黑色滴滴答答的蜡烛。

爱丽丝这样计算:

```
((G ** Kb) % P) ** Ka) % P 
```

Enter fullscreen mode Exit fullscreen mode

与
相同

```
((G ** Kb) ** Ka) % P 
```

Enter fullscreen mode Exit fullscreen mode

哪只是:

```
(G ** (Ka * Kb)) % P 
```

Enter fullscreen mode Exit fullscreen mode

因为这就是指数的工作方式。`Ka * Kb`当然和`Kb * Ka`一样，所以 Bob 的计算也是一样的。只是两种情况下内项都是对等体的公钥。

近了！

## 第二步:我们都要去度暑假

好的，那么爱丽丝和鲍勃可以一起找到一把钥匙。(我的意思是，如果他们真的在互相交谈，而不是和一个 MITM 人交谈，但那是一个完全不同的问题)。

但是假设 Alice 要去度假，并且想要将她的消息重定向到 Carol？她当然可以给卡罗尔她的私人钥匙。但那是垃圾，所以她只给了 Carol 密钥的一部分，而把另一部分给了她的服务器。

好吧，这会变得很奇怪。

所以我们有 Alice 的公钥，这就是 Bob 看到的。那么 Alice 将拥有一个私钥。然后，Carol 也将拥有 Alice 的私钥，服务器将拥有 Carol-as-Alice 的重新加密密钥。

```
Ka = gen_key()  # Private, only Alice has this. Pa = op(Ka)  # Public, everyone has this. 
Kca = gen_key() % Ka  # Clamp it to Ka. Carol gets this from Alice. Rca = Ka - Kca  # Yes, we're really just splitting it. The server gets this from Alice 
Kb = gen_key()  # Private, only Bob has this. Pb = op(Ka)  # Public. 
```

Enter fullscreen mode Exit fullscreen mode

好了，一切就绪。所以现在 Bob 想给 Alice 发送一条消息，并像以前一样同意了一个密钥:

```
result = op(Kb, Pa) 
```

Enter fullscreen mode Exit fullscreen mode

爱丽丝很容易得到同样的结果。但是她的服务器在发送给卡罗尔之前会这样做:

```
half_result = op(Rca, Pb) 
```

Enter fullscreen mode Exit fullscreen mode

卡罗尔接着做剩下的事情

```
result = (op(Kca, Pb) * half_result) % P 
```

Enter fullscreen mode Exit fullscreen mode

令人惊讶的是，两种情况下的结果是一样的。然而，卡罗尔和服务器都没有爱丽丝的私钥。当然，鹰眼会发现他们合谋获取它是微不足道的——这不是一个伟大的代理再加密方案。

除非是。

## 第三步:台式机、笔记本电脑、平板电脑、手机、手表。

如今，我们有越来越多的设备。我真的有以上所有的，都在做客户的事情。

传统的密钥交换方法包括 Alice 和 Bob 为所有这些设备交换公钥(哎呀！)或者在所有这些设备上共享相同的密钥对(哎呀！)，或者让服务器拥有私钥(ICK！).

即使从上面的简单形式来看，代理再加密也是相当有用的。

因此，Alice 的所有设备都有一个密钥对，但只将私钥保存在她最信任的设备上，甚至可能保存在智能卡或其他设备上。她上传到服务器的公钥。

对于每个设备，Alice 生成再加密密钥和相应的私钥，将再加密密钥上传到服务器，并将私钥传送到设备。

现在，Bob 发送到服务器的消息可以分散到每台设备，没有共享密钥，服务器也没有办法解密。当然，任何设备都可以与服务器串通来获取 Alice 的私钥。但这是可以接受的，因为她拥有这些设备，并且已经有了私钥。

## 第四步:盈利

据我所知，这些都不受专利保护。你不应该相信我的话。但是，如果您想在生产中进行 DH，请记住:

*   你想用大质数。OpenSSL 有一些工具可以为你找到合适的参数。
*   你可能想看看 ECDH，DH 的椭圆曲线变体。但是我不知道如何在那里进行代理重新加密——尽管这可能会很简单。