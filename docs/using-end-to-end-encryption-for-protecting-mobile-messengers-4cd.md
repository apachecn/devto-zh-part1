# 使用端到端加密确保移动应用安全和数据隐私

> 原文：<https://dev.to/dianamaltseva8/using-end-to-end-encryption-for-protecting-mobile-messengers-4cd>

爱德华·斯诺登事件后，整个世界都开始关注如何确保手机应用程序的安全和用户数据隐私。

目前，end2end encryption (E2EE)是保护用户数据的最可靠和最先进的方式，在构建移动信使方面具有特殊的意义。

例如， [WhatsApp 在 2016 年](https://techcrunch.com/2016/04/05/whatsapp-completes-end-to-end-encryption-rollout/) 集成了端到端加密，从而使得政府和安全部门无法查看用户信息。

同年， [Viber 还决定增加 E2EE](https://www.cio.com/article/3058157/viber-follows-whatsapp-in-adding-end-to-end-encryption-to-its-messaging-service.html) 以提供数据安全和隐私。但是 end2end 加密到底是什么意思呢？

### E2EE 是如何工作的？

端到端是系统，这意味着设备在没有第三方(服务器)参与的情况下相互传输加密信息。通信(数据交换)是直接的，没有中介，所有数据(短信、电话、图像和视频)都是完全加密的。

E2EE 意味着只有那些参与通信的人(设备)才拥有加密和解密消息的密钥。

因此，只有参与聊天(包括一对一聊天、群聊)的用户才能阅读消息或听到他们之间的通话。

换句话说，文件和电子邮件将通过各种第三方服务，但由于完全加密，它们对他们来说完全不可读。因此，只有最终用户可以读取信息。

从拥有移动通讯工具并为其添加了 E2EE 的公司的角度来考虑这种情况，排除收集其用户数据的可能性。

**end 2 end 加密根据以下规则工作:**

*   加密的消息通过通信信道传输(通过 HTTP 协议)。

*   私钥只存储在用户设备上。

*   服务器只存储公钥。用公钥解密消息是不可能的(你需要两个密钥)。

*   删除移动应用程序后，私钥也会被删除。因此，阅读你之前收到的信息变得不可能。当用户重新安装应用程序时，会再次生成加密密钥。

### 编码算法

加密算法是将信息转换成可理解的数据串的数学过程。

源数据通常称为开放文本或纯文本。值得注意的是，对于加密过程来说，信息是什么并不重要，是真实文本还是另一种数据。

了解更多关于使用加密算法的 **[以及将 E2EE 添加到您的手机应用程序的方法。【T2](https://smartym.pro/blog/mobile-app-security-using-end2end-encryption-for-protecting-mobile-messengers/)**