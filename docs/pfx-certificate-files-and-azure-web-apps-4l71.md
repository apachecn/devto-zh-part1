# PFX 证书文件和 Azure Web 应用

> 原文：<https://dev.to/tylerd/pfx-certificate-files-and-azure-web-apps-4l71>

## 我今天怎么烧伤了...

我需要编写一个简单的 SAML 1.1 提供程序，它将生成一个 SAML 令牌并使用. pfx 证书对其进行签名。

所以我在我的 MVC 控制器中写了这段代码...

```
var cert = new X509Certificate2(pfxFile, "myPassword");

var privateKey = cert.PrivateKey; 
```

使用私钥对令牌进行签名，在本地运行时没有出现任何问题。然而，当我将网站发布到 Azure Web Apps 时，我得到了这个错误。

```
[CryptographicException: The system cannot find the file specified.] System.Security.Cryptography.CryptographicException.ThrowCryptographicException(Int32 hr) +33
System.Security.Cryptography.X509Certificates.X509Utils.\_LoadCertFromFile(String fileName, IntPtr password, UInt32 dwFlags, Boolean persistKeySet, SafeCertContextHandle& pCertCtx) +0 
System.Security.Cryptography.X509Certificates.X509Certificate.LoadCertificateFromFile(String fileName, Object password, X509KeyStorageFlags keyStorageFlags) +218 
System.Security.Cryptography.X509Certificates.X509Certificate..ctor(String fileName, String password) +63 
System.Security.Cryptography.X509Certificates.X509Certificate2..ctor(String fileName, String password) +58 WAWSPfxTest.Controllers.HomeController.Index() +180 
```

我确认文件确实存在。

## 发生了什么？？

事实证明，当您加载证书时，系统将使用本地目录来存储密钥(？？)

密钥的默认位置是本地用户配置文件下的，对于 Azure Web Apps，没有本地用户配置文件目录。

## 键存储救援标志

经过反复试验，我发现 **MachineKeySet** 标志可以在我的 Azure Web 应用上工作。所以通过快速修改代码，它又开始工作了。

```
var cert = new X509Certificate2(pfxFile, "myPassword", X509KeyStorageFlags.MachineKeySet);

var privateKey = cert.PrivateKey; 
```

> **注意:** [这篇 Azure 博客文章](https://azure.microsoft.com/en-us/blog/using-certificates-in-azure-websites-applications/)概述了如何在 Azure Web 应用中上传和使用证书。

希望这能帮助到某个人。