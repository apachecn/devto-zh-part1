# AWS 云的无服务器后端:发送推文和收集电子邮件

> 原文：<https://dev.to/rob117/serverless-backends-with-aws-cloud-sending-tweets-and-collecting-emails>

这是 AWS 无服务器架构系列的一部分。在这里和我的博客 [J-bytes](http://robsherling.com/jbytes/index.php/2017/01/08/serverless-backends-with-aws-cloud-intro/) 上都可以找到原始的博文系列。

# 最终免责声明

我知道我之前已经说过十几次了，但这是最后一次:本教程旨在向您传授很酷的 AWS 服务，并为您提供一些使用它们的实际经验。有许多不同的方法可以做到这一点，但并不是所有的方法都同样适用于所有的目的。不过，这非常类似于我在一家公司使用的实际生产代码(当然，他们明确允许我编写本指南)。它是有效的，而且是经过战斗考验的。

此外，继续在您计划执行此操作的计算机上安装 MySQL。当然，你不必使用 MySQL，但是我们将在 Ruby 中使用 mysql2 gem，所以我推荐它。如果合适，可以随意替换您喜欢的数据库配置。如果你不知道如何安装和最低限度地配置一个 Mysql/PostgreSQL 数据库(但你能以某种方式跟踪和理解所有的代码和单词，直到现在？)，这是学习的时候。去上些教程，然后回来。

很好。让我们做 DB。

创建可能让 DBA 抓狂的语句(VARCHAR utf-8 之类的):

```
CREATE DATABASE twitter;
use twitter;
CREATE TABLE users (uid int unique, sent boolean, token VARCHAR(255), token_iv VARCHAR(255), screen_name VARCHAR(255), screen_name_iv VARCHAR(255), secret VARCHAR(255), secret_iv VARCHAR(255), message TEXT); 
```

Enter fullscreen mode Exit fullscreen mode

此外，继续安装 mysql2 和 twitter gems。

## 邮件和 CSV

因此，在某个时候，你的客户很可能会要求你提供一份电子邮件列表，列出那些已经注册向他们发送“目标邮件”的人。这是你如何做的。

我假设是 Linux/Mac，但是假设你安装了 Ruby 和 Node.js，我看不出有什么理由这不能完全适用于 Windows。

首先，下载您的数据管道创建的电子邮件文件(这是一个 JSON，因为它当然是)，并将名称改为更容易键入的名称，如“encrypted_emails”。如果你看看里面的东西，应该看起来大部分是垃圾。这很好；AES 正在运行，我们正在通过所有的合规性检查。让我们把它恢复成正常的格式，我们可以把它发送给我们的客户，让他们可以使用。注意:最佳做法是通过安全连接将加密文件发送给客户端，然后让他们使用一组训练有素的代理提供的密码对文件进行解密，这些代理使用带有安全开关的公文包。我买得起普通的电子邮件，所以我们用它来发送数据。

在项目空间中创建一个名为“local”的文件夹。在里面，放置文件 [decrypt.js](https://github.com/Rob117/serverless-twitter-email/blob/master/local/decrypt.js) ，并将带有“添加密码”的部分更改为您保存在适当的 S3 配置桶中的 AES 密码。注意:在现实生活中，由于安全和意外的代码提交泄露了您的密码，您应该将它放在一个环境变量中。

这段代码只是获取一个字符串，并将其恢复到未加密状态。我们在 node.js 中制作它，因为我无法让 ruby 和 node 很好地使用它们各自的加密库，这种方法需要 10 秒钟，并且保证可以工作，最后期限，人，最后期限。

接下来，在同一个本地文件夹中，放置文件 [decrypt-emails.rb](https://github.com/Rob117/serverless-twitter-email/blob/master/local/decrypt-emails.rb) 。确保 encrypted_emails 文件也在本地文件夹中。阅读脚本中的解释。然后，在您的终端中:

`ruby decrypt-emails.rb <S3 email file name here>`

观察“processed_emails.csv”输出。

成功！如果您仍然在该文档中看到垃圾字符串，那是因为您的 AES 密码配置错误。确保您有正确的阶段和密码。

# 推文。所有的微博。

下载我们的 S3 桶中的 twitter 数据，并将其重命名为类似“twitter_data”的神秘和难以理解的名称。将它移到我们最近创建的“本地”文件夹中。

我们这里需要两份文件。一个将我们的文件上传到我们准备好的 Mysql 数据库中，另一个将 tweets 从该数据库中发出。

如果您还没有在本地主机 MySQL 上创建适当的 db/tables，请参见上面的创建语句。

将以下两个文件放入“本地”文件夹。代码假定 MySQL 根用户没有密码，根据需要更改。

[s3_to_mysql.rb](https://github.com/Rob117/serverless-twitter-email/blob/master/local/s3_to_mysql.rb) -根据需要更改数据

[send _ tweets . Rb](https://github.com/Rob117/serverless-twitter-email/blob/master/local/send_tweets.rb)——根据需要输入 twitter 客户端数据和 MySQL 数据，**运行**前记得在 decrypt.js 中放入正确的 AES 密码。同样，不是打死马，而是请在现实生活中使用环境变量。

随意修改代码，从文件中读取我们的 tweet，强制 140 个字符的限制，等等。

注意，s3_to_mysql.rb 可以在任何项目中使用，因为它与我们的项目无关。只需创建一个 MySQL 表，其中包含我们的 DynamoDB 表所包含的所有键，然后运行脚本。

总之，在控制台中，运行:

`ruby s3_to_mysql.rb <s3_twitter data file name>`

你的 mysql 应该用数据填充！然后运行:

`ruby send_tweets.rb <message to tweet here>`

您注册的 twitter 帐户现在应该有一条来自他们自己的带有您的消息的 dm。如果有，恭喜你！

## 后记

我希望我能有一个很酷的方法来结束这些小时的辅导和一路上的挫折，但我只能说:

这花了很长很长的时间才把这些代码整理成你所看到的样子。如果让我再做一次，我会做得更快。当我开始工作的时候，公司里甚至没有人知道这是否可行；甚至让我建造它的人也在猜测。我没有看到任何关于如何连接这些东西的教程，这让我很困扰。一路上有如此多的陷阱和如此多的“陷阱”,让我沮丧到需要离开的地步。*我直接看着你，CloudWatch 为 lambda* 记录角色。

当我开始时，我从未使用过 lambda，但我知道互联网是如何工作的，所以我可以试着把它拼凑起来。现在我是公司的 lambda/API 网关权威，感觉真的很酷。不要害怕尝试新技术，更不要害怕在需要的时候寻求帮助。

直到下一次，

-罗伯