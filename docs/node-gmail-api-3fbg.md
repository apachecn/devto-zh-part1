# 节点 Gmail API

> 原文：<https://dev.to/wkmn/node-gmail-api-3fbg>

我们将构建一个简单的 Gmail API 来方便通知的使用。请注意，这不是最安全的方法。

### 要求

*   节点 Js
*   小型 Linux 服务器
*   *扔掉* Gmail 账户

*查看我的例子* [节点通知](https://www.weckmann.me/posts/node-gmail-api/%22https://apis.clarkhacks.com/cgi/gui%22)

## Gmail 账户

**不要**使用您的日常 Gmail 帐户。创建一个*扔掉*，因为我们将需要启用**不太安全的应用**。

#### [创建新的 Gmail 帐户](https://www.weckmann.me/posts/node-gmail-api/%22https://accounts.google.com/SignUp%22)。

创建帐户时，请记下您的密码。

如果您不回复收到的电子邮件，您可以启用*休假*回复。

```
1\. In the top right, click Settings.
2\. Scroll down to the "Vacation responder" section.
3\. Select Vacation responder on.
4\. Fill in the date range, subject, and message.
5\. Under your message, check the box if you only want your contacts to see your vacation reply.
6\. At the bottom of the page, click Save Changes.

~ Google Support 
```

Enter fullscreen mode Exit fullscreen mode

#### 不太安全的应用

单击下面的链接。

[启用不太安全的应用](https://www.weckmann.me/posts/node-gmail-api/%22https://www.google.com/settings/security/lesssecureapps%22)。

### 复制模板

```
>$ git clone https://github.com/clarkhacks/Gmail-API
>$ cd Gmail-API
>$ npm install 
```

Enter fullscreen mode Exit fullscreen mode

您现在有了一个空白的`app.js`，完成的代码(例如/copy-paste)，并且安装了`node_modules`。

### 代码

将您的详细信息添加到`credentials.json`。这个文件不会对公众开放，但是，这就是为什么我们的代码不太安全。

```
// ./credentials.json

{
  "user": "user@gmail.com", // your email
  "pass": "AbCdEf!" // your password
} 
```

Enter fullscreen mode Exit fullscreen mode

包括你的变量。

```
// ./app.js

var
  express = require('express'),
  app = express(),
  credentials = require('./credentials.json' // email & pass 
```

Enter fullscreen mode Exit fullscreen mode

设置路线。

```
// ./app.js

app.get('/send', function(req, res) {
// ... 
```

Enter fullscreen mode Exit fullscreen mode

从`localhost/send`发送邮件

```
// ./app.js > In 'send' route.

var send = require('gmail-send')({
  user: credentials.user, // credentials.json - email
  pass: credentials.pass, // credentials.json - password
  to: "name@domain.tld", // Send To Email
  subject: "Hello!", // Subject
  text: "Sent from my Gmail API!", // Message
})({});

return res.send("To: " + sendTo + "<br>" + "Subject: " + subject + "<br>" + sendWhat 
```

Enter fullscreen mode Exit fullscreen mode

使用查询获取电子邮件、主题和消息。

```
// ./app.js > In 'send' route.

  var sendTo = req.query.sendTo,
    subject = req.query.subject,
    sendWhat = req.query.sendWhat
  if (sendTo && sendWhat && sendWhat) {
    var send = require('gmail-send')({
      user: credentials.user,
      pass: credentials.pass,
      to: sendTo,
      subject: subject,
      text: sendWhat,
    })({});

    return res.send("To: " + sendTo + "<br>" + "Subject: " + subject + "<br>" + sendWhat
  }
   else {
    return res.send('No Email Specified.'
  } 
```

Enter fullscreen mode Exit fullscreen mode

### [使用中的](#in-use)

在您的浏览器中访问`http://localhost/send?sendTo=user@domain.tld&subject=Hello&sendWhat=Greetings%21`。

您应该会得到类似这样的响应:

```
To: user@domain.tld
Subject: Hello
Greetings! 
```

Enter fullscreen mode Exit fullscreen mode

## 这个能做什么？

我有一个这样的服务正在运行(我用 json web 令牌保护它)，当`x`发生时，我用它来通知我的手机。

您可以通过用 javascript 发送请求在前端使用它。我建议保护 API 本身。

给你的手机发短信可能会很贵(取决于你发短信的频率)。解决这个问题的一个方法是使用电子邮件！发送电子邮件到 [YouNumber@smsgateway.tld](mailto:YouNumber@smsgateway.tld) ，你的手机应该会收到一条短信。

下面是一些短信网关:

```
Verizon number@vtext.com
ATT number@txt.att.net
Cricket number@sms.mycricket.
T-Mobile    number@@tmomail.net

For more just Google :) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

[通过电子邮件回复](//mailto:arpitbatra123@gmail.com?subject=Reply:%20Node%20Gmail%20API)