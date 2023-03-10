# 黑得越多，学得越多，赚得越多，被邀请得越多。

> 原文：<https://dev.to/ij/hack-more-learn-more-earn-more-and-get-invited-more--1n92>

这篇博客不是为一个漏洞写的，而是所有黑客在报告一个漏洞时应该知道的。像 HackerOne 和 Bugcrowd 这样的所有平台对每个报告都有不同的处理方式:*不会修复、重复、不适用、信息丰富*等。这些都会反映在你得到的分数上，例如在 HackerOne 中，你的信号是你声誉的平均值，所以 N/A &提供的信息越多，你的信号就越少。这也会影响你的声誉。这是一个连锁反应，因为一旦它们全部关闭，旨在自动邀请黑客加入私人程序的算法将不会邀请信号和声誉较低的黑客。那么如何才能黑的多，学的多，赚的多，被邀请的多呢？以下是一些建议:

#### 如何接近目标？

虫子赏金都归结于理解你的目标。通常你没有应用程序的源代码，所以你不能仅仅指出一个漏洞。你必须了解应用程序如何工作，它有什么样的服务，以及应用程序中的权限是什么。为了更好的回答这个问题，我给你提供一个“完全”理论化的 app:

有一个叫 awesomecompany.com 的网络应用，他们最近推出了一个 bug bounty。他们允许你在成为认证用户后购买比特币。

那么，您将如何处理这个问题:

首先确保你理解应用程序。这意味着实际使用应用程序。现在还不要把自己当成黑客*。注意你使用的每个端点。试着理解他们做什么，例如:1)身份验证，2)买卖比特币，3)用于保护的账户 2FA，4)个人资料更新，5)当向其他用户买卖比特币时可以发送的消息。这是我能想到的几个漏洞。*

 *瞄准应用程序:

现在，一旦你有了可以发现什么的想法，让你的侦察工具发现其他漏洞，如子域接管，aws 桶，api 密钥和凭证，硬编码授权等。。

接下来，使用您先前的知识战略性地测试应用程序的关键部分，例如，如果您可以访问其他人的身份验证文件，该怎么办？你能在身份验证过程之外做一个 bXSS 吗？2FA 安检怎么做？你可以用 IDOR 更新别人的个人资料，并更改他们的电子邮件进行帐户接管吗？你能在转移比特币的时候用你发给某人的信息 XSS 他们吗？

一般来说，确保你至少使用该应用程序约 30 分钟至 1 小时。不要马上开始报告，确保在报告前检查影响和所有其他情况。

#### 如何报告 bug？

这是一个经常出现的问题。这里的主要问题是:什么是重要的？数量还是质量？在许多情况下，质量对于要分类的 bug 来说是极其重要的。如果你只是写这样的东西:

```
Hi, I found a CSRF on your site. You can change profile information in example.com/profile/account and do account takeover. Here is picture of the request that is sent. Thanks! @yourname 
```

好的..所以您告诉他们/profile/account 处的端点易受 CSRF 攻击。在分类方面，现在公司必须重现这一点。他们必须进入自己的帐户，使用 Burp 生成 CSRF 概念证明并复制它。这可能不需要太多时间，但是将上面报告与下面的进行比较:

```
Hi,

# Summary During the testing of the endpoint, `/profile/update` it was noticed that a POST request is sent. The request looks like this: {Picture} In that, we see no CSRF protection which allows attacker to modify information of another user.

# Reproduction To reproduce, simply download the HTML file or copy paste this code

{code} and run it locally.

# Impact In that page, we can change our email without any authentication, so an attacker can change the email of their victim -\> reset the account password with new email. This will lead to automatic account takeover

# Fix A proper authentication of the form with randomized CSRF token will be good. Along with that, I personally recommend that you force user to put their current password when changing critical information like email. 
```

哪个更好？对于分类，第二份报告给出了 PoC 利用，2)影响场景，以及 3)团队的潜在解决方案。

#### 报告 bug 后怎么办？

大多数平台对每个程序都有一个衡量标准。该指标解释了某些事实，如:平均反应时间，平均奖金，最高奖金，平均奖金周期。如果一个程序的平均响应时间是 15 天，而你从第二天或第三天开始要求更新，这是一个好的行为吗？

