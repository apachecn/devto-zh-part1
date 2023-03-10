# JSON Web 令牌的 7 个最佳实践

> 原文：<https://dev.to/neilmadden/7-best-practices-for-json-web-tokens>

**更新 2017 年 3 月 15 日** : *鉴于最近对 JWT 和何塞规范的批评，我写了一些关于[你应该使用 JWT/何塞的笔记？](https://neilmadden.wordpress.com/2017/03/15/should-you-use-jwt-jose/)。*

JSON Web 令牌 (JWTs，发音为“jots”)作为一种安全传输小型信息包的方式越来越流行，例如会话令牌、身份证明和网络协议消息。虽然有很多库可以使 jwt 的构建和验证变得简单，但是也很容易误用它们，最终损害您想要实现的安全属性。在本文中， [I](https://neilmadden.wordpress.com/) 概述了一些确保您的信息安全的最佳实践。

## 1。花点时间了解底层的安全属性

很容易把加密库想象成一个黑盒。你选择一个库，阅读文档，不久你就在你的应用程序上撒了一点数字签名或加密魔法粉，然后继续下一个任务。这是一个错误。即使是最精心设计和实现的库，如果对它所提供的安全属性以及如何提供这些属性缺乏了解，在使用不当时也会导致致命的缺陷。

JWT 图书馆提供了许多令人困惑的选项:

*   基于 [HMAC](https://en.wikipedia.org/wiki/Hash-based_message_authentication_code) 与 [SHA-256、SHA-384 或 SHA-512](https://en.wikipedia.org/wiki/SHA-2) 的对称消息认证码(MAC)
*   基于 [RSA](https://en.wikipedia.org/wiki/RSA_(cryptosystem)) 或[椭圆曲线 DSA](https://en.wikipedia.org/wiki/Elliptic_Curve_Digital_Signature_Algorithm) 的数字签名。
*   基于 [CBC 模式](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#CBC)下 [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 或 [GCM](https://en.wikipedia.org/wiki/Galois/Counter_Mode) 模式下 AES 的对称认证加密。
*   使用具有各种填充模式的 RSA 的公钥加密( [PKCS#1.5](https://tools.ietf.org/html/rfc2313) 、 [OAEP](https://en.wikipedia.org/wiki/Optimal_asymmetric_encryption_padding) )。
*   基于 [ECDH](https://en.wikipedia.org/wiki/Elliptic_curve_Diffie%E2%80%93Hellman) 或[口令的](https://tools.ietf.org/html/rfc2898#section-6.2)等密钥协商协议，以及各种密钥包装模式。
*   有效载荷的压缩。

这些选项一开始可能会令人困惑，您需要仔细选择以获得您想要的安全属性。例如，为了保护由同一应用程序发布和使用的会话令牌，使用对称加密模式(如 AES-GCM)可能是合理的。这提供了会话令牌内容的机密性以及完整性和真实性，以防止伪造。然而，像 RSA 这样的公钥加密方案在这种情况下是完全不合适的，因为任何拥有*公钥*的人都可以创建一个有效的会话令牌。

从长远来看，花一些时间来了解安全属性以及底层加密原语如何提供这些属性将会带来好处。Coursera Crypto I 课程会给你一个好的开始。

## 2。不要走极端

有了上一点中描述的过多的模式，我们很容易走极端，什么都用一点。这通常是不必要的。例如，如果您想要确保您自己的 jwt 的机密性和真实性，那么使用一种对称加密模式就足够了，因为这些已经被认证了。您可以使用共享密钥直接加密 jwt，或者使用一种密钥包装模式。当然也没有必要应用一个单独的 HMAC，除非你对安全边际非常偏执。(更好的办法是经常转动你的钥匙，见下文)。您使用的选项越多，您的系统推理起来就越复杂，并且您可能需要管理的凭证也越多。

## 3。计划如何管理您的密钥

任何系统安全性最薄弱的部分通常是密钥管理。如果密钥泄露，那么安全属性就不再有保证:消息可能被解密，安全令牌可能被伪造，系统的其他部分也可能因此被破坏。您应该遵循管理密钥的最佳实践:

*   对所有密钥使用安全的存储方式，以防泄露。考虑使用[硬件安全模块(HSM)](https://en.wikipedia.org/wiki/Hardware_security_module) 或运行在独立主机上的专用密钥管理服务，如[金库](https://www.vaultproject.io/)或[亚马逊 KMS](https://aws.amazon.com/kms/) 。确保适当的访问和审计控制到位。
*   根据使用量，经常轮换密钥，例如每天、每周或每月一次。
*   使用高质量的随机数据源来生成密钥。在类 UNIX 系统上，这通常意味着/dev/random 或/dev/urandom，或者专用的硬件随机数据生成器。如果必须使用密码，那么就使用高质量的基于密码的密钥派生算法，比如 [bcrypt](https://en.wikipedia.org/wiki/Bcrypt) 、 [scrypt](https://en.wikipedia.org/wiki/Scrypt) 、 [Argon2](https://en.wikipedia.org/wiki/Argon2) 或者 [PBKDF2](https://en.wikipedia.org/wiki/PBKDF2) 。使用高迭代计数(例如 [NIST 建议 PBKDF2](https://pages.nist.gov/800-63-3/sp800-63b.html#memorized-secret-verifiers) 至少 10，000 次迭代)和唯一的每键 salts(如适用)。
*   立即轮换任何可疑的密钥，并停止信任任何使用该密钥的 jwt。

## 4。考虑使用“无头”jwt

JWT 由受保护的有效载荷和明文“报头”部分组成。这可以包含各种比特的信息，例如用于对有效载荷进行签名或加密的算法，或者由网络上的中介使用的特定于应用的信息，例如用于消息路由。在很多情况下，这些信息是多余的，无论如何相信其内容都是十分危险的。如果您不需要与期望标准 JWT 的第三方进行互操作，那么您可以节省一些空间，并通过在生成 JWT 时简单地剥离报头部分，然后在解析之前从已知数据中重新创建它，来消除一类漏洞。我将这些称为“无头 jwt ”,并建议您尽可能地使用它们。

去掉页眉很容易:只需去掉第一个“.”之前的所有内容编码 JWT 中的字符。要重建 JWT，只需对标识已知算法和参数的固定头进行 base64url 编码，并将其添加到无头 JWT 中。

## 5。同时使用加密和压缩时要小心

[如果不小心，将加密和压缩结合起来可能会导致信息泄露](http://breachattack.com)。这不仅仅是理论上的漏洞，实际攻击已经证明了这一点。如果攻击者能够控制与某些敏感信息相同的加密块中的任何信息，那么压缩可能会泄漏关于秘密部分的信息。当空间非常宝贵时(例如对于 HTTP cookies)，压缩 JWT 可能是一个很大的优势，但是应该权衡潜在的风险。如果您需要保密，并且确实也想要压缩，可以考虑以下选项:

*   [将压缩内容填充到您满意的固定大小](http://crypto.stackexchange.com/a/37936/26028)。你仍然可以得到一个不错的压缩比。
*   将数据分为由应用程序控制的机密信息和由用户输入控制的非机密信息。将秘密信息放入加密和压缩的有效载荷中，将用户提供的信息放入未压缩(和未加密)的 JWT 报头中。这样，用户控制的信息不能改变秘密信息的压缩。如果用户提供的信息也应该加密，那么可以考虑使用两个独立的 jwt。

## 6。考虑 JWT 寿命和撤销

如果使用 JWT 来传递权威，最佳实践是限制 JWT 的有效期。如果 jwt 必须是长期的，那么研究撤销 jwt 的选项以防止进一步使用:

*   基于唯一的 ID 将 jwt 列入黑名单可以是可伸缩的和有效的，但是默认情况下 jwt 在没有信息的情况下是可信的。
*   具有短命的 jwt，并要求定期重新发布它们(例如，通过 [OAuth2 刷新令牌流](https://tools.ietf.org/html/rfc6749#section-1.5))。当客户端是编程式的，因此可以定期自动刷新令牌时，这可能是一个合适的折衷方案。
*   仅使用 jwt 来标识资源和一些最小状态，而将大部分数据存储在服务器上。数据库以唯一标识符(例如 UUID)将状态存储为正常。JWT 是由应用层根据这个唯一的 ID 和一些最少的元数据构建的。这可能看起来是最糟糕的，但实际上是相当明智的默认，原因如下:1 .状态保存在服务器上，可以很容易地管理、审计和撤销(删除)。2.JWT(例如 HMAC)中的保护允许在命中中央数据库资源之前快速检查有效性。3.如果令牌存储在数据库中，但由应用层使用单独存储的密钥即时签名/验证，那么攻击者必须破坏两个系统才能伪造或窃取令牌。4.随着时间的推移，随着需求的变化，状态可以从 JWT 转移到数据库，反之亦然。5.数据库中可以存储比 JWT 通常所能容纳的更大量的状态，并且更新它要容易得多。

## 7。阅读安全注意事项！

像大多数 RFC 一样，JWT 规范包含[安全考虑](https://tools.ietf.org/html/rfc7518#section-8)部分，详细说明了常见的威胁以及如何避免这些威胁的建议。在部署基于 JWT 的解决方案之前，您应该阅读所有这些内容，并确保您理解它们:

*   [RFC 7515: JSON 网络签名](https://tools.ietf.org/html/rfc7515#section-10)
*   [RFC 7516: JSON 网页加密](https://tools.ietf.org/html/rfc7516#section-11)
*   [RFC 7517: JSON Web Key](https://tools.ietf.org/html/rfc7517#section-9)
*   [RFC 7518: JSON Web 算法](https://tools.ietf.org/html/rfc7518#section-8)

你也应该阅读对基于 JWT 的解决方案的普遍批评。虽然我认为这篇文章夸大了风险(有合理的缓解措施可用)，但它提出了一些有效的观点，您应该在押注 jwt 之前考虑这些观点。

## 结论

我希望这篇文章为您提供了一些关于构建健壮、安全的基于 JWT 的解决方案的可靠建议。如果仔细使用并进行大量规划，jwt 可以构成高度可伸缩和可靠系统的基础。通过遵循本文中的最佳实践，您应该能够采取适当的保护措施来确保数据的安全。

**如果你喜欢这篇文章，你会喜欢我的书** : [API 安全在行动](https://www.manning.com/books/api-security-in-action)