# libna 快速参考

> 原文：<https://dev.to/paragonie/libsodium-quick-reference>

如果你不熟悉密码学术语，[请先阅读本页](https://dev.to/paragonie/you-wouldnt-base64-a-password-cryptography-terms-and-concepts-for-developers)。它涵盖了从术语(如*随机数*)到概念(如*公钥加密*)的一切。

* * *

在 [Day Camp 4 开发者](https://daycamp4developers.com)大会上，我发表了一篇名为*在 PHP 7.2* 中用钠烹饪的演讲，主要是现场演示 lib 钠提供的各种加密特性。与会者问我的一个问题是关于知道使用哪个特性来解决特定的问题。我怀疑很多人都会遇到这种问题，所以这里有一个快速参考表，后面有详细的解释。

在下表中，所有加密模式都使用[认证加密](https://paragonie.com/blog/2015/05/using-encryption-and-authentication-correctly)。

| lib 钠函数 | 类型 | 注释/使用案例 |
| --- | --- | --- |
| lib 钠函数 | 类型 | 注释/使用案例 |
| 加密 _pwhash | 密码哈希 | 安全的密码存储，从用户输入中获取密钥 |
| [crypto_generichash](#crypto-generichash) | 加密哈希函数 | 抗碰撞和抗原像；替换 MD5/SHA1/等等。 |
| [crypto_shorthash](#crypto-shorthash) | 短输入哈希函数 | 哈希表、布隆过滤器、缓存查找键 |
| [crypto_auth](#crypto-auth) | 对称认证 | 双方都可以验证和/或伪造消息 |
| [加密签名](#crypto-sign) | 不对称认证 | 数字签名(任何人都可以验证，只有发件人可以签名) |
| [crypto_aead_*](#crypto-aead) | 对称加密 | 带有附加数据的共享秘密加密 |
| [crypto_secretbox](#crypto-secretbox) | 对称加密 | 与 NaCl、TweetNaCl 等兼容。 |
| [crypto_box](#crypto-box) | 不对称加密 | 发送方和接收方都可以解密 |
| [加密盒密封](#crypto-box-seal) | 不对称加密 | 只有接收者可以解密；发件人匿名 |

# 用 lib 钠解决常见任务

**我需要存储一个加密或散列的密码([实际上从未加密，只是散列了](https://paragonie.com/blog/2015/08/you-wouldnt-base64-a-password-cryptography-decoded#passwords) )**

使用[T0 和`crypto_pwhash_str_verify()`T3。](#crypto-pwhash)

* * *

**我需要加密一个字符串，然后稍后解密(在*同一个*机器上)**

为简单起见:使用[T0 和`crypto_secretbox_open()`T3。](#crypto-secretbox)

其实用 [`crypto_aead_*`](#crypto-aead) 更好(特别是如果你用的是[样本代码](#crypto-aead-sample-php)，但是`secretbox` API 简单安全。

* * *

**我需要加密一个字符串，然后稍后解密(在另一台*不同的*机器上)**

*看情况！*

你有双向数据流(`A <=> B`)还是单向数据流(`A -> B`)？

如果你只在节点 A 上加密，只在节点 B 上解密，你将需要 [`crypto_box_seal()`和`crypto_box_seal_open()`](#crypto-box-seal) 。这可以防止发送方(节点 A)在消息发送到接收方(节点 B)后对其进行解密。

否则，你会简单地使用 [`crypto_box()`和`crypto_box_open()`](#crypto-box) 。

* * *

**我之前使用了 mcrypt_encrypt()或 openssl_encrypt()，需要迁移我的数据**

首先，像往常一样解密您的数据，然后使用 [`crypto_secretbox()`和`crypto_secretbox_open()`](#crypto-secretbox) 重新加密。

* * *

**我之前使用了 OpenSSL _ public _ encrypt()/OpenSSL _ private _ decrypt()，需要迁移我的数据**

首先，像往常一样解密您的数据，然后使用 [`crypto_box`](#crypto-box) 或 [`crypto_box_seal`](#crypto-box-seal) 重新加密(请参见之前关于不同机器之间加密的答案，以了解您需要哪一种机器)。

# lib 钠功能分析

**PHP 开发人员:**在 PHP 7.2 之前的版本中，来自 PECL 的 lib 钠，下面的函数是在`Sodium`命名空间中定义的。在 PHP 7.2 中，名称空间被抛弃，取而代之的是前缀`sodium_`(以符合 PHP 内部开发标准)。

例如，在 PHP 7.1 和更低版本中，使用来自 PECL 的 ext/钠，`crypto_secretbox()`的示例片段可能如下所示:

```
<?php
// PHP < 7.2 with `pecl install libsodium`
$key = str_repeat("\x80", 32);
$nonce = random_bytes(24);
$data = $nonce . \Sodium\crypto_secretbox("Test", $nonce, $key);
```

当 PHP 7.2 发布时，界面将改为如下所示:

```
<?php
// PHP >= 7.2
$key = str_repeat("\x80", 32);
$nonce = random_bytes(24);
$data = $nonce . sodium_crypto_secretbox("Test", $nonce, $key);
```

对于用户来说，你的代码应该是这样的:

```
<?php
// PHP >= 7.2
$key = str_repeat("\x80", 32);
$nonce = random_bytes(24);
$data = $nonce . ParagonIE_Sodium_Compat::crypto_secretbox("Test", $nonce, $key);
```

在下面的例子中，当我们定义一个 lib 钠函数时，实际的函数名要么是命名空间，要么是前缀，要么是`ParagonIE_Sodium_Compat`上的静态方法。为了简单起见，我们使用简单的 libna 名称，因为该部分保持不变。

## 哈希函数

### crypto_pwhash()

选择你自己的冒险:

*   密钥派生:`crypto_pwhash()`
*   密码散列/验证:`crypto_pwhash_str()`和`crypto_pwhash_str_verify()`

libna 使用 Argon2，即[密码哈希竞赛](https://password-hashing.net)的获胜者。更具体地说，它在所有当前版本中使用 Argon2i(抗侧信道变体)，但未来版本可能会将默认设置切换为 Argon2id，这更能抵抗 GPU 攻击。

每当您接受**用户提供的输入**(即密码)并且您的目标是:

*   将他们的密码转换成加密密钥，或者
*   将其存储起来，以便日后进行安全验证

请务必查看覆盖`crypto_pwhash` 的官方 lib 钠文档，了解如何使用它的详细信息。

#### crypto_pwhash()示例代码(PHP 7.2+)

```
<?php

/* This uses Argon2i with two numeric constants that correspond to
 * the number of passes (OPSLIMIT) and the amount of memory to use
 * (MEMLIMIT). A higher OPSLIMIT helps against CPU attacks, while a
 * higher MEMLIMIT helps against GPU attacks.
 */
$storeInDatabase = sodium_crypto_pwhash_str(
    $password, 
    SODIUM_CRYPTO_PWHASH_OPSLIMIT_INTERACTIVE,
    SODIUM_CRYPTO_PWHASH_MEMLIMIT_INTERACTIVE
);

/* Once that's stored, you can just test against the hash like so: */
if (sodium_crypto_pwhash_str_verify($password, $storeInDatabase)) {
    /* Logged in! */
} else {
    /* Incorrect password. */
}
```

### crypto_generichash()

对于 PHP 开发人员:这有点像`hash()`，除了如果你传递一个键，它也可以是`hash_hmac()`，并且它只允许 BLAKE2b(所以你不必选择算法)。

你想在任何你通常使用`hash()`、`md5()`或`sha1()`的地方使用`crypto_generichash()`(除非“通常”意味着会引起密码学家奇怪地看着你)。`crypto_generichash()`之所以吸引人，是因为它具备以下所有特征:

*   抗碰撞的
*   抗原像的
*   对长度扩展攻击免疫
*   比 SHA256 更安全
*   比 MD5 更快

参见:[`crypto_generichash`](https://download.libsodium.org/doc/hashing/generic_hashing.html)上的 lib 钠文档。

#### crypto _ generichash()示例代码(PHP 7.2+)

```
<?php

$someData = 'This is a test message';
$someSecretKey = random_bytes(32);

$hash      = sodium_crypto_generichash($someData);
var_dump(mb_strlen($hash, '8bit')); /* int(32) */

$blake2mac = sodium_crypto_generichash($someData, $someSecretKey);
var_dump(mb_strlen($blake2mac, '8bit')); /* int(32) */

$truncated = sodium_crypto_generichash($someData, '', 16);
var_dump(mb_strlen($truncated, '8bit')); /* int(16) */
```

### crypto_shorthash()

SipHash，驱动`crypto_shorthash()`的算法，是一个安全的键控伪随机函数。或者，换句话说，它是一个类似 BLAKE2b 的哈希函数，只不过它只能与密钥一起使用，并且它的 64 位输出大小太小，无法抵抗冲突或免疫暴力搜索。

不要在适合使用`crypto_generichash()`的地方使用`crypto_shorthash()`。SipHash 更适合构建带有每个请求的键的哈希表，以抵抗哈希冲突拒绝服务攻击。在微基准很重要的情况下，您还可以将它用于 Bloom filters 和缓存查找。但是一般来说，`crypto_generichash()`和`crypto_pwhash()`可能是这项工作更好的工具。

参见:[`crypto_shorthash`](https://download.libsodium.org/doc/hashing/short-input_hashing.html)上的 lib 钠文档。

#### crypto_shorthash()示例代码(PHP 7.2+)

```
<?php
$key = random_bytes(16);
$input = ['apple', 'boy', 'cat', 'dog', 'echo'];
$mapped = [];
foreach ($input as $item) {
    $hash = sodium_crypto_shorthash($item, $key);
    $mapped[bin2hex($hash)] = $item;
}
var_dump($mapped);
```

## 证明

### 加密认证()

`crypto_auth()`来自 NaCl，用于提供对称密钥认证。如果你了解 HMAC，你知道这是什么。如果你的目标是与其他 NaCl/libna 项目进行互操作，PHP 开发人员只需要`crypto_auth()`和它的对等物`crypto_auth_verify()`。否则，`hash_hmac()`和`hash_equals()`完成同样的事情。

`crypto_auth()`实际上是 HMAC-SHA512 被截断为 32 字节。

值得注意的是，使用 HMAC，任何能够验证消息的一方也能够对消息进行签名。这就是对称密钥认证的含义。如果您需要一个实体能够签名，但是宇宙中的其他人只能验证，那么您希望用`crypto_sign()`来代替。

参见:[`crypto_auth`](https://download.libsodium.org/doc/secret-key_cryptography/secret-key_authentication.html)上的 lib 钠文档。

#### crypto_auth()示例代码(PHP 7.2+)

```
<?php
$key = random_bytes(32);
$message = 'authenticate me';

/* Get the message authentication code (MAC): */
$mac = sodium_crypto_auth($message, $key);

/* Verify a message: */
if (sodium_crypto_auth_verify($mac, $message, $key)) {
    /* Verified */
} else {
    /* Message has been tampered with; discard */
}
```

### 加密签名()

`crypto_sign`函数有两种风格:

*   `crypto_sign()`和`crypto_sign_open()`，对于一气呵成发送签名消息很有用。
*   `crypto_sign_detached()`和`crypto_sign_verify_detached()`，哪个更普遍有用。

通常，您会发现自己在 PHP 领域更倾向于后两种，但是前两种也很有用。

与`crypto_auth`不同，`crypto_sign`利用*非对称*(又名*公钥*)密码术。你用你的秘密密钥签署一条消息，任何人用你的公开密钥都能验证这条消息。

再次强调，T2 的 lib 钠文档值得一读。

#### crypto_sign()示例代码(PHP 7.2+)

```
<?php
$mySigningKeypair = sodium_crypto_sign_keypair();
$secretKey = sodium_crypto_sign_secretkey($mySigningKeypair);
$publicKey = sodium_crypto_sign_publickey($mySigningKeypair);

$message = 'authenticate me';

/* Sign the message, using your secret key (which is NOT given out): */
$signature = sodium_crypto_sign_detached($message, $secretKey);

/* Now validate the signature with your public key (which IS given out): */
if (sodium_crypto_sign_verify_detached($signature, $message, $publicKey)) {
    /* Message was signed by me. */
} else {
    throw new Exception('Invalid signature. Do not trust the message.');
}
```

## 加密

### 加密 _aead()

AEAD 是一个密码学家的首字母缩写词，代表 **A** 认证的 **E** 与 **A** 关联的 **D** ata 的加密。总的想法是你有:

*   你的明文被加密成密文。
*   其他一些不接触密文的数据。
*   一个认证标签，包含密文和其他数据。

当您有预共享密钥或协商密钥(例如，通过椭圆曲线 Diffie-Hellman)时，您希望使用`crypto_aead`。

AEAD 模式是 2017 年加密的首选方式。libna 为其 AEAD 接口提供了几个选项，所有这些选项都实现了对称加密。您可能希望按照偏好顺序使用:

*   (一天):`crypto_aead_encrypt()`和`crypto_aead_decrypt()`
    *   尚不可用。
    *   将在[凯撒](https://competitions.cr.yp.to/caesar.html)决赛选手选拔结束后发售。
*   `crypto_aead_xchacha20poly1305_ietf_*()`
    *   允许您使用非常大的随机数，它可以安全地随机生成，没有意外重用随机数的实际风险。
*   `crypto_aead_chacha20poly1305_ietf_*()`
    *   标准化的 ChaCha20-Poly1305 变体。
*   `crypto_aead_aes256gcm_*()`
    *   需要专门的硬件支持。只有在保证每台设备都配备现代处理器的情况下，才使用此选项。
*   `crypto_aead_chacha20poly1305_*()`
    *   在 IETF 标准化用于 TLS 的 nonce/计数器大小之前实现。支持度较低。

lib 钠文档的 [AEAD 部分有更多关于密钥/随机数大小等的细节。](https://download.libsodium.org/doc/secret-key_cryptography/aead.html)

#### crypto_aead_*()示例代码

```
<?php
/**
 * Wrap crypto_aead_*_encrypt() in a drop-dead-simple encryption interface
 *
 * @link https://paragonie.com/b/kIqqEWlp3VUOpRD7
 * @param string $message
 * @param string $key
 * @return string
 */
function simpleEncrypt($message, $key)
{
    $nonce = random_bytes(24); // NONCE = Number to be used ONCE, for each message
    $encrypted = ParagonIE_Sodium_Compat::crypto_aead_xchacha20poly1305_ietf_encrypt(
        $message,
        $nonce,
        $nonce,
        $key
    );
    return $nonce . $encrypted;
}

/**
 * Wrap crypto_aead_*_decrypt() in a drop-dead-simple decryption interface
 *
 * @link https://paragonie.com/b/kIqqEWlp3VUOpRD7
 * @param string $message - Encrypted message
 * @param string $key     - Encryption key
 * @return string
 * @throws Exception
 */
function simpleDecrypt($message, $key)
{
    $nonce = mb_substr($message, 0, 24, '8bit');
    $ciphertext = mb_substr($message, 24, null, '8bit');
    $plaintext = ParagonIE_Sodium_Compat::crypto_aead_xchacha20poly1305_ietf_decrypt(
        $ciphertext,
        $nonce,
        $nonce,
        $key
    );
    if (!is_string($plaintext)) {
        throw new Exception('Invalid message');
    }
    return $plaintext;
}

$secretKey = random_bytes(32);
$message = 'Test message';

/* Encrypt the message: */
$ciphertext = simpleEncrypt($message, $secretKey);

/* Decrypt the message: */
try {
    $decrypted = simpleDecrypt($ciphertext, $secretKey);
    var_dump(hash_equals($decrypted, $message));
    /* bool(true) */
} catch (Exception $ex) {
    /* Someone is up to no good */
    exit(255);
}
```

### crypto_secretbox()

`crypto_secretbox()`来自 NaCl，实现对称(共享密钥)认证加密。`secretbox`接受一个很大的 nonce，它可以安全地随机生成(PHP 中的`random_bytes()`，lib 钠中的`randombytes_buf()`，而且几乎不用担心重用 nonce。

当您有预共享密钥或协商密钥(例如，通过椭圆曲线 Diffie-Hellman)时，您希望使用`crypto_secretbox()`。

如果你必须在`secretbox`和`aead_xchacha20poly1305`之间选择，选择 AEAD 模式。否则，使用`secretbox`。

在的[处可以找到`crypto_secretbox`的 libna 文档的相关章节。](https://download.libsodium.org/doc/secret-key_cryptography/authenticated_encryption.html)

#### crypto_secretbox_*()示例代码(PHP 7.2+)

```
<?php
$key = random_bytes(32);
$message = 'Yellow submarine';

/* For each encryption: */
    $nonce = random_bytes(24); /* Never repeat this! */
    $ciphertext = sodium_crypto_secretbox($message, $nonce, $key);
    $plaintext = sodium_crypto_secretbox_open($ciphertext, $nonce, $key);
```

### crypto_box()

`crypto_box`来自 NaCl，实现认证的非对称(也称为公钥)加密。有了`crypto_box()`，发送方和接收方都可以阅读消息，并验证对方是否发送了消息。然而，它允许每一方协商他们自己的私钥，并相互共享不同的秘密，所以与一个人交谈不会让你偷听另一个人与他们的谈话。

您希望在通常实现 RSA 加密的地方使用`crypto_box()`。它更常见于交互式协议(例如消息应用程序)。

要了解更多信息，请参见`crypto_box` 的[lib 钠文档。](https://download.libsodium.org/doc/public-key_cryptography/authenticated_encryption.html)

#### crypto_box()示例代码(PHP 7.2+)

```
<?php
# Setup:

/* On Node A */
$aliceKeypair = sodium_crypto_box_keypair();
    $aliceSecretKey = sodium_crypto_box_secretkey($aliceKeypair);
    $alicePublicKey = sodium_crypto_box_publickey($aliceKeypair);
    // Then share $alicePublicKey with Node B

/* On Node B: */
$bobKeypair = sodium_crypto_box_keypair();
    $bobSecretKey = sodium_crypto_box_secretkey($bobKeypair);
    $bobPublicKey = sodium_crypto_box_publickey($bobKeypair);
    // Then share $bobPublicKey with Node A

# Transmission:

/* Sending from Node A to Node B */
    $message 'Hi there! :)';
    $aliceToBob = $aliceSecretKey . $bobPublicKey;
    $nonce = random_bytes(24); /* Never repeat this! */
    $ciphertext = $nonce . sodium_crypto_box($message, $nonce, $aliceToBob);

/* On Node B, receiving an encrypted message from Node A */
    $bobToAlice = $bobSecretKey . $alicePublicKey;
    $nonce = mb_substr($ciphertext, 0, 24, '8bit');
    $encrypted = mb_substr($ciphertext, 24, null, '8bit');
    $decrypted = sodium_crypto_box_open($encrypted, $nonce, $bobToAlice);

# Alternatively:

/* Sending from Node B to Node A */
    $message 'Hello yourself.';
    $bobToAlice = $bobSecretKey . $alicePublicKey;
    $nonce = random_bytes(24); /* Never repeat this! */
    $ciphertext = $nonce . sodium_crypto_box($message, $nonce, $bobToAlice);

/* On Node A, receiving an encrypted message from Node B */
    $aliceToBob = $aliceSecretKey . $bobPublicKey;
    $nonce = mb_substr($ciphertext, 0, 24, '8bit');
    $encrypted = mb_substr($ciphertext, 24, null, '8bit');
    $decrypted = sodium_crypto_box_open($encrypted, $nonce, $aliceToBob);
```

### crypto_box_seal()

`crypto_box_seal()`是一个 libna 独占，它允许发送者加密只有接收者才能解密的消息。密封 API 有时被称为**匿名公钥加密**，因为尽管它提供了密文完整性，但发送者并不像`crypto_box`那样被认证。

每当您加密不希望发送者能够解密的信息时，您都希望使用`crypto_box_seal`。例如，将加密数据存储在在线数据库中，该数据库只能用气隙机器中离线保存的密钥解密。

如果你试图在`crypto_box()`和`crypto_box_seal()`之间做出决定，问问自己执行加密的系统是否能够解密他们发送的信息。如果答案是否定的，你想要`crypto_box_seal()`。如果您仍然需要对发送方进行身份验证，请在发送方的`crypto_box_seal()`前使用`crypto_sign()`，在接收方的`crypto_box_seal_open()`后使用`crypto_sign_open()`。

与往常一样，[`crypto_box_seal`](https://download.libsodium.org/doc/public-key_cryptography/sealed_boxes.html)的文档深入解释了它的用法。

#### crypto_box_seal()示例代码(PHP 7.2+)

```
<?php
# Setup:

/* On Node B: */
$bobKeypair = sodium_crypto_box_keypair();
    $bobPublicKey = sodium_crypto_box_publickey($bobKeypair);
    // Then share $bobPublicKey with Node A

# Transmission:

/* Sending from Node A to Node B */
    $message 'Hi there! :)';
    $ciphertext = sodium_crypto_box_seal($message, $bobPublicKey);

/* On Node B, receiving an encrypted message from Node A */
    $decrypted = sodium_crypto_box_seal_open($ciphertext, $bobKeypair);
```

# 其他功能

如果您对这里没有列出的任何 lib 钠函数感到困惑，请参考官方 lib 钠文档。

* * *

这篇文章最初发表在我们公司的网站上。我们所有的博客文章都是在知识共享许可下发布的。