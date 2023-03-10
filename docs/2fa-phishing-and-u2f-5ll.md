# 2FA、网络钓鱼和 U2F

> 原文：<https://dev.to/ingridmorstrad/2fa-phishing-and-u2f-5ll>

## [T1】简介](#intro)

作为加强安全措施的一部分，我的工作单位给我们发了优步卡。什么是 Yubikey？这是一种插入电脑 USB 端口的设备，当你触摸它几秒钟，它就会吐出一串东西。该字符串用于身份验证(识别您的身份)。该设备有一个电容传感器来检测触摸，就像今天的大多数手机一样(使用“手写笔”的旧手机使用基于电阻的电阻式触摸)。大约一个月前，我看到一个视频/文章，说 2FA(双因素认证)容易受到网络钓鱼的攻击，现在有一个新的提案 U2F 可以解决这个问题。我知道 Yubikey 支持 U2F，但我不明白它是如何解决问题的。

## 什么是 2FA？什么是网络钓鱼？

如果你最近用一台新电脑登录了谷歌、脸书或你的银行，你最好做两件事。首先，你必须输入你的用户名和密码，如果你得到了正确的，然后你必须做第二步，你输入一个代码发送到你的手机(或电子邮件)。这“第二步”就是使身份验证成为双因素(2FA)的原因。为什么这样好？首先，大多数密码都没什么价值。人们在多个网站上使用相同的密码，这意味着如果一个密码泄露，想要接管你的帐户的人可以在多个网站上尝试该密码。或者，假设你通过一台被入侵的电脑(可能是网吧里的一台共享电脑，它有一个键盘记录器，可以记录你在上面输入的所有内容)登录你的账户。现在，如果您在那台计算机上键入了密码，“攻击者”(可能是计算机的所有者)就可以查看它！现在，他/她可以完全访问您的帐户。除非，2FA。如果您设置了 2FA，那么当攻击者稍后试图使用您的密码登录您的帐户时，他们将无法访问该帐户，因为他们无法访问您的手机(这将是 OTP 被发送的位置)。但是，攻击者不会轻易放弃...

网络钓鱼是一种攻击，攻击者创建一个看起来像你的电子邮件登录页面(或银行登录页面或脸书登录页面，你知道的)的网站，并试图让你登录。当你这样做时，用户名和密码被发送到攻击者的网站，他可以在你没有意识到的情况下使用它登录实际的网站(电子邮件/Facebook/银行)。啊，那么 2FA 能保护我吗？不要！攻击者可能会向您显示一个页面，要求您键入发送到您设备的代码。如果您这样做，攻击者就可以再次访问您的帐户。

## 那么 Yubikey/U2F 是怎么回事？

这里是我被难倒的地方。长按 Yubikey 会生成一个字符串，不管你使用的是什么应用程序。例如，您可以在记事本中看到打印出来的字符串。因此，这意味着它的工作方式非常类似于 OTP(一次性密码-发送到您手机的代码)，从某种意义上说，它不使用特定于您正在登录的网页等信息。(它可以使用时间戳等。但 OTP 也可以)。但是，我到处都读到 U2F 可以保护你免受网络钓鱼！怎么回事？

在阅读了一堆页面之后，所有这些页面基本上都声称 Yubikey“只是工作”/“发送一个密钥”,而没有描述它如何生成密钥以及为什么这可以防止网络钓鱼，我阅读了 Yubikey 网站上的规范& U2F 上的 Google 设计文档。问题是，我混淆了 Yubikey 的两个不同特征。我看到的字符串是 Yubico 的 OTP，这是它支持的许多不同的东西之一。还支持 U2F，完全不一样。

U2F 的工作方式是这样的:首先，你在网站上注册你的硬件令牌(这就像创建一个密码)。在注册过程中，硬件令牌会创建一个私钥/公钥对(如果您不知道这是什么，请参见下文)和一个与网站对应的“KeyHandle”(类似于密码)。重要提示:“KeyHandle”存储关于网站名称的信息(协议、主机名和端口)。它将“key handle”&公钥发送到网站，网站存储它。然后在呈现登录页面时，服务器(登录网站)给浏览器一个用户的“KeyHandle”，通过 U2F 验证用户。然后，浏览器要求用户按下 U2F 硬件令牌。一旦按下，硬件令牌首先检查这个“KeyHandle”是否与它为这个网站名称存储的(由浏览器发送的)相匹配。它能做到这一点是因为上面提到的重要一点。如果这个不匹配(*咳*钓鱼攻击！*咳*)，它就是不响应，所以认证会失败。由于浏览器扮演着非常重要的角色，它应该意识到这个角色——浏览器需要兼容 U2F 才能工作。这也意味着，如果浏览器本身受到损害(称为“浏览器中的人”攻击)，那么你就完蛋了(耸耸肩)。

额外注意:U2F 还可以抵御更复杂的网络钓鱼攻击(注册和认证期间的 MITM)，但这些不值得深入研究。U2F 承认，如果您设法获得实际网站(电子邮件/银行/facebook 等)的证书，它可能会受到攻击。)但我不会为此而烦恼，因为这只是意味着证书颁发过程被破坏了，也因为我不确定这是如何破坏 U2F 的(我认为它们需要相同的原始名称——由于 DNS 冲突，这将不起作用，但显然我遗漏了一些东西)。我认为到目前为止“ChannelID”支持还没有实现。

## 公钥/私钥如何工作

比方说，你想给我发送一些信息，并确保只有我可以阅读它。我有一对钥匙(1 把公共钥匙和 1 把私人钥匙)。创建这一对密钥的目的是，如果用公钥加密，可以用私钥解密消息。现在，你可以用我的公钥加密你要发送给我的消息，并确保没有其他人可以阅读它(因为只有我应该有我的私钥)。

## 参考文献(我看过的东西就不用了)

[https://docs . Google . com/document/d/1 sjcwdrfbvpg 1 tya VO 5 RSS D1 qpjwj 8 \ _ im6sl-vwjj 6k 0](https://docs.google.com/document/d/1SjCwdrFbVPG1tYavO5RsSD1QpJwj8%5C_im6sl-VWjJ6k0)

[https://fido aliance . org/specs/u2f-specs-master/fido-u2f-overview . html](https://fidoalliance.org/specs/u2f-specs-master/fido-u2f-overview.html)

[http://www . linuxjournal . com/magazine/yubi key-one-time-password-authentic ation？page=0，1](http://www.linuxjournal.com/magazine/yubikey-one-time-password-authentication?page=0,1)