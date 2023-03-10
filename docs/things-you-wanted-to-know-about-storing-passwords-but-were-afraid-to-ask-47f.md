# 你想知道但又不敢问的关于存储密码的事情

> 原文：<https://dev.to/ruidfigueiredo/things-you-wanted-to-know-about-storing-passwords-but-were-afraid-to-ask-47f>

安全漏洞非常普遍。更糟糕的是，当涉及到用户的密码时，经常没有采取合理的预防措施来确保他们不能轻易地从被破坏的数据中提取出来。

人们倾向于在多个站点使用相同的密码，或者简单的不同密码。这使人们更容易记住密码，但也意味着当密码暴露时，攻击者可能会访问使用该密码的其他网站。

问题是，现在没有理由让这种情况变得如此普遍。尽管围绕密码学的科学相当复杂，但是算法很容易使用并且容易获得。

这篇博客文章试图解释一个存储的密码应该具有什么样的良好品质，以及可以使用哪些算法来达到这个目的。

[![Image of 0s and 1s suggesting encrypted data for storing passwords](img/d5823b64e0559efc39777fd525c61280.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6AS2HPcR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.blinkingcaret.com/wp-content/uploads/2017/11/encryptied-data.jpg)

那么存储的密码应该具备哪些品质呢？首先，只有用户应该知道它。存储密码后，无法获取密码。

这排除了以明文形式保存密码的可能性，但也排除了加密密码的可能性。

这可能听起来像是常识，但不幸的是，有一些相当著名的在线企业的例子，如果你使用他们的“忘记密码功能”，他们会以明文形式通过电子邮件将密码发送给你。

我现在想到的例子是乐购，英国和爱尔兰最大的连锁超市之一。几年前，当被问及此事时，他们说他们正在[加密密码](https://www.theregister.co.uk/2012/07/31/tesco_website_insecurity/)。这不是一个好主意，因为如果存在安全漏洞，并且用于加密密码的密钥被发现，攻击者将可以访问所有用户的密码。

因此，如果加密密码不是一个好主意，我们可以使用什么替代方案？

## 散列法

也许我们可以破解密码。但是首先，哈希是什么？

散列背后的思想是，你可以接受任何输入，并产生一个具有特定大小的输出，而你不能从中“提取”原始输入。

例如，下面是字符串“hello world”在 [base64](https://en.wikipedia.org/wiki/Base64) 中的 [SHA1](https://en.wikipedia.org/wiki/SHA-1) 哈希算法的输出:

22596363 B3 de 40 b 06 f 981 FB 85d 82312 E8 c0ed 511

这是“你好，世界”的 SHA1 散列：

f951b 101989 B2 C3 b 7471710 b4e 78 fc 4 db DFA 0 ca 6

这是一个 700Mb 视频文件的 SHA1 哈希:

1b 2803 f 08 a 8 F2 ba 251 a 557 CD 61 f1 a38 b 07427011

两个非常相似的输入产生完全不同的散列，并且不管输入的大小，输出总是具有相同的大小。

哈希算法通常用于检测重复数据。假设您正在存储视频文件，并且您想要确定某个视频文件是否已经被存储。

如果每次您需要存储一个新的视频文件时，您首先计算它的哈希，然后您可以将视频文件的哈希与以前存储的哈希进行比较。如果已经有一个哈希与您试图保存的视频文件相同，这意味着它是重复的，如果不是，您可以保存带有哈希的视频文件。这样做的好处是比较两个哈希比比较两个视频文件要快得多。

哈希的另一个常见用例是检测数据在传输过程中是否被破坏。

例如，如果一个文件需要通过一个不太可靠的介质传输，那么在文件发送之前会创建一个散列。然后，文件和散列一起被发送。

在接收端，再次计算散列值，并与传输的散列值进行比较。如果它们不匹配，这意味着文件被破坏。

哈希算法的一些例子有 [MD5、SHA1、SHA256 和 SHA512](https://en.wikipedia.org/wiki/Secure_Hash_Algorithms) 等等。

这些算法的输出质量看起来非常适合存储密码。给定一个密码，计算它的散列值并存储它。

当用户尝试登录时，根据用户在登录表单中输入的密码计算哈希。如果该散列与存储的散列相匹配，那么它就是正确的密码。

不幸的是，这里有一个陷阱。哈希应该非常快。在其原始使用情形中(例如，重复检测、验证数据未被更改/损坏)，它需要非常快。

这使得它不适合存储密码，因为如果它的计算速度很快，这意味着可以在短时间内生成许多哈希。这意味着，如果您想知道使用了什么密码来生成特定的哈希，您可以在短时间内尝试许多候选密码，以查看是否有任何密码生成了与您正在寻找的密码相匹配的哈希。

因为人们选择的大多数密码在某种程度上都是可预测的(例如，您可以在字典中找到的单词或它们的简单变体)，所以如果攻击者设法访问密码的哈希，大多数情况下，找到密码就变成了计算字典中单词的哈希，直到找到匹配为止。

加快从哈希中获取密码的过程的一种方法是使用以前入侵的数据，即最常用的密码。通过预先计算最常见的密码，可以从哈希到密码进行查找搜索。如果攻击者设法访问存储哈希的网站数据库，这个过程很可能会泄露大量密码。

这种通过使用预先计算的密码哈希查找密码的方法被称为[彩虹表攻击](https://en.wikipedia.org/wiki/Rainbow_table)。你甚至不需要自己生成散列，有预先计算好的散列可以在线购买。它们并不难找。

## HMAC

如果我们不能阻止人们一直使用相同的密码，我们能做些什么来使哈希更加安全吗？

如果我们只是在密码后面附加一个随机字符串会怎么样？

我们可以用密码+随机字符串组合的 hash 来存储随机字符串。当用户尝试登录时，我们将用户输入的密码附加到随机字符串中，并重新计算散列。如果匹配，则密码是正确的。

举个例子，如果密码是“cutecats”，不用计算“cutecats”的哈希，我们可以计算“cutecatsm@O88q39！Q^x”并存储`HASH("cutecatsm@O88q39!Q^x")`和`m@O88q39!Q^x`。

你不太可能在彩虹桌上找到`cutecatsm@O88q39!Q^x`。

抵抗彩虹表攻击的有效方法是为每个密码生成一个随机字符串。如果您考虑所有密码只有一个随机字符串的替代方案，攻击者仍然可以预先计算一次所有哈希，并使用它们来尝试获取密码。

如果每个密码都有一个随机字符串，这意味着攻击者不能重用任何之前计算的哈希。

附加在密码后面的随机字符串通常被称为 [salt](https://en.wikipedia.org/wiki/Salt_(cryptography)) 。

虽然我们可以只使用散列算法来执行这些操作，例如`SHA256(password + salt)`，但是有理由不这样做。假设密码是“可爱的猫”，盐是“1salt”。现在假设密码是“cutecats1”，盐是“salt”。这两种组合将生成相同的哈希。这是次优的。

还有另一种算法，它将两个输入组合在一起，并生成一个与前面提到的哈希算法具有相同属性的输出。它被命名为 [HMAC](https://en.wikipedia.org/wiki/Hash-based_message_authentication_code) ，代表基于散列的消息认证码。使用 HMAC，“可爱的猫”、“1 盐”和“可爱的猫 1”和“盐”产生不同的输出。

在我们深入了解什么是 HMAC 之前，让我们先来谈谈 [MAC](https://en.wikipedia.org/wiki/Message_authentication_code) (消息认证码)及其用途。

假设您希望通过互联网与某人交流，尽管您不介意有人在监听您的对话，但您希望确保对话未被篡改(即，没有人可以作为参与对话的两个人之一发送消息)。一条杂乱的消息在这里是行不通的。哈希只能保证消息没有被破坏，如果坏的参与者可以访问消息并修改它，这意味着他可以为修改后的消息计算新的哈希。对信息的接收者来说，看起来一切都很好。

消息认证码通过使用只有通信双方知道的秘密来解决这个问题。以 Alice 和 Bob 为例，如果 Alice 想给 Bob 发送一条消息，她将计算`MAC(secret, messageToBob)`并与`messageToBob`一起发送。Bob 收到消息后将重复该过程，并将他生成的 MAC 代码与他从 Alice 处收到的代码进行比较。如果它们匹配，Bob 可以确信不仅消息没有被修改，而且它是由 Alice 发送的，因为她是唯一能够生成有效 MAC 的其他人。

如果你想知道 Alice 和 Bob 是如何在可能有人监听他们的通信的情况下就秘密达成一致的，请看一下关于 https 如何工作的简要说明，在这里你会找到一个关于 T2 Diffie-Hellman T3 算法的例子。

那是麦克。那么 [HMAC(基于哈希的消息认证码)](https://en.wikipedia.org/wiki/Hash-based_message_authentication_code)呢？它只是 MAC 的一个实现，允许我们从几种不同的哈希算法中选择来计算 MAC，例如，HMAC-SHA256 使用的哈希算法是 SHA256，HMAC-SHA1 的哈希算法是 SHA-1，等等。

那么我们如何使用 HMAC 来存储密码呢？例如，我们可以选择 SHA256，计算`HMAC-SHA256(password, salt)`，并将其与`salt`一起存储。

解决了？可惜没有。

虽然彩虹表攻击在这里不起作用，但今天的硬件允许我们如此快速地生成这些哈希，以至于可以只使用常用密码列表中的密码，甚至进行暴力攻击，例如计算`HMAC-SHA256(testPassword, salt)`，其中`testPassword`是“a”，然后是“aa”，然后是“ab”，直到某个长度的字符、数字和符号的所有组合。

给你一个想法，一个高端显卡，你今天可以去买，例如英伟达 GTX 1080，[每秒可以做~4450 万 SHA256 哈希]((https://gist.github.com/epixoip/6ee29d5d626bd8dfe671a2d8f188b77b))。在一台计算机中有几个这样的卡是可能的，所以这个数字可以大得多。

## 密钥推导

所以如果`HMAC`不起作用，那怎么办？

使用盐和密码的想法是一个好主意，它不起作用的唯一原因是因为计算 HMAC 是如此之快，以至于有可能在合理的时间内猜出密码。要做到这一点，攻击者只需要密码的散列和 salt(这将在违规的情况下发生),以及足够好的能够每秒生成许多散列的硬件。

也许有一种方法可以让这个过程的计算成本更高？

如果我们做`HMAC(password, salt)=hash1`然后再做`HMAC(password, hash1)=hash2`并且重复很多次，比如 100.000，会怎么样？这将大大减慢这一进程。一个接一个地尝试密码，或者暴力方法不再合理。

有一些算法可以做到这一点，它们不仅仅是重复`HMAC`。它们被称为密钥导出函数算法。

密钥派生背后的思想是，给定一个密钥，从原始密钥生成一个或多个具有特定属性的密钥。例如， [AES-256 加密算法](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)需要一个 256 位的密钥。

要求用户手动生成精确的 256 位密钥是不合理的。那把钥匙很难记住。

这是密钥派生的用途之一。这是一种从一个密钥(例如用户想出的密码)到另一个具有特定大小的密钥的方式。

但是除此之外，在正确配置下，这些密钥推导具有计算非常昂贵的特性。

例如 [PBKFD2(基于密码的密钥导出函数 2)](https://en.wikipedia.org/wiki/PBKDF2) 可以被配置为使用密码、盐、特定的`HMAC`算法和`HMAC`应该应用的迭代次数。还有 [bcrypt](https://en.wikipedia.org/wiki/Bcrypt) 和 [scrypt](https://en.wikipedia.org/wiki/Scrypt) 也可以以类似的方式使用。

例如，在 dotnet core 中使用 PBKDF2 会是什么样子，128 位 salt，100.000 次迭代，输出密钥大小为 128 位(16 字节)。

```
const int saltSize = 128 / 8;
const int outputKeySize = 128 / 8;
const int numberOfIterations = 100000;

var password = "cutecats";

using (var rng = RandomNumberGenerator.Create())
{
    var salt = new byte[saltSize];
    rng.GetBytes(salt);

    var derivedKey = KeyDerivation.Pbkdf2(password, salt, KeyDerivationPrf.HMACSHA256, numberOfIterations, outputKeySize);

    Console.WriteLine($"The salt in base64: {Convert.ToBase64String(salt)}");
    Console.WriteLine($"The 'hash/derived key' in base64: {Convert.ToBase64String(derivedKey)}");
} 
```

Enter fullscreen mode Exit fullscreen mode

要运行这个例子，你需要安装 [`Microsoft.AspNetCore.Cryptography.KeyDerivation` nuget 包](https://www.nuget.org/packages/Microsoft.AspNetCore.Cryptography.KeyDerivation/2.0.0)。

如果您想知道我为什么选择 128 位 salt 大小或 128 位输出密钥，我对此没有很好的答案，但一个好的经验法则是它应该小于哈希算法产生的值，例如我们使用 SHA256，它产生 256 位输出或 32 个字节。

顺便说一下，“可爱的猫”是一个可怕的密码，你应该使用类似于 [LastPass](https://www.lastpass.com/) 、 [1Password](https://1password.com/) 或类似的密码管理器来为你生成密码。这是因为即使密码存储正确，如果它是像“可爱的猫”这样的东西，或者例如你在字典中找到的单词，即使花 1 秒钟尝试每个密码，攻击者也可能通过猜测和一点耐心就找到它。

就这样，我希望我已经帮助解释了为什么我们应该使用长度合理的密码，由几个数字、符号和字符组合而成，以及为什么它们不应该以一种可以很容易地“逆转”回原始密码的方式存储。