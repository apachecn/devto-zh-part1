# 窃取你同意的朋友的 Gmail 密码

> 原文：<https://dev.to/wkmn/steal-your-consenting-friends-gmail-password-5hl3>

你有一个同意的朋友，你只是碰巧需要他们的密码吗？不要再看了，这里有一个完整的设置。

### 我是怎么做到的？

如果你只是想让代码跳到[毛刺](https://www.weckmann.me/posts/steal-your-consenting-friends-gmail-password/%22https://glitch.com/edit/#!/google-phishing%22)。请注意，此代码仅用于教育目的。

我从构建表单开始。该表单被设计成看起来与谷歌登录表单完全相同。

将查询参数`email`设置为用户的电子邮件。例如:

`google-phishing.glitch.me/?email=admin1@gmail.com`

一些 Javascript 处理对节点服务器的 post 请求。

导航到[https://google-phishing.glitch.me/get_log](https://www.weckmann.me/posts/steal-your-consenting-friends-gmail-password/%22https://google-phishing.glitch.me/get_log%22)查看收集的密码。

为什么会有人上当？我们中的大多数人看到谷歌标志都会感到安全。像 accounts-google-sign-in-appspot-com.glitch.me/…冰川这样的区域看起来是官方的。自然，我们希望登录来访问我们的数据。

无论你做什么，我都没有责任。

* * *

[通过电子邮件回复](//mailto:arpitbatra123@gmail.com?subject=Reply:%20Steal%20Your%20Consenting%20Friend)