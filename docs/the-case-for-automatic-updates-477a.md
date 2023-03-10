# 自动更新的情况

> 原文：<https://dev.to/paragonie/the-case-for-automatic-updates-477a>

一旦您客户的网络中存在安全漏洞，防止安全漏洞涉及许多活动部件，但最重要的是:

1.  在罪犯之前识别安全漏洞。
2.  修复您发现的安全漏洞。
3.  在客户的所有网络中部署补丁程序。

考虑下面的时间表:

[![The Life and Death of a Software Vulnerability (Timeline)](img/10afc6bcdabec63047aca5d3dada8c8f.png "The Life and Death of a Software Vulnerability. Time 0: Vulnerability Introduced; Time 1: Vulnerability Identified; Time 2: Vulnerability Fixed; Time 3: Fix deployed")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cFMr7W9G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://paragonie.com/files/blog/auto-updates.png)

点 0 和点 1 之间的时间可能是几年。对于 Linux 内核，平均大约是 5 年。有很多方法可以减少这个数量，其中大部分涉及自动化测试和安全专家的手工代码审查。

点 1 和点 2 之间的时间可能是数周或数月。我们的平均漏洞识别-修复时间窗口少于 24 小时。大多数组织不具备同样的灵活性或专业知识。

大多数开发团队无法控制第 2 点和第 3 点之间的时间——在更新可用之后，应用补丁(以及消除漏洞)所需的时间。

通过手动更新而不是自动更新，你迫使你的客户承担所有责任，确保你的错误不会损害他们的业务。只有很少一部分客户可能更愿意自己负责验证和应用每个更新。

总的来说，大多数人没有安全意识、时间管理和纪律来承担这个责任。

自动安全更新将点 2 和点 3 之间的间隔从**可能是无限的**减少到**几乎为零。**与手动补丁管理相比，这显然是一项有意义的改进。

### 过时的软件有什么实际风险？

信息安全行业对过时软件的问题进行了深入研究。根据[威瑞森 2015 年数据泄露调查报告(PDF)](https://msisac.cisecurity.org/whitepaper/documents/1.pdf) 举例来说，当一个软件漏洞被利用...

> 在 CVE 发布一年多之后，99.9%被利用的漏洞受到威胁*。*

2016 年，袋熊安全公司在一篇题为[过时软件和插件增加最终用户风险](https://info.wombatsecurity.com/blog/out-of-date-software-and-plug-ins-compound-end-user-risk)的文章中对类似研究进行了反思。一篇关于 Help Net Security 的文章研究了 F-Secure 的发现，并回应了[公司依赖过时软件并将其用户置于风险之中的问题](https://www.helpnetsecurity.com/2014/04/02/the-dangers-of-using-outdated-software/)。

数据和简单的逻辑都支持过时软件的危险:如果犯罪分子意识到软件产品中的特定漏洞，如果使用其产品的大多数公司在犯罪分子想要利用该产品时仍然容易受到攻击，那么供应商发布安全补丁也没有关系。

### 看起来如此明显，但是...

如果现实世界中 99.9%的软件漏洞是可以通过保持软件最新来预防的，并且未打补丁的漏洞的危险在安全专业人士中是众所周知的，那么显而易见的问题是，“为什么不是所有东西都自动更新自己？”

实现安全的自动更新机制是一项非同寻常的工程壮举，大多数程序员甚至不知道从哪里着手。通常那些认为他们了解的人对风险和复杂性一无所知。

## 简单安全的自动软件更新

首先，确保你的可交付成果可以从源代码中重现。如果您正在使用从未编译成二进制代码的脚本语言，这仅仅要求您的软件是开源的。

第二，使用实施代码签名的更新框架(即[更新框架](https://theupdateframework.github.io/))。这意味着您的更新文件必须由一个仅由您控制的私钥进行签名，但是任何人都可以用您的公钥进行验证。

> 如果你不明白“私钥”或“公钥”是什么意思，我们的另一篇文章是对密码学术语和概念的通俗易懂的介绍，将会对此有所启发。

最后，运行一个[历史记录](https://paragonie.com/blog/2017/07/chronicle-will-make-you-question-need-for-blockchain-technology)实例。每次发布更新时，将新的发布信息发布到您的编年史中。使与更新框架交互的代码验证您看到的版本也发布在编年史中(或者，特别是对于企业客户，他们自己的驻留在公司网络上的编年史副本)。

就是这样。更新框架(或者与你的栈相关的类似实现)和**历史**是你所需要的(就工具而言)。让你的软件开源，让你的构建可复制，你将在空间*和*时间上大大减少客户的攻击面。

### PHP 中的安全自动软件更新

目前还没有更新框架的 PHP 实现。如果有足够的社区兴趣，我们可能会承诺在未来建立一个。然而，这可能没有必要。

如果您正在开发现代 PHP，您几乎肯定会使用 Composer 和 Packagist。如果没有，强烈建议你尽快学习。

2017 年，我向 [Packagist 团队](https://github.com/composer/packagist/issues/797)提出了一项建议，运行他们自己的 Chronicle 实例，该实例将用于实时发布软件发布的信息。我们正在研究其他提案，以加强签名验证并解决公钥基础设施(PKI)问题。

换句话说:如果你正在使用 Composer，那么在不久的将来，这可能已经是一个为你解决的问题了。

### 面向嵌入式设备和物联网的安全自动更新

嵌入式开发面临着独特的挑战，在实现安全自动更新协议方面还没有太多的指导，特别是对于所谓的“智能设备”。由于内存或电源使用要求较低，在不耗尽整个电源或内存预算的情况下，仅将加密技术应用到产品设计中通常是不可行的。

对于极度受限的设备， [libhydrogen](https://github.com/jedisct1/libhydrogen) 是一个很有吸引力的选择。它非常轻量级，当前的实现只使用了两个原语来提供一个全功能的加密库:吉姆利置换和曲线 25519。

* * *

这篇帖子合并了[Paragon Initiative Enterprises 博客](https://paragonie.com/blog)上几篇关于自动更新的帖子的重要部分。如果你对[构建安全的 PHP 软件](https://paragonie.com/blog/2017/12/2018-guide-building-secure-php-software)感兴趣，可以看看我们的网站。