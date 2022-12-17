# node . js SparkPost 使用指南

> 原文：<https://dev.to/sparkpost/a-guide-to-using-sparkpost-with-nodejs>

作为 [SparkPost](https://www.sparkpost.com) 的开发者倡导者，我编写了许多示例应用程序。我的背景主要是前端开发，因此我最擅长的语言是 JavaScript。感谢 Node.js，我也是一名不错的后端开发人员。这是否意味着我现在是全栈开发者了？无论如何，对我来说重要的是，我们有一个非常棒的 node . js SparkPost 客户端库。所以，我一头扎进去，成为了一名贡献者([甚至在我被雇佣之前](https://www.sparkpost.com/blog/sparkpost-node-js-client-library/))。

请允许我帮助您在 Node.js 项目中开始使用 SparkPost 发送电子邮件。

### 安装&设置

我假设你已经安装了。因为我们遵循 [Node.js 长期支持(LTS)时间表](https://github.com/nodejs/LTS#lts-schedule1)，所以您需要运行版本 4 或更高版本。您可以在终端窗口中使用`node --version`命令查看您正在运行的版本。

让我们创建一个新的 npm 项目。如果你已经有了，可以跳过这一部分。

```
> mkdir sparkpost-test
> cd sparkpost-test
> npm init --yes 
```

Enter fullscreen mode Exit fullscreen mode

这将创建一个新项目，并接受所有默认设置。你也可以运行`npm init`来回答所有的提示。

现在我们可以安装[节点-火花柱](https://github.com/SparkPost/node-sparkpost) :

```
> npm install sparkpost --save 
```

Enter fullscreen mode Exit fullscreen mode

安装后，您可以导入并创建 SparkPost 类的实例:

```
const SparkPost = require(‘sparkpost’)
const client = new SparkPost('YOUR API KEY') 
```

Enter fullscreen mode Exit fullscreen mode

避免将 API 键放在代码中是一个很好的做法。我们强烈建议将它存储在您的代码之外，因此我们设置了客户端库来检测`SPARKPOST_API_KEY`环境变量。

### 发送邮件

现在您已经有了一个 SparkPost 实例，可以开始发送了。有相当多的发送选项可供选择，但是让我们从一个简单的例子开始。以下是如何向一个收件人发送电子邮件，并指定内嵌内容:

```
client.transmissions.send({
  content: {
    from: ‘test@your-sending-domain.com’,
    subject: ‘Hello from node-sparkpost’,
    html: ‘<p>Hello world</p>’
  },
  recipients: [
    {address: 'someone@somedomain.com'}
  ]
})
.then(data => {
  console.log('Woohoo! You just sent your first mailing!')
  console.log(data)
})
.catch(err => {
  console.log('Whoops! Something went wrong')
  console.log(err)
}) 
```

Enter fullscreen mode Exit fullscreen mode

注意:这个例子使用了承诺，但是不用担心。我们还[支持回调函数](https://github.com/SparkPost/node-sparkpost/blob/master/docs/async.md)。

传输还有更多的选项，包括指定存储的模板或收件人列表、抄送和密件抄送、添加附件、指定活动、使用替代数据等等。查看[示例](https://github.com/SparkPost/node-sparkpost/tree/master/examples/transmissions)、传输资源的[文档以及](https://github.com/SparkPost/node-sparkpost/blob/master/docs/resources/transmissions.md)[传输 API 文档](https://developers.sparkpost.com/api/transmissions.html)了解更多信息。

### 奖励:使用节点邮件程序发送电子邮件

[Nodemailer](https://nodemailer.com/about/) 是一个流行的 Node.js 库，它让发送电子邮件变得“轻而易举”。对于那些选择使用这个库的人，我们为 Nodemailer 创建了一个 [SparkPost transport。您需要在您的项目中安装`nodemailer`和`nodemailer-sparkpost-transport`包。](https://github.com/SparkPost/nodemailer-sparkpost-transport) 

```
npm install nodemailer nodemailer-sparkpost-transport --save 
```

Enter fullscreen mode Exit fullscreen mode

现在您可以创建一个 nodemailer 传输实例:

```
const nodemailer = require('nodemailer')
const sparkPostTransport = require('nodemailer-sparkpost-transport')
const transporter = nodemailer.createTransport(sparkPostTransport({
  'sparkPostApiKey': process.env.SPARKPOST_API_KEY
})) 
```

Enter fullscreen mode Exit fullscreen mode

注意我是如何从环境变量中读取 API 键的。永远不要把它直接放在你的代码中，这仍然是一个最佳实践。

有几个选项可以传递到 SparkPost 传输中，比如活动 id 和消息是否是事务性的。看看所有选项的 README.md 。

下面是使用 Nodemailer 发送相同消息的方法:

```
transporter.sendMail({
  from: 'test@your-sending-domain.com',
  to: 'someone@somedomain.com',
  subject: 'Hello from nodemailer-sparkpost-transport',
  html: '<p>Hello world</p>'
}, (err, info) => {
  if (err) {
    console.error(err);
  } else {
    console.log(info);
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

### 双重奖励:用 notif.me 发送邮件

我们都知道电子邮件是沟通之王，但有时你希望能够通过多种渠道联系到人们。 [notif.me](https://notifme.github.io/notifme-sdk/) 是一个 Node.js 库，用于发送各种事务性消息。无论您想要发送电子邮件、短信、推送还是网络推送，您都可以轻松完成。它还为多个供应商建立了秋季和循环策略。我们最近与创建者合作建立了一个 SparkPost 提供者。您需要在您的项目中安装`notifme-sdk`包。

```
npm install notifme-sdk --save 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以使用 SparkPost 提供程序创建 notifme 实例:

```
const NotifmeSdk = require('notifme-sdk').default
const notifmeSdk = new NotifmeSdk({
  channels: {
    email: {
      providers: [{
        type: 'sparkpost',
        apiKey: process.env.SPARKPOST_API_KEY,
      }]
    }
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

同样，我们从环境变量中提取 API 键。我们已经说了三遍了——这很重要。🙂

现在让我们重复这个例子，这次使用 notif.me:

```
notifmeSdk.send({
  email: {
    from: 'test@your-sending-domain.com',
    to: 'someone@somedomain.com',
    subject: 'Hello from the SparkPost notif.me provider',
    html: '<p>Hello world</p>'
  }
}).then(console.log) 
```

Enter fullscreen mode Exit fullscreen mode

它真的很容易使用，我建议看看[的其他功能。](https://notifme.github.io/notifme-sdk/#features)

### 节点没有错

当使用 Node.js 发送电子邮件时，您有许多选择。我们努力让它变得尽可能无痛。如果您遇到任何问题或有任何疑问，请随时在我们的 [node-sparkpost](https://github.com/login?return_to=https%3A%2F%2Fgithub.com%2FSparkPost%2Fnode-sparkpost%2Fissues%2Fnew) 或[node mailer-sparkpost-transport](https://github.com/login?return_to=https%3A%2F%2Fgithub.com%2FSparkPost%2Fnodemailer-sparkpost-transport%2Fissues%2Fnew)github repos 上提交问题，或加入我们在#node 频道的社区 [Slack](http://slack.sparkpost.com) 团队。我期待收到你的来信。

*本帖最初发表于[sparkpost.com](https://www.sparkpost.com/blog/guide-using-sparkpost-node-js/)T3】*