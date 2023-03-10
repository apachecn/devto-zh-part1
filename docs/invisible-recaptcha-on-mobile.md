# 手机上的隐形 reCAPTCHA？

> 原文：<https://dev.to/dimitarnestorov/invisible-recaptcha-on-mobile>

为了防止在我们的登录/注册表单上使用暴力，我们在网站上实现了谷歌的[隐形 reCAPTCHA](https://developers.google.com/recaptcha/docs/invisible) 。然而，我们不能在我们开发的应用程序上向移动用户展示这一点。所以我想知道有没有一种简单又安全的方法让用户登录我们的应用程序？

现在，首先想到的是实现一个移动登录/注册端点，但是攻击者很容易得到这个端点并开始暴力破解它。

我想到的下一件事是向 API 请求一个令牌，并使用它来登录用户，但是这将意味着在 X 次尝试失败后阻止一个 IP 地址，并且它开始感觉像我们将开发我们自己版本的 CAPTCHA。

另一种方法是创建类似 OAuth 的东西，但 UX 会受到影响，因为用户必须看到一个网页，并且不时地会看到验证码。

你有什么简单的实现方法可以防止攻击者暴力破解应用程序吗？

就目前而言，第二种方法似乎是最合理的。我以前偶然发现过这样的网站，它给了 10 次用 X 电子邮件地址登录的尝试，然后阻止那个用户登录一段时间。

[https://www.youtube.com/embed/hdJ7PTdPaqc](https://www.youtube.com/embed/hdJ7PTdPaqc)

我记得这个视频曾经提交过一次这个表格，但是 reCAPTCHA 没有出现。我只能猜测那部分被修剪了。似乎 reCAPTCHA 通过简单地比较时间戳来决定何时“测试”用户。

附注:我想不出一个标题不像点击诱饵ðÿ˜…