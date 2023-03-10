# AWS 云的无服务器后端:Twitter 和电子邮件活动

> 原文：<https://dev.to/rob117/serverless-backends-with-aws-cloud-intro>

# 我们开始吧

*嗨！你可以在我的博客 [J 字节](http://robsherling.com/jbytes/index.php/category/tutorials/aws/?order=asc)上阅读这个系列教程的原始格式。这不是一个短系列；它超过 11，000 个单词，是一个关于如何很好地理解几种技术的详细教程。许多文章都很长，并且要求您实际打开一个 AWS 控制台屏幕。当你有一点时间的时候，这可能是最好的解决方法。此外，如果你有任何问题/你写的很酷的东西/可以聊天的东西，你可以通过`rob@<blog-domain-name>.com`联系我。*

我们将为一个完全使用 AWS 云服务(主要是 Lambda 和 DynamoDB)的网站制作一个 Twitter 和电子邮件活动的“无服务器”后端，以节省传统基础设施实现的大量资金。同样，这是一个大规模的教程，所以留出几个小时来完成它。我们将做一切事情，从加密和密钥管理到在 AWS Cloudfront 后面创建一个静态站点。

### 超酷图:

[![cloudcraft diagram of lamda layout](img/3a3f986189159a6826d41a40cb2df39c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZG5wFKRh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://robsherling.com/jbytes/wp-content/uploads/2017/01/LamdaTwitter.png)

如果你不关心怎么做和为什么做，只是想完成这个项目，那就直接看下一篇文章吧。

如果你发现自己需要一种方法来注册大量的 twitter 用户/电子邮件，用于一个单一的目的，如注册活动，那么这是给你的教程。如果你打算不止一次发送电子邮件/推文，你也可以做一些简单的修改，比如 RDS over DynamoDB。

我们将在这个项目中制作两个阶段(登台和生产)来演示项目每个级别的阶段处理。

本教程假设该网站是一个 S3 帐户托管的静态页面，但如果不是这样，您可以简单地调整。我们将制作一个占位符页面来演示概念证明。

我们将允许用户注册他们的电子邮件，然后向他们发送确认消息。或者，我们将允许他们通过 twitter 注册，并决定他们是否要关注我们。当我问 twitter 他们的自动关注政策时，他们向我保证，我们可以让用户以这种方式关注我们，但前提是他们明确同意(即使用复选框)。

我们将加密和存储他们的电子邮件和 twitter 信息，以便以后我们可以向他们发送电子邮件/向他们发送直接消息/做一些很酷的事情。

我假设你有一个 AWS 帐户，有足够的权限，并不担心整个项目的小费用。我假设你有某种域名，或者很容易得到。如果你想发送电子邮件，你可能需要它，否则不要担心它。其中大部分是免费的，但我不能保证你不会被收取任何费用。我还假设你可以使用谷歌，并有耐心。

### 其他假定知识:

*   你知道如何在你的特定机器上安装包(本教程假设 Mac 或 Linux，Windows 可能工作，但我不知道)
*   非常基础的 Javascript 很好但不是必须的(node.js)。您也可以使用 Python 或 Java 编写 Lambda 代码，但是如果您这样做，您只能靠自己。
*   有些红宝石也不错但不是必须的(你知道什么是宝石)。
*   Git，因为总是使用版本控制。一直都是。
*   您对命令行(mkdir、cd、touch 等)有些熟悉。)
*   基本的 Mysql(可选的，但是在我们发送 tweets 的最后需要很酷的部分)
*   你知道 JSON 是什么(即使你不能从头开始写，你也明白这个概念)
*   你至少听说过以下这些(如果没有，读一点你不知道的):AWS Lambda，API Gateway，Data Pipeline，S3，CloudWatch，DynamoDB，CloudFront，CLI，KMS，WAF，IAM，邮件服务 Mailgun，工具邮递员

如果满足以下条件，则适合您实施此方案:

*   您希望消除网站的大多数硬件瓶颈，让数据库读写能力成为主要的决定因素。请注意，如果您的请求速率迅速增加到每秒 300 个 PUT/LIST/DELETE 或 800 个 GET，S3 就会遇到瓶颈 [(source)](http://docs.aws.amazon.com/AmazonS3/latest/dev/request-rate-perf-considerations.html) 。如果它超过 600 个组合“lambda/秒”(twitter 注册、回拨和电子邮件注册)，你将需要请求增加你的 lambda 限制[(来源)](http://docs.aws.amazon.com/lambda/latest/dg/concurrent-executions.html)
*   你不想处理等待自动扩展、服务器崩溃等问题。
*   你想通过使用 ec2 服务器节省一大笔钱(前 100 万 lambda 请求是免费的)
*   你想看起来很酷，用一堆云技术来制造完全安全可靠的后端。它失败的唯一方式是如果您的 AWS 区域不可用。
*   你是一个受虐狂，可以在同一个项目中处理两种语言(如果算上 SQL 的话是三种)、无数的技术和 AWS 控制台+ DynamoDB 偶尔挑剔的 I/O 次数。

### 备注:

如果你觉得这很困惑，不要担心。这花了我很多时间来完成，而且我需要不止一次地和其他人商量。花了几个星期的改进才把它弄到一个像样的地方。此外，由于本指南的布局，我们的一些决定直到稍后才会有意义。例如，为什么我们要构建一个总是抛出错误的 lambda？(答案:只能用 API 网关重定向)[(来源)](https://aws.amazon.com/blogs/compute/redirection-in-a-serverless-api-with-aws-lambda-and-amazon-api-gateway/)

为不同阶段制作多个 lambda 版本的部分绝对很糟糕。我知道。除非 AWS 改进他们的服务，否则什么也做不了，除非你跳过 lambda 版本控制和 staging。

### 免责声明:

1)为此我花了 4 个小时学会了 Javascript。如果你有易读但显然更好的方式来编写 lambda 代码，请告诉我，我会发布更新。当我这样做的时候，你将会在编辑的文件的末尾收到积分(通过名字，电子邮件，用户名，无论你想要什么)。先到先得。

2)我很乐意帮助您解决代码问题，但不幸的是，我没有时间一对一地解决问题。如果你给我留下一个关于你所遇到的错误的详细信息，并提供一些证据，证明尽管直接遵循了指南，错误还是发生了，我肯定会重新访问帖子，进行调查，并将这些变化归功于你，但是 Google +论坛是你的朋友。

3)这个项目都是捆绑在一起的。挑选“如何制作一个 API”之类的东西可能会让你失望，因为我们做出的设计决策对我们的项目来说很棒，但在其他项目中却非常糟糕(例如，大多数 API 方案只返回 301 而不是 200)。

### 我为什么建造它:

我建立这个项目是因为我的技术主管和我进行了一次简短的谈话，内容是:
技术主管:“你在 Rails 中建立了 API？”
我:“是啊，因为一些古怪和我对一些关键词的幼稚理解，花了几个小时，但它已经完成了。”
导语:“太好了。在 AWS Lambda 中从头再做一遍。我从来没有这样做过，但应该是可能的。告诉我进展如何。”我喜欢和新技术打交道，所以我同意了。我环顾四周，找不到一个单独的操作指南，所以我认为这可能是一项有用的技术，可以学习并与世界分享。

我们开始吧！

对了，麻省理工的执照。只需查看[回购](https://github.com/Rob117/serverless-twitter-email)。