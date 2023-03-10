# 你知道 HTTPS 有不同类型的证书吗？

> 原文：<https://dev.to/antoinette0x53/did-you-know-there-are-different-types-of-certs-for-https>

你能说出下面两张图片的区别吗(除了它们是不同的网站这一事实)？

[![](img/84d7e323b59c980362c8b16bb67e2dbf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2qYC9ADd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gqj8ccp4o6o1jfxvj9kw.PNG)
[![](img/8d4225a499b3c0e0a8438a578eb84f01.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--vLpLCLhM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6cs6x8nh3kb96ma6b9m2.PNG)

你看到 Twitter 的绿色挂锁旁边有“Twitter，Inc [US]”而谷歌只有“安全”吗？这意味着 Twitter 使用 EV 或扩展验证证书，而 Google 只使用 DV 或域验证证书。我只是模糊地注意到，某些 HTTPS 网站在绿色挂锁旁边有除“安全”之外的其他措辞，直到我读了 Troy Hunt 的博客， *[关于 EV 证书、商业 ca、网络钓鱼和让我们加密](https://www.troyhunt.com/on-the-perceived-value-ev-certs-cas-phishing-lets-encrypt/)* 的(感知)价值，我才知道那是什么意思。

我不会重复他说过的每一句话，因为他把每一件事都讲得很好。如果你有时间，你应该读一读。然而，他的博客中有一句话完美地总结了一切。

> “DV 证书让我们确信，我们正在与我们认为自己所在的领域进行沟通，而 EV 证书让我们相信，我们正在与我们认为自己所在的组织进行沟通。”

域验证证书要求证书申请人证明他们拥有获取证书的站点，这并不难做到。EV 证书的目的是提供额外的信心。有人可以很容易地获得钓鱼网站的 DV 证书，但是 EV 证书需要证明他们是他们所声称的组织。事实上，在颁发 EV 证书之前，审计人员必须对网站进行独立审查。我不能只是买一个域名，运行一个贝宝钓鱼网站，并获得一个电子证书说我是贝宝。

这对你意味着什么？也许没什么。有人提到，作为安全专业人员，我们应该开始改变我们管理用户的方式。因此，我们不是天生信任绿色挂锁，而是训练用户识别 EV 和 DV 证书之间的差异，并基于此改变行为。这在目前看来不太可能，因为许多排名前 100 的 Alexa 网站都放弃了 EV certs(比如谷歌)。但是，当我们看到 HTTPS 网站的 EV 证书时，我们可能会开始注意，当我们不再看到该证书时，我们可能会改变行为。

例如，美国银行有 EV 证书，我总是下意识地知道这一点，因为这是我第一次想知道为什么组织名称会在绿色挂锁旁边。如果有一天这种情况突然改变，我会立刻怀疑。

那么，你现在会开始寻找这两种类型的证书吗？你以前知道 EV 和 DV 认证吗？还是这也是你第一次听说？