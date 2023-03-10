# 认证你的安卓和 iOS 应用。

> 原文：<https://dev.to/drankolq/certificate-pinning-your-android-and-ios-apps>

当我们，开发者，在开发任何类型的软件时，我们不能忘记安全ðÿ”。我们应该使用的最低安全措施是 HTTPS 作为客户端(在这种情况下，是 Android/iOS 应用程序)和服务器之间共享信息的协议，然后是更新的加密协议，如 TLS 1.2 (SSL 3.0 易受攻击！您可能认为使用 HTTPS 就足够了，但在某些情况下，如银行应用程序，敏感数据可能会在我们的客户端和服务器之间发送，这可能会有风险。
默认情况下，当建立 TLS 连接时，客户端检查两件事:

1.  服务器的证书与请求的主机名匹配。
2.  服务器的证书有一个事实链，可以追溯到受信任的根证书。

它不做的是检查证书是否是您知道您的服务器正在使用的特定证书，这是一个可能的安全漏洞:如果客户端受到威胁并且安装了不安全的证书，有人可能会进行中间人攻击。

[![](img/b3f31a4e0dee8da92cfc88ceb67dff19.png "Root CA, intermediate CA and Medium certificate")](https://res.cloudinary.com/practicaldev/image/fetch/s--Qhn6vn5S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A6H-NohaUwM_V3scd8tdjxw.png) 
这个问题的解决方案是证书锁定(certificate pinning ):在我们的客户机上存储一个证书，以确保发出的任何 SSL 请求都与服务器上的请求相匹配。让我来解释一下如何在 Android 和 iOS 应用程序上实现。

### 安卓

[OkHttp](http://square.github.io/okhttp) lib 提供一个 CertificatePinner 类，添加到 OkHttpClient 实例中。锁定主机最简单的方法是在连接失败时打开锁定断开的配置并读取预期的配置。

```
CertificatePinner certificatePinner = new CertificatePinner.Builder()
         .add("mydomain.com", "sha256/AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA=")
         .build();
     OkHttpClient client = OkHttpClient.Builder()
         .certificatePinner(certificatePinner)
         .build(); 
```

Enter fullscreen mode Exit fullscreen mode

请求执行后，您会在控制台上看到这条消息:

```
javax.net.ssl.SSLPeerUnverifiedException: Certificate pinning failure!
   Peer certificate chain:
     sha256/afwiKY3RxoMmLkuRW1l7QsPZTJPwDS2pdDROQjXw8ig=: CN=mydomain.com, OU=PositiveSSL
     sha256/klO23nT2ehFDXCfx3eHTDRESMz3asj1muO+4aIdjiuY=: CN=COMODO RSA Secure Server CA
     sha256/grX4Ta9HpZx6tSHkmCrvpApTQGo67CYDnvprLg5yRME=: CN=COMODO RSA Certification Authority
     sha256/lCppFqbkrlJ3EcVFAkeip0+44VaoJUymbnOaEUk7tEU=: CN=AddTrust External CA Root
   Pinned certificates for mydomain.com:
     sha256/AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA=
   at okhttp3.CertificatePinner.check(CertificatePinner.java)
   at okhttp3.Connection.upgradeToTls(Connection.java)
   at okhttp3.Connection.connect(Connection.java)
   at okhttp3.Connection.connectAndSetOwner(Connection.java) 
```

Enter fullscreen mode Exit fullscreen mode

该例外将为您提供服务器的证书公钥哈希。把它们贴在证书上就完成了！

```
CertificatePinner certificatePinner = new CertificatePinner.Builder()
       .add("mydomain.com", "sha256/afwiKY3RxoMmLkuRW1l7QsPZTJPwDS2pdDROQjXw8ig=")
       .add("mydomain.com", "sha256/klO23nT2ehFDXCfx3eHTDRESMz3asj1muO+4aIdjiuY=")
       .add("mydomain.com", "sha256/grX4Ta9HpZx6tSHkmCrvpApTQGo67CYDnvprLg5yRME=")
       .add("mydomain.com", "sha256/lCppFqbkrlJ3EcVFAkeip0+44VaoJUymbnOaEUk7tEU=")
       .build(); 
```

Enter fullscreen mode Exit fullscreen mode

### iOS

iOS 解决方案没有这么简单，因为您需要将证书本身存储在您的应用程序中。在我的例子中，我使用 Alamofire 作为 Swift 的 HTTP 客户端库。
首先需要在**中获取服务器的证书。der** 格式，并将其添加到您的 iOS 项目中。

```
openssl s_client -showcerts -servername mydomain.com -connect mydomain.com:443
 </dev/null | openssl x509 -outform DER > mydomainCert.der 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们启用证书锁定:为此，我们需要 ServerTrustPolicy 和 SessionManager 对象。第一个将定义将在该过程中使用的主机名和证书:

```
var serverTrustPolicies = [
    "mydomain.com": .pinCertificates(
    certificates: ServerTrustPolicy.certificates(),
    validateCertificateChain: true,
    validateHost: true
  ),
] 
```

Enter fullscreen mode Exit fullscreen mode

ServerTrustPolicy.certificates()将返回所有存储的证书，布尔值将验证证书链和主机名。
最后，使用这个信任策略创建一个 SessionManager 对象:

```
var sessionManager = SessionManager(serverTrustPolicyManager: ServerTrustPolicyManager(policies: serverTrustPolicies!)) 
```

Enter fullscreen mode Exit fullscreen mode

搞定了。âœ".只需使用这个 *sessionManager* 对象来执行请求

```
sessionManager.request("https://mydomain.com/api", method: .get, headers: headers)... 
```

Enter fullscreen mode Exit fullscreen mode

欢迎反馈！希望它有用

#### 来源

ok http:[https://github.com/square/okhttp/wiki/HTTPS](https://github.com/square/okhttp/wiki/HTTPS)
阿拉莫菲尔:[https://github.com/Alamofire/Alamofire#security](https://github.com/Alamofire/Alamofire#security)