# Java 密钥库——血淋淋的细节

> 原文：<https://dev.to/neilmadden/java-keystoresthe-gory-details-dnj>

*最初发布在我的[博客](https://neilmadden.wordpress.com/2017/11/17/java-keystores-the-gory-details/)*

Java 密钥库用于以加密和完整性保护的方式存储密钥材料和相关证书。像所有 Java 一样，这种机制是可插拔的，因此存在各种不同的选项。有很多文章描述了不同的类型以及如何初始化它们，加载密钥和证书等等。然而，缺乏关于这些密钥库如何存储和保护您的密钥材料的详细技术信息。这篇文章试图把最常见的密匙库的重要细节收集到一个地方。

每个密钥存储都有一个用于保护整个存储的总密码，并且可以选择为每个秘密或私有密钥条目设置每个条目的密码(如果您的后端支持的话)。

## Java 密钥库(JKS)

最初的 Sun JKS (Java 密钥存储)格式是一种专有的二进制格式文件，只能存储非对称私钥和相关的 X.509 证书。

单个私钥条目由简单的自制流密码保护，基本上密码是加盐的(160 位)并用 SHA-1 散列在一个简单的链式结构中，直到它生成足够的输出字节以异或进入私钥。然后，它存储一个由 SHA-1(密码+私钥字节)组成的简单验证器标记，即未加密的私钥字节。换句话说，这是一个[加密和 MAC](https://en.wikipedia.org/wiki/Authenticated_encryption#Encrypt-and-MAC_.28E.26M.29) 方案，具有基于简单前缀键控 SHA-1 的朴素结构。(本方案有 OID 1.3.6.1.4.1.42.2.17.1.1)。

整个档案再次受到自制前缀键控散列结构的完整性保护，该散列结构包括原始密钥库密码的 UTF-16 字节的 SHA1 散列，接着是短语“Mighty Aphrodite”(我不是开玩笑)的 UTF-8 字节，接着是编码的密钥库条目的字节。

如果这个描述的每一部分都没有让你对着屏幕发出同样的恐惧和困惑，那么你可能还没有完全理解这有多糟糕。不要使用它，即使只是为了存储证书——它的抗篡改性比加密还要差。

## JCE 钥匙商店

Sun 后来用 Java 加密扩展(JCE)更新了 JVM 的加密功能。与此同时，他们还引入了一种新的专有密钥存储格式:JCEKS。

JCEKS 使用“PBEWithMD5AndTripleDES”来加密单个密钥条目，使用 64 位随机 salt 和 200，000 次 PBKDF1 迭代来导出密钥。TripleDES 在 CBC 模式下与 3 个密钥(“加密-解密-加密”)一起使用。对于单个密钥没有单独的完整性保护，如果归档作为一个整体受到完整性保护，这是没有问题的，但是这意味着访问控制实际上是在整个密钥库的级别上进行的。从加密的角度来看，这并不可怕，但肯定可以改进 MD5 和 T2 triple des T3 都不再被认为是安全的，而且已经有很长时间没有人在新项目中推荐它们了。然而，打破它也不是一件轻而易举的事。

JCEKS 使用与 JKS 相同的荒谬的“Mighty Aphrodite”前缀关键字散列来保护整个档案的完整性。最好假设这两个密钥存储都没有严格的完整性保护。

## PKCS 第 12 名

除了这些专有的密钥库，Java 还支持“标准的”PKCS#12 格式的密钥库。“标准”之所以引起恐慌，是因为尽管它确实是一种标准格式，但它是一种非常灵活的格式，因此在实践中，不同软件支持的“密钥包”格式和加密算法之间存在显著差异。例如，当您在 Java 的 PKCS#12 密钥库中存储对称的 SecretKey 对象时，OpenSSL 无法读取它们，因为它们使用了它不理解的包类型(“secret bag”-OID 1 . 2 . 840 . 113549 . 1 . 12 . 10 . 1 . 5)。

Java 使用 PKCS#12 标准格式的版本 3。它以上述“secretBag”格式存储秘密密钥，并以“PKCS#8 隐藏密钥包”格式(OID 1.2.840.113549.1.12.10.1.2)存储非对称私钥。这只是决定了磁盘上字节的格式。在这两种情况下，实际的密钥材料都是使用某种形式的基于密码的加密(PBE)模式进行加密的。默认情况下，这是“pbewithsha 1 and DESede”—“DESede”是加密-解密-加密模式中三元组的另一个名称，因此这与 JCEKS 使用的模式非常相似，只是使用了稍微好一点(但仍不推荐)的 SHA-1 形式的散列。默认情况下，这使用 160 位 salt 和 50，000 次迭代。

**但是**，在 PKCS#12 实现中有一个重要的改进——*你可以选择加密算法！*保存密钥时，通过传入一个 [PasswordProtection](https://docs.oracle.com/javase/8/docs/api/java/security/KeyStore.PasswordProtection.html#PasswordProtection-char:A-java.lang.String-java.security.spec.AlgorithmParameterSpec-) 参数(从 Java 8 开始)，您可以指定一个特定的(基于密码的)密码。我还没有检查到底允许什么样的密码，但你至少可以指定一个更强的 PBE 模式，比如“PBEWithHmacSHA512AndAES_256”，它将使用 salted PBKDF2 导出一个 256 位的 AES 密钥，然后使用 AES/CBC/pkcs 5 与该密钥相加来加密存储的密钥。您还可以增加所使用的 PBKDF2 的迭代次数。例如:

```
import java.io.FileOutputStream;
import java.security.KeyStore;
import java.security.KeyStore.PasswordProtection;
import java.security.KeyStore.SecretKeyEntry;
import java.security.SecureRandom;

import javax.crypto.SecretKey;
import javax.crypto.spec.PBEParameterSpec;
import javax.crypto.spec.SecretKeySpec;

public class scratch {
    public static void main(String... args) throws Exception {
        KeyStore keyStore = KeyStore.getInstance("PKCS12");
        keyStore.load(null, null); // Initialize a blank keystore

        SecretKey key = new SecretKeySpec(new byte[32], "AES");

        char[] password = "changeit".toCharArray();
        byte[] salt = new byte[20];
        new SecureRandom().nextBytes(salt);
        keyStore.setEntry("test", new SecretKeyEntry(key),
            new PasswordProtection(password,
                "PBEWithHmacSHA512AndAES_128",
                new PBEParameterSpec(salt, 100_000)));

        keyStore.store(new FileOutputStream("/tmp/keystore.p12"), password);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意，尽管上述 PBE 模式中包含了“HmacSHA512 ”,但它仅适用于从密码中导出的密钥。在单个条目级别没有完整性保护。

同样值得注意的是，密钥库和个人密钥密码*应该是相同的*。我不认为这是 Java 中 PKCS#12 的一个基本限制，但是像命令行“keytool”实用程序这样的标准 Java 工具肯定不能处理 PKCS#12 密钥库，因为它们的存储和单个密钥使用了不同的密码。如果你不需要使用这些工具，那么你可以为每把钥匙设置不同的密码。

与前面的条目不同，PKCS#12 密钥存储格式实际上也加密证书。它通过硬编码算法“PBEWithSHA1AndRC2_40”来实现这一点。这使用 50，000 轮加盐 PBKDF1 来导出用于 RC2 加密的 40 位密钥。RC2 是一个旧的流密码，我当然不会推荐。40 位的密钥太小，不能提供任何真正的安全性。这让我想知道，为什么要费心应用 50，000 轮 PBKDF1 来保护密码，同时生成一个本身容易受到暴力攻击的密钥。暴力破解派生密钥实际上可能比原始密码更快。我只能假设，这是为了保持与某个决策的兼容性，而这个决策是很久以前做出的，现在参与其中的每个人都深感遗憾。

整个 PKCS#12 密钥库的完整性受到“HmacPBESHA1”的保护。这是 HMAC-SHA1 使用从商店密码导出的密钥，使用 salted PBKDF2-HMAC-SHA1 的 100，000 次迭代。这都是硬编码，所以不能改变。这是一个不错的选择，尽管在这里能够使用 SHA-1 以外的东西也不错，因为 PKCS#12 似乎允许使用其他 MAC。对于 HMAC 的用法，SHA-1 现在还可以，但是最好把它去掉。能够调整迭代次数也是不错的。

总的来说，PKCS 12 号密钥库比 Sun 设计的任何一个专有选项都好得多。如果您使用 AES 和 SHA2 指定自己的 PasswordProtection 实例，并使用高迭代次数和良好的随机 salts，那么即使按照现代标准，这实际上也是一个非常可靠的设计。唯一真正丑陋的部分是可信证书的 40 位 RC2 加密，但如果你不关心证书的保密性，那么我们可以忽略这个细节，只考虑他们轻度混淆。至少 HMAC-SHA1 法案的使用是一种体面的完整性保护。

## PKCS 第 11 名

PKCS 11 号没什么好说的。这是一个标准接口，旨在与各种类型的*硬件*安全令牌一起使用:特别是硬件安全模块(HSM)。这些东西从 50 欧元的 T2u 盘到价值数万或数十万美元的网络连接的庞然大物。硬件通常是专有的和封闭的，所以很难说你的密钥将如何存储。不过，一般来说，无论是远程攻击者还是那些实际接触硬件并有大量时间的人，都可以获得有效的密钥保护。这并不能保证安全性，因为有很多方式密钥可能会从硬件中意外泄漏，正如最近英飞凌硬件中的 [ROCA 漏洞所展示的那样。尽管如此，对于高价值的密钥来说，经过充分测试的 HSM 可能是一个非常安全的选择。](https://crocs.fi.muni.cz/public/papers/rsa_ccs17)

我不会详细介绍如何建立一个 PKCS 11 号主要商店，因为它确实因供应商而异。至于 PKCS#12，虽然接口是标准化的，但是在该标准中有巨大的变化空间。在大多数情况下，您可以让 HSM 在安全硬件中生成密钥，并且永远不要导出私钥材料(可能除了用于备份)。

## 摘要

使用 HSM 或 PKCS#12 密钥库，并在存储密钥时指定手动密码保护参数。避免专有密钥存储。

或者，将密钥管理外包给其他人，并使用像 [Hashicorp Vault 这样的密钥管理系统(KMS)。](//www.vaultproject.io)