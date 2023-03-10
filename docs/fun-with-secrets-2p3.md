# 秘密的乐趣

> 原文：<https://dev.to/terceranexus6/fun-with-secrets-2p3>

计算机科学中的安全性是每个个人和公司都非常重视的问题。通信(人与人之间或人际网络或任何在线通信)容易被监听或操纵。

例如，使用 **http** 而不是 **https** 是不安全的(有时甚至被你的浏览器标记为不可信的)，因为通过它的信息没有加密，有人能够用收集的数据冒充你的账户。这也是为什么安全浏览推荐 [**https 无处不在**](https://www.eff.org/es/https-everywhere) 和 [**隐私獾**](https://www.eff.org/es/privacybadger) 的原因。

但是当我们说“*加密*，我们指的是什么呢？

早在计算机还不存在的时候，密码学就已经存在了。数学一直在保护我们的交流。有时是一封信，有时是一张便条或一个信使，但是有许多隐藏信息的机智方法。

例如，“*单表位替换系统*是一个双射应用

```
 e: A -> A 
```

Enter fullscreen mode Exit fullscreen mode

作为一个字母 A a*任意长度 A 上的链聚集:

```
 e: A* -> A*, [e(X0 X1 ...) = e(X0) e(X1)...] 
```

Enter fullscreen mode Exit fullscreen mode

这方面的一个例子是塞萨尔密码。这包括向右的循环位移，数学上(位移= +3):

```
 e: Z23 -> Z23, [e(x) = x+3 (mod 23)] 
```

Enter fullscreen mode Exit fullscreen mode

注意是 Z23，因为罗马字母长度= 23。

不幸的是，这种方法(你小时候可能用过)有一个巨大的安全漏洞:**字母周期分析**。让我解释一下。每一种语言都有一项研究表明了字母在这种语言中出现的周期性。即使字母是混合的，如果我们用这个数字(例如，在英语中，最常用的字母是 E 或 T)我们就能猜出信息。

还有一个类似的密码版本叫做“*多字母替换系统*”。这个系统使用一个关键字来加密所有的信息。(就像重复了很多次单阿尔法一样)。让我给你看一个例子:

我们的关键词是燕尾服，我们的信息是帮助我欧比万·克诺比

我们用数字标记字母表，就像这样:

| A | B | C | ... | X | Y | Z |
| --- | --- | --- | --- | --- | --- | --- |
| one | Two | three | ... | Twenty-three | Twenty-four | Twenty-five |

所以 TUX 等于 19，20，23
并且帮助我奥比万克诺比等于 7，4，11，15，12，4，14，1，8，22，0，13，10，4，13，14，1，8

现在我们在消息中设置 TUX 中的数字，就像这样:

| H | E | L | P | M | E | O | B | 我 | W | A | 普通 | K | E | 普通 | O | B | 我 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| T | U | X | T | U | X | T | U | X | T | U | X | T | U | X | T | U | X |
| Nineteen | Twenty | Twenty-three | Nineteen | Twenty | Twenty-three | Nineteen | Twenty | Twenty-three | Nineteen | Twenty | Twenty-three | Nineteen | Twenty | Twenty-three | Nineteen | Twenty | Twenty-three |

现在，我们将设置的值添加到原始值中(在 mod 25 中)。

| H | E | ... | B | 我 |
| --- | --- | --- | --- | --- |
| 7 + 19 | 4 + 20 | ... | 1 + 20 | 8 + 23 |
| A | Y | ... | V | F |

所以 help meobiwankenobi = AYIIGBHVFPUKDYKHVF

这个系统稍微复杂一点，但是如果我们知道关键字长度，也容易受到周期性分析**的影响。**

我们还有希尔密码，它存在于矩阵密码中。

例如，我们可以用密匙矩阵= ([32]，[15])对单词 MATH 进行加密

字母表中的 m，A = ([12]，[0])T，H = ([10]，[7])

```
 ([32],[15])([12],[0]) = ([0],[12])
    ([32],[15])([10],[7]) = ([71],[54]) = ([35][18]) **in mod 36 (alphanumeric)** 
```

Enter fullscreen mode Exit fullscreen mode

所以 MATH = AM95

这个系统比其他系统安全多了。还有所谓的换位系统，其中包括改变字母顺序(周期性分析在这里也失败了)。例如:

[![](img/11e02d5190f6a88d0a1a75c6eee96ea3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--D1dngYQK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://image.slidesharecdn.com/cryptography101-140813084146-phpapp01/95/cryptography-from-demaratus-to-rsa-4-638.jpg%3Fcb%3D1407919684)

这种密码在二战中与一次性密码本和笔记本密码一起被广泛使用。

一般来说，密码系统必须遵循某些规则。例如，秘密必须隐藏在算法中，算法的力量在于它的形式，而不是算法对公众隐藏的方式。(这是一些用户使用私有加密的主要问题)。大多数数学规则可以在保密系统的*通信理论中找到，这是 C.E .香农关于此事的一项研究。当前的加密是以比特而不是字母工作的，最近的密码学研究正在发展量子密码术，为即将到来的量子计算机做准备。这可能意味着常规计算机密码术的完全混乱，我们应该警惕！*

 *与此同时，我们可以继续用基础密码写情书和情书。据说这种方法在唐璜身上奏效了，唐璜在破译了一个女人与维格内尔之间的密码后，让一个女人爱上了他。*