一旦报告通过审核，请耐心等待。如果超过 1 个月，那么要求更新是一回事。但是对于一个小 CSRF 来说，要求 5 天内更新不是一个好习惯。但是，例如，如果您报告了一个 RCE，则报告被解决的情况会有所不同。每当黑客发现 RCE，公司不会在修复后立即关闭已解决的报告。他们必须进行取证检查，以确保它以前没有被利用。

#### 你应该避免报告哪些 bug？

有几个错误你应该确保永远不要报告。要记住的主要事情是:“看到什么，说什么”并不意味着你报告了你看到的任何奇怪的问题。永远不要报告这样的错误:

1.  404 页或任何地方的内容欺骗。除非你能得到一个 HTML 注入或使其成为一个 XSS 内容欺骗不是一件大事。
2.  SPF/DMARC/DKIM 弱点/缺失和电子邮件欺骗:这些都是有联系的，电子邮件欺骗是电子邮件供应商如何看到域的 SPF 记录的结果。电子邮件欺骗更像是一种网络钓鱼攻击，其中大多数通常位于垃圾邮件箱中。如果一家邮件服务公司接受了收件箱，并不意味着所有的邮件服务公司都会接受。不要报告这些错误。
3.  注销 CSRF:好的，你可以注销一个用户。但是这有什么问题呢？你能利用它使它变得危险吗，如果不能，不要报告它。
4.  滥发电子邮件或一些人称之为“电子邮件轰炸”:一家公司有一个重置密码的功能，你可以用它来发送大量的电子邮件给用户。这会惹恼用户，但这怎么会是安全问题呢？这与其说是一个安全问题，不如说是一个 UI 错误。如果公司开始为 UI bug 付费，那么我会赚几百万。
5.  抓取横幅:假设你发现有人在使用某个版本的 wordpress，或者某个版本的 apache2/nginx。不要报告它，除非你能利用它的某种漏洞来利用它。如果一个公告说，RCE 的某个版本的 wordpress，不要报告它，除非你能证明该网站也容易受到 RCE 的攻击，即测试它，看看它是否容易受到攻击。*
6.  永远不要报告一个错误，而只是留下一个对另一个报告的引用，认为这将解释漏洞。对于不同的应用程序如何工作，每个公司都有自己的政策和系统。参考完全不同的公司的东西是没有意义的。

今天到此为止。如果我想到其他的事情不汇报(肯定不止 6 个，我在这里补充)。

#### 如何成为一名优秀的黑客

如果你提交了一个误报的 bug，它被关闭为 N/A，千万不要回复一条消息说“嗨，你能不能把这个关闭为信息性的，这有损我的名誉？”。这种短信会给公司留下不好的影响。他们想帮助黑客(相信我说的话)，但同时，他们不想关闭所有的报告作为信息。公司与黑客一起成长，他们从发给他们的每份报告中学习。黑客根据公司处理每个漏洞的方式向公司学习。有些公司，比如 Gratipay，在透明度方面做得真的很好。当代码被修复后，他们会把它公布出来，这样用户就可以看到漏洞在哪里。作为一名黑客，你应该永远愿意学习。如果你的 bug 不适用，除非你完全确定它不应该被关闭，否则不要争论。如果你认为你在报告中遗漏了一些东西，比如你不知道除了 CSRF，他们还有其他认证，请热情地询问他们。

Bug bounty 不仅仅是为了赚钱，而是为了学习和成长。没有人是完美的，我们每天都从发现的错误中学习。

#### 如何成长为一名黑客？

研究和了解不同的漏洞有助于黑客取得成功。不断检查新发现的错误。我个人推荐如下:[https://hackerone.com/hacktivity](https://hackerone.com/hacktivity)和看到披露的报道。确保您了解每个错误是如何被发现的，以及它们的影响。永远不要，我重复一遍，永远不要复制粘贴公开披露的报告并将其发送给多个程序，因为程序可以很容易地检测到垃圾邮件。其次，尝试练习弱势。创建一个与公开的漏洞相匹配的易受攻击的应用程序，看看您能否破解它。

如果你觉得卡住了，问问题，但当你确实有礼貌和耐心。社区里的每个人都想帮助你，但是一直问他们同样的问题，不听从他们的建议会让他们阻止你，而不是回应你。

最后，永远要有耐心。当你在安全领域起步时，你不会马上在顶级程序中找到 RCE。开始学习和成长。千万不要走捷径。这是我最喜欢的一句话:“假装它，直到你让它在安全中不起作用”。

干杯！*