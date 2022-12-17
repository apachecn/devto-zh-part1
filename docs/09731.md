# 使用 OpenSSL 实际生成自签名证书和私钥

> 原文：<https://dev.to/ecnepsnai/pragmatically-generating-a-self-signed-certificate-and-private-key-usingopenssl>

最近，我发现自己需要使用 OpenSSL 为一个 iOS 应用程序生成一个 HTTPS 服务器证书和私钥，令我惊讶的是完全没有 OpenSSL 的文档。

虽然有大量的函数文档，但 OpenSSL 真正缺乏的是如何将它们组合在一起的例子。这就像拥有了拼图的所有部分，但是没有成品的图片。许多在线示例是不安全的，或者使用了不推荐使用的函数，考虑到后果，这是一个严重的问题。

本教程将介绍如何用 c 语言为您的应用程序生成 RSA 或 ECDSA 私钥和 X509 服务器证书的基础知识。

**重要提示:**本教程是为 OpenSSL 的现代版 1.1.x 编写的，并不向后兼容 OpenSSL 1.0.x，如果你还在 1.0.x 列车上，那么**强烈**建议你升级应用。

# 生成私钥

任何 PKI 加密设置都需要一个私钥，通常有两种算法可供选择:RSA 和 ECDSA。

## ECDSA 诉 S. RSA

