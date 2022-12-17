# 将 security.txt 添加到您的网站

> 原文：<https://dev.to/ankitvijay/add-securitytxt-to-your-website-47o9>

最近，我看到了安全专家特洛伊·亨特的以下推文

> Google 现在有一个 security.txt 文件——你也应该有！[https://t.co/fXmeNG9Yem](https://t.co/fXmeNG9Yem)https://t.co/drbJQ1Ynw8T2
> 
> —特洛伊·亨特(@ Troy Hunt)[2018 年 2 月 3 日](https://twitter.com/troyhunt/status/959724034622595072?ref_src=twsrc%5Etfw)

推文谈到了一个名为`security.txt`的文件。我继续阅读[的帖子](https://www.troyhunt.com/fixing-data-breaches-part-3-the-ease-of-disclosure/)，以理解`security.txt`到底是什么，它解决了什么问题。

## 那么，什么是 Security.txt 呢

没有哪个组织希望在安全问题上被抓错脚。数据或安全漏洞可能会导致公司的财务和声誉损失。当安全研究人员在组织网站/应用程序中发现潜在违规或安全漏洞时，他/她会尝试联系组织以“负责任地披露”该问题。这种披露本质上是保密的，让本组织有时间对这一问题采取适当行动。

但是，安全研究员向谁求助呢？通常，在这种情况下，他们不愿意通过网站上的一般“联系我们”页面或给组织的客户服务部发电子邮件/打电话来联系组织。他们更愿意接触组织中能立即采取行动的人，有权威的人。

那就是`security.txt`可以来救援的地方。安全研究员* *艾德·福迪尔提出了`security.txt`的概念。** 根据他的网站，【securitytxt.org】T4:

> security.txt 的主要目的是帮助公司和安全研究人员在保护平台安全时变得更容易。多亏了 security.txt，安全研究人员可以很容易地就安全问题与公司取得联系。

这些组织可以在他们的网站上添加`security.txt`来提供信息，说明当安全研究人员在他们的网站上遇到安全漏洞时，他们可以如何或向谁求助。

该网站还提供了一种简单的方法来生成 security.txt 文件。文件需要添加到路径`/.well-known/security.txt`。

包括[谷歌](https://www.google.com/.well-known/security.txt)在内的许多网站已经开始增加`security.txt`，也许是时候你也应该增加一个 [![🙂](img/1bb31e891282bfa40812655c9c9ace9e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DvXCaIjD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s.w.oimg/core/emoji/11/72x72/1f642.png)

帖子[将 security.txt 添加到你的网站](https://ankitvijay.net/2018/02/04/add-security-txt-to-your-web-app/)最早出现在[嗨，我是 Ankit](https://ankitvijay.net) 。