# 你的 SSL 有多好？

> 原文：<https://dev.to/funkysi1701/how-good-is-your-ssl-3ncf>

[![Troy Hunt SSL expert](img/e496cb61e8aad61c20bc9e5cfd554814.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EeCc3BDE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storageaccountblog9f5d.blob.core.windows.net/blazor/wp-content/uploads/2017/07/Troy-Hunt-Profile-Photo.jpg%3Fresize%3D150%252C150%26ssl%3D1)

我最近在 Pluralsight 上看了 Troy Hunt 的《每个开发人员都必须了解的 HTTPS 课程》。它非常好，真的让你思考 SSL 证书以及如何正确地实现它们。

特洛伊特别提到的一件事是网站 [SSL 实验室](https://www.ssllabs.com/ssltest/index.html)。这个网站允许你测试网站的 SSL 实现。A 到 F 的等级被划分为 A 为最好，F 为最差。

Troy Hunt 在他的博客上讨论了澳大利亚银行的得分情况。让我们看看几家英国银行。

| **银行** | **SSL 证书** | **等级** | **SSL 下的主页** |
| --- | --- | --- | --- |
| 汇丰银行 | [www.hsbc.co.uk](http://www.hsbc.co.uk) | B | Y |
| 全国性的 | onlinebanking.nationwide.co.uk | C | 普通 |
| 纳特韦斯特 | [www.nwolb.com](http://www.nwolb.com) | C | 普通 |
| 巴克莱银行 | [www.barclaycard.co.uk](http://www.barclaycard.co.uk) | 表示“不” | Y |
| 主要经营银行） | bank.barclays.co.uk | 表示“不” | 普通 |
| 劳埃德银行 | [www.lloydsbank.com](http://www.lloydsbank.com) | A | 普通 |
| 苏格兰皇家银行 | [www.rbsdigital.com](http://www.rbsdigital.com) | C | 普通 |
| 渣打银行 | [www.sc.com](http://www.sc.com) | C | Y |
| 处女钱 | uk.virginmoney.com | A+ | Y |
| 桑坦德 | retail.santander.co.uk | 表示“不” | 普通 |

总的来说，评分都很好，都在 A-C 范围内。但是我也指出了他们的主页上是否有 SSL。上面列出的 10 个网站中只有 4 个在 SSL 下加载了主页。

为什么只要登录在 SSL 下，这就很重要？任何通过 http 加载的页面都有被中间人攻击的潜在风险。虚假的恶意主页可能包含指向任何页面的链接，并诱骗用户输入个人信息。

如果你想测试这里没有列出的银行或其他网站。转到[https://www.ssllabs.com/ssltest/index.html](https://www.ssllabs.com/ssltest/index.html)并在搜索中键入 SSL 证书上的地址。好消息是这个网站得了 a。

Troy 提到 SSL 的采用快速增长，评级的提高也在快速增长。在写这篇博客的过程中，其中一家银行从 C 级升到了 A 级。