长期以来，RSA 一直是私钥的事实上的标准，如果正确使用，它仍然是安全的。RSA 密钥的安全性取决于密钥创建时的“大数”有多大。随着我们越来越擅长分解更小的数字，这个数字的推荐大小会越来越大。然而，ECDSA 密钥的创建不同于 RSA 密钥，更难破解。事实上，自 1985 年以来，在解决 ECDSA 密钥使用椭圆曲线离散对数问题(ECDLP)的问题上没有取得任何实质性的进展。因此，ECDSA 密钥提供了更高的安全性，而不需要更大的数量。例如:256 位 ECDSA 密钥相当于 3248 位 RSA 密钥。【ECDSA 密钥不仅比大多数 RSA 密钥更安全，而且占用的 CPU 也更少。CloudFlare 在他们的[关于算法](https://blog.cloudflare.com/ecdsa-the-digital-signature-algorithm-of-a-better-internet/)的文章中剖析了两者的性能。

```
 sign/s
256 bit ecdsa (nistp256)    9516.8
rsa 2048 bits               1001.8

(openssl 1.0.2 beta on x86_64 with enable-ec_nistp_64_gcc_128) 
```

Enter fullscreen mode Exit fullscreen mode

简而言之:ECDSA 密钥的 CPU 占用率比 RSA 密钥低 9 倍，同时提供了更高的安全性。

## 生成 RSA 私钥

```
#include <openssl/x509v3.h>
#include <openssl/rsa.h> 
/* ... */

OPENSSL_init_ssl(0, NULL);
OPENSSL_init_crypto(0, NULL);

EVP_PKEY * pkey      = EVP_PKEY_new();
if (!pkey) {
    // OpenSSL Error. Use `ERR_peek_last_error_line` to find out more.
    return NULL;
}

BIGNUM   * bigNumber = BN_new();
int        exponent  = RSA_F4;
RSA      * rsa       = RSA_new();

if (BN_set_word(bigNumber, exponent) < 0) {
    // OpenSSL Error. Use `ERR_peek_last_error_line` to find out more.
    goto cleanup;
}

if (RSA_generate_key_ex(rsa,
                        2048,
                        bigNumber,
                        NULL) < 0) {
    // OpenSSL Error. Use `ERR_peek_last_error_line` to find out more.
    goto cleanup;
}

if (!EVP_PKEY_set1_RSA(pkey, rsa)) {
    // OpenSSL Error. Use `ERR_peek_last_error_line` to find out more.
    goto cleanup;
}

cleanup:
    RSA_free(rsa);
    BN_free(bigNumber);

    return pkey; 
```

Enter fullscreen mode Exit fullscreen mode

## 生成 ECDSA 私钥

```
#include <openssl/x509v3.h>
#include <openssl/ecdsa.h> 
/* ... */

OPENSSL_init_ssl(0, NULL);
OPENSSL_init_crypto(0, NULL);

EVP_PKEY * pkey = EVP_PKEY_new();

EC_KEY * ecc = EC_KEY_new_by_curve_name(NID_X9_62_prime256v1);
if (!ecc) {
    // OpenSSL Error. Use `ERR_peek_last_error_line` to find out more.
    return NULL;
}

EC_KEY_set_asn1_flag(ecc, OPENSSL_EC_NAMED_CURVE);
if (EC_KEY_generate_key(ecc) < 0) {
    // OpenSSL Error. Use `ERR_peek_last_error_line` to find out more.
    return NULL;
}

if (EVP_PKEY_assign_EC_KEY(pkey, ecc) < 0) {
    // OpenSSL Error. Use `ERR_peek_last_error_line` to find out more.
    return NULL;
}

return pkey; 
```

Enter fullscreen mode Exit fullscreen mode

# 生成服务器证书

服务器证书是面向客户端的信息，它详细描述了与服务器的连接。它告诉客户端使用什么类型的密码，并验证服务器的身份。在这种情况下，我们将生成一个自签名证书，因此在您本地安装该证书之前，您的计算机不会信任它。

```
#include <openssl/x509v3.h> 
/* ... */

OPENSSL_init_ssl(0, NULL);
OPENSSL_init_crypto(0, NULL);

X509 * x509;
x509 = X509_new();
// 2 means Version 3.
X509_set_version(x509, 2L);

// Generate your private key using one of the above methods
EVP_PKEY * pkey;
X509_set_pubkey(x509, pkey);
EVP_PKEY_free(pkey);

// Generate a random serial number for this certificate.
// There are various ways to do this, depending on the platform.
// I'll leave this up to you.
unsigned long random_serial_number;

// Set Serial Number
ASN1_INTEGER_set(X509_get_serialNumber(x509), random_serial_number);

// Set Validity Date Range
// These value is appended to the systems current time stamp meaning that 0 = now.
X509_gmtime_adj((ASN1_TIME *)X509_get0_notBefore(x509), 0);
// 60 * 60 * 24 * NUMBER_OF_DAYS_TO_BE_VALID
X509_gmtime_adj((ASN1_TIME *)X509_get0_notAfter(x509), 7776000);

X509_NAME * name;
name = X509_get_subject_name(x509);

// Now to add the subject name fields to the certificate
// I use a macro here to make it cleaner.
#define addName(field, value) X509_NAME_add_entry_by_txt(name, field,  MBSTRING_ASC, (unsigned char *)value, -1, -1, 0) 
// The domain name or IP address that the certificate is issued for.
addName("CN", "ecn.io");

// The organizational unit for the cert. Usually this is a department.
addName("OU", "Certificate Authority");

// The organization of the cert.
addName("O",  "ecn.io Blog");

// The city of the organization.
addName("L",  "Vancouver");

// The state/province of the organization.
addName("S",  "British Columbia");

// The country (ISO 3166) of the organization
addName("C",  "CA");

X509_set_issuer_name(x509, name);

// Modern browsers ignore the CN subject field and refer only to the Subject
// Alternative Name extension, which allows you to specify
// multiple domain names, IP addresses, and more for a single certificate.
// The SAN value is in the following format:
// <TYPE>.<INDEX>:<VALUE>
// Common types are:
// - DNS
// - IP
// - email
// - URI
// Join multiple SAN entries using a comma.
// In this example, this certificate is for both the `ecn.io` and `*.ecn.io`
// domains. Which means it'll cover all single level subdomains for ecn.io
// (E.G. blog.ecn.io but not awesome.blog.ecn.io)
const char * san_value = "DNS.1:ecn.io,DNS.2:*.ecn.io";

X509_EXTENSION * extension = X509V3_EXT_conf_nid(NULL, NULL, NID_subject_alt_name, san_value);
if (X509_add_ext(x509, extension, -1) == 0) {
    // OpenSSL Error. Use `ERR_peek_last_error_line` to find out more.
    X509_EXTENSION_free(extension);
    return;
}
X509_EXTENSION_free(extension);

// Specify the encryption algorithm of the signature.
// SHA256 should suit your needs.
if (X509_sign(x509, pkey, EVP_sha256()) < 0) {
    // OpenSSL Error. Use `ERR_peek_last_error_line` to find out more.
    return;
} 
```

Enter fullscreen mode Exit fullscreen mode

# 保存您的私钥和证书

OpenSSL 为您提供了以各种格式将您的私钥和证书保存到磁盘的机制。有许多方法可以对证书和密钥进行编码，我将向您展示如何使用最常见的两种方法，PKCS12 (P12)和 PEM。

私钥的安全性是 PKI 最重要的组成部分，如果您不小心确保您的私钥是安全的，您的所有工作都很容易被浪费掉。你可以做很多事情来保证你的私钥安全，但是至少你应该改变文件权限，使其不具有全局可读性。Jeff Atwood 有一篇关于保持私钥私有的文章。

## PKCS12 V.S. PEM

如上所述，有许多方法可以对密钥进行编码，但是我将介绍的两种方法是 PKCS12 和 PEM。PKCS12(也称为 P12)是微软 PFX 格式的继任者，该格式被批评为过于复杂和难以使用，尽管 P12 仍然非常复杂，但谢天谢地，OpenSSL 几乎为您完成了所有繁重的工作，让您只需实现少量命令。

使用 P12 的最大优势在于，它们总是使用通行短语加密，并且可以在单个文件中的所谓“桶”中包含多个证书和密钥。

P12 的常见替代方案是隐私增强邮件(PEM)。PEM 是使用 base64 对证书和密钥进行编码的一个非常简单的标准，它可以作为 ASCII 文本轻松移动，而不是像 DER 和 P12 那样的二进制数据。

由于 PEM 严格来说只是一种编码，它不需要加密任何数据，因此通常用于 Web 服务器，因此不需要将硬编码的密码添加到配置文件中。此外，与 P12 不同，PEM 编码是每个文件一个对象，这意味着一个证书文件和一个密钥文件。

## 使用 PKCS12 保存

```
#include <openssl/x509v3.h>
#include <openssl/pkcs12.h> 
/* ... */

OPENSSL_init_ssl(0, NULL);
OPENSSL_init_crypto(0, NULL);

// Prompt the user for the password to encrypt the P12 file using
// DON'T USE A HARD-CODED PASSWORD OR I WILL PERSONALLY COME OVER
// THERE AND SCREAM AT YOU
const char * export_password;

PKCS12 * p12 = PKCS12_create(export_password, NULL, pkey, x509, NULL, 0, 0, PKCS12_DEFAULT_ITER, 1, NID_key_usage);

// The full path of the P12 file that you'll create
const char * save_path = "./server.p12";

FILE * f = fopen(save_path, "wb");

if (i2d_PKCS12_fp(f, p12) != 1) {
    // OpenSSL Error. Use `ERR_peek_last_error_line` to find out more.
    fclose(f);
    return;
}
fclose(f);
finished(path, nil); 
```

Enter fullscreen mode Exit fullscreen mode

## 使用 PEM 保存

```
#include <openssl/x509v3.h>
#include <openssl/pem.h> 
/* ... */

// The full path of the key and crt file that you'll create
const char * key_path = "./server.key";
const char * crt_path = "./server.crt";

FILE * f = fopen(key_path, "wb");

// Prompt the user for the password to encrypt the key file using
// DON'T USE A HARD-CODED PASSWORD OR I WILL PERSONALLY COME OVER
// THERE AND SCREAM AT YOU
const char * export_password;

// Here you write the private key (pkey) to disk. OpenSSL will encrypt the
// file using the password and cipher you provide.
if (PEM_write_PrivateKey(f,
                         pkey,
                         EVP_des_ede3_cbc(),
                         (unsigned char *)export_password,
                         (int)strlen(export_password),
                         NULL,
                         NULL) < 0) {
    // OpenSSL Error. Use `ERR_peek_last_error_line` to find out more.
    fclose(f);
}
fclose(f);

f = fopen(crt_path, "wb");

// Here you write the certificate to the disk. No encryption is needed here
// since this is public facing information
if (PEM_write_X509(f, x509) < 0) {
    // OpenSSL Error. Use `ERR_peek_last_error_line` to find out more.
    fclose(f);
}
fclose(f); 
```

Enter fullscreen mode Exit fullscreen mode

# 验证我们的服务器证书

OpenSSL 有一个漂亮的小函数，可以让您快速地将 X509 证书的摘要打印到一个文件(或标准输出):

```
X509 * cert = ...
X509_print_fp(stdout, cert); 
```

Enter fullscreen mode Exit fullscreen mode

检查用 RSA 私钥创建的证书:

```
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number: 1365365151 (0x5161d19f)
    Signature Algorithm: sha256WithRSAEncryption
        Issuer: CN=ecn.io, OU=Certificate Authority, O=ecn.io, L=Vancouver, C=CA
        Validity
            Not Before: Oct 30 20:33:01 2017 GMT
            Not After : Oct 31 02:33:00 2018 GMT
        Subject: CN=ecn.io, OU=Certificate Authority, O=ecn.io, L=Vancouver, C=CA
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (2048 bit)
                Modulus:
                    ...omitted...
                Exponent: 65537 (0x10001)
        X509v3 extensions:
            X509v3 Subject Alternative Name:
                DNS:ecn.io, DNS:*.ecn.io
    Signature Algorithm: sha256WithRSAEncryption
         ...omitted... 
```

Enter fullscreen mode Exit fullscreen mode

用 ECDSA 键:

```
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number: 1365365151 (0x5161d19f)
    Signature Algorithm: ecdsa-with-SHA256
        Issuer: CN=ecn.io, OU=Certificate Authority, O=ecn.io, L=Vancouver, C=CA
        Validity
            Not Before: Oct 30 20:33:01 2017 GMT
            Not After : Oct 31 02:33:00 2018 GMT
        Subject: CN=ecn.io, OU=Certificate Authority, O=ecn.io, L=Vancouver, C=CA
        Subject Public Key Info:
            Public Key Algorithm: id-ecPublicKey
                Public-Key: (256 bit)
                pub:
                    ...omitted...
                ASN1 OID: prime256v1
                NIST CURVE: P-256
        X509v3 extensions:
            X509v3 Subject Alternative Name:
                DNS:ecn.io, DNS:*.ecn.io
    Signature Algorithm: ecdsa-with-SHA256
         ...omitted... 
```

Enter fullscreen mode Exit fullscreen mode

如果您正在寻找使用 iOS 移动设备在旅途中轻松验证您的新 X509 证书，请查看我的一个项目: [TLS Inspector](https://tlsinspector.com) 。全球首个免费的 iOS 版& Libre X509 证书检查器( [GitHub](https://github.com/certificate-helper/tls-inspector) )。