# AWS 云无服务器后端:Twitter 完成

> 原文：<https://dev.to/rob117/serverless-backends-with-aws-cloud-twitter-complete>

这是 AWS 无服务器架构系列的一部分。在这里和我的博客 [J-bytes](http://robsherling.com/jbytes/index.php/2017/01/08/serverless-backends-with-aws-cloud-intro/) 上都可以找到原始的博文系列。

# 我们用 Lambda 发几条推吧

好了，现在终于到了推特的时候了。在本节中，我们将处理用户的授权，让他们关注我们，并向他们发送一条 tweet，声明他们已成功注册！

请记住，如果你让用户关注你而没有勾选那个复选框，你的账户可能会被禁止使用 twitter。这可能是一个严重的问题，因为如果没有这个账户，你收集的所有 twitter 账号都将变得毫无用处。

此外，我们将向 twitter 用户发送他们自己的 DM。这是因为发送直接应用 DM 受到帐户的强烈速率限制，但是让用户自己发送 DM 绝对可以在大规模上实现。我知道这有点奇怪，但我们就是这么做的。这就是为什么在你的 twitter 应用程序设置中请求 DM 访问权限是非常重要的。

无论如何，我们将在这里做熟悉的步骤——上传一个 lambda，建立一个网关连接来连接它，并完成我们的测试。我们还将讨论如何在生产中零停机时间更新 lambdas。

## 第一步:λ

你现在知道该怎么做了，所以我会把它说得很短(看看你还记得多少)！警告:[圣诞树](http://rob.conery.io/2012/04/05/cleaning-up-deep-callback-nesting-with-nodes-eventemitter/)和前方非干 Javascript。非常欢迎建议。

制作一个名为 callback_lambda 的文件夹。cd，并将 loader.js 复制到其中。将 [index.js](https://github.com/Rob117/serverless-twitter-email/blob/master/callback_lambda/index.js) 移入其中，并在这里下载 node-twitter-api 库。将 index.js 中的数据库区域更改为您所在的区域。闭嘴。在 AWS 控制台上创建一个名为 twitter_callback 的 lambda 函数。像以前一样上传你的文件。别忘了把超时设置为 15s，角色设置为 master_lambda！创建两个别名- staging 和 production，指向$LATEST。

现在还不用担心更新 json-env 文件。我们将进行 API 后阶段，以确保一切顺利。我们也不会对此进行测试，因为一旦我们实际连接了 API 并通过它进行测试，测试就容易多了。

## 第二步:Api 网关

所以，这个网关会有一点不同，因为它是 GET 而不是 POST。

这大概是唯一的区别。

转到我们的 API。在资源选项卡上，单击/api，然后单击操作，创建资源。将此命名为“twitter-callback”。在 twitter-callback 上创建一个 GET 方法。lambda 是 Twitter _ callback:$ { stage variables . alias }
在您的 AWS CLI 中设置它，再次进行试运行和生产。在集成请求上，主体映射模板是 application/json(您必须手动键入它。AWS 将它作为模板，但如果您将其留空，它实际上不会自动填充)。请求传递是“没有定义模板时”，主体传递是“方法请求传递”。

方法响应也是 301，带有 location 的头。

将对我们的 301 错误正则表达式的集成响应设置为“^Twitter.MovedPermanently.*”，然后在单击“保存”之后，将标题映射值“integration . response . body . error type”添加到位置标题映射中。回到 GET method execution 屏幕，在 Integration Response 下，将 HTTP 状态模式更改为我们刚刚设置的模式。

发布、导出。点燃最后一轮的邮差！
注意:如果你的 follow 帐户与你正在测试的帐户是同一个帐户，你的 CloudWatch 日志会告诉你不能关注你自己。这很好；这意味着 follow 正在工作。此外，如果您看不到您的帐户在关注某人，请确保要关注的帐户旁边没有挂锁符号。这意味着只有当它手动批准其关注者时，它才会被关注。

## 第三步:环境配置

转到 API gateway，然后 staging，然后 staging，然后 twitter-callback - GET。复制那个网址。转到您的 S3，下载您的 staging environment vars，并将这个巨大的 url 粘贴到您的“twitter_api_callback”键中。用 KMS 加密重新下载。为生产(API 网关、阶段、生产等)重复。).

**第四步:邮递员**

去获取 twitter-session 的暂存 url(或者检查你的 postman 历史)，并将其放入 postman。头仍然是 Content-Type:application/x-www-form-urlencoded，体仍然是 x-www-form-urlencoded，键值对为 follow : true。

用邮件发送。当你得到回应时，点击“漂亮”Ctrl-f 为单词“重定向”。您应该会看到一个值为“[https://twitter.com/oauth/lots_of_stuff](https://twitter.com/oauth/lots_of_stuff)”的标签。复制整个 URL 并将其粘贴到浏览器中。授权应用程序。你应该看到一些加载的东西，然后被重定向回你的网站！希望重定向地址有成功的参数。如果没有，你必须阅读你的 Cloudwatch 日志，看看你可能在哪里出错了。您应该能够查看您授权的 twitter 帐户，并看到您自己给自己发的推文。如果你选择跟随邮递员跟随你自己，你还应该看到:

```
Trace: {
  statusCode: 403,
  data: '{"errors":[{"code":158,"message":"You can\'t follow yourself."}]}'
} 
```

否则你应该简单地跟踪某人。

祝贺你努力工作！记住，如果你没有得到正确的信息，你不知道你做了什么，检查你的 twitter 应用程序设置。

最后，要更新 lambda 设置，进入 AWS 面板，点击需要更新的 lambda。根据需要进行任何更改，然后发布新版本。然后单击蓝色测试按钮左侧的按钮(版本、限定符、别名等。)并单击别名选项卡。选择您要更新的别名，单击“配置”选项卡，然后只需单击版本下拉菜单并确认。更新了！

接下来，我们将把整个东西放在 CloudFront 上，然后通过数据管道将数据下载到 S3 桶中，这样我们就可以做一些很酷的事情，如解密电子邮件地址和发送 DMs。