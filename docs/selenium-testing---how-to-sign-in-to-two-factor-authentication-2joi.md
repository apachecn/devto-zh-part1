# 硒测试-如何登录双因素认证？

> 原文：<https://dev.to/j_sakamoto/selenium-testing---how-to-sign-in-to-two-factor-authentication-2joi>

在本文中，我将解释如何在使用 Selenium 的端到端测试中登录受双因素身份验证保护的网站。

# 一种双因素认证

本文使用“认证者”移动应用程序，如“Google 认证者”或“Microsoft 认证者”等，处理获取 OTP(一次性密码)类型双因素认证。

[![screen shot of GoogleAuthenticator mobile app](img/8455d95e7720136b22b7531bfe1cd2d4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YxMaVM1F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/sample-by-jsakamoto/Selenium-E2ETest-for-OTP2FAAuth/master/.asset/google-authenticator-mobile-app-mini.jpg)

本文不处理通过 SMS 或电子邮件发送身份验证代码的双因素身份验证类型。

# 端到端测试登录 2FA 站点难吗？

当登录受二元身份验证保护的网站时，您应该从您自己的移动设备中的身份验证者移动应用程序获取“身份验证者代码”(也称为“OTP”)，并将其输入到登录表单中。

但是，在端到端测试中，测试程序如何在不访问认证器移动应用程序的情况下获得“认证器代码”呢？

除了禁用帐号的双因素认证功能进行测试之外，就没有别的办法了吗？

# 想法很简单！

别担心，你的测试代码可以做到。

你可以很容易地在测试程序中实现计算认证码的程序。

这就像在测试程序中放了一个“认证器”移动应用程序。

# 2FA 码怎么算？

我写了两个示例代码。一个是 C #，一个是 Java。

在 C#中，可以用库[“Otp 计算 2FA 代码。NET"](https://www.nuget.org/packages/Otp.NET/) 。

```
using OtpNet;
...

var otpKeyStr = "6jm7n6xwitpjooh7ihewyyzeux7aqmw2"; // <- this 2FA secret key.

var otpKeyBytes = Base32Encoding.ToBytes(otpKeyStr);
var totp = new Totp(otpKeyBytes);
var twoFactorCode = totp.ComputeTotp(); // <- got 2FA coed at this time! 
```

在 Java 中，可以用库[“aero gear-OTP-Java”](https://github.com/aerogear/aerogear-otp-java)计算 2FA 代码。

```
import org.jboss.aerogear.security.otp.Totp;
...

String otpKeyStr = "6jm7n6xwitpjooh7ihewyyzeux7aqmw2"; // <- this 2FA secret key.

Totp totp = new Totp(otpKeyStr);
String twoFactorCode = totp.now(); // <- got 2FA coed at this time! 
```

我感谢那些图书馆的贡献者。

# 全套 C#示例代码

您可以从下面的 GitHub 库获得完整的 C#示例代码。

[https://github . com/sample-by-jsakamoto/Selenium-e2e test-for-OTP 2 faauth](https://github.com/sample-by-jsakamoto/Selenium-E2ETest-for-OTP2FAAuth)

[![movie](img/32f622a819bf7e0bb1b547f1e8814495.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FmhcguVQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://raw.githubusercontent.com/sample-by-jsakamoto/Selenium-E2ETest-for-OTP2FAAuth/master/.asset/movie001.gif)

该库还包括测试目标的示例网站应用程序。

示例网站应用程序也作为 Docker 图像提供在下面的存储库中。

[https://hub . docker . com/r/samplebyjsakamoto/OTP 2 faauthwebappdemo/](https://hub.docker.com/r/samplebyjsakamoto/otp2faauthwebappdemo/)

# 结论

2FA 网站的 Selenium 端到端测试不用怕。

你可以用非常有用的 libraris 很容易地实现测试程序，在程序内部获得 2FA 代码。

测试愉快:)