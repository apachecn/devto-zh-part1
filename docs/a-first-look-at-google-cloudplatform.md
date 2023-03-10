# 谷歌云平台初探

> 原文：<https://dev.to/kyle/a-first-look-at-google-cloudplatform>

这真的很长，所以我把它分成几部分。随意跳到你感兴趣的部分。

*   设置、组织差异& IAM/认证(本贴)([个人镜像](https://kyle.io/2017/07/first-look-google-cloud-platform/))
*   [谷歌计算引擎](https://dev.to/kyle/a-first-look-at-google-compute-engine) ( [个人镜像](https://kyle.io/2017/07/first-look-google-compute-engine/))
*   [谷歌云存储](https://dev.to/kyle/a-first-look-at-google-cloudstorage) ( [个人镜像](https://kyle.io/2017/07/first-look-google-cloud-storage/))
*   [谷歌云数据存储](https://dev.to/kyle/a-first-look-at-google-cloud-datastore) ( [个人镜像](https://kyle.io/2017/07/first-look-google-cloud-datastore/))

* * *

我有一堆 AWS 的经验(免责声明:我在那里工作过)。我的 AWS 账户可以追溯到 2010 年，但我只是在过去的两年里才真正开始大量使用 AWS。

我最近一直在为一个新项目进行成本估算，随着 GCP 免费层和 300 美元信用的引入，我决定研究一下 GCP 提供的一些服务，看看它与 AWS 相比如何。

谷歌对 AWS 和 GCP 进行了比较，这很有用，但是很枯燥。我决定开始尝试——300 美元的信用额度意味着我非常安全！

## 注册 GCP

这是一个去 GCP 控制台用我的谷歌账户登录的问题。我必须注册免费试用，并提供我的信用卡信息，但仅此而已。与 AWS 注册流程相比，这要简单得多。

然而，这很简单，因为谷歌已经有效地拆分了账户验证步骤——我*使用了我的 gmail 账户*，而这个账户*已经*验证过了。这样做的一个副作用是创建的资源默认与这个账户*相关联*。一个 AWS 帐户转移起来很简单——更新电子邮件地址，然后就完事了。我的谷歌账户？不太容易转移，但这让我想到了第一个主要区别。

### AWS 账户 vs GCP 项目

谷歌并没有详细解释账户/项目的区别，只是在比较中提到了这一点:

> 云平台按项目而不是按账户对你的服务使用进行分组。在这个模型中，您可以在同一个帐户下创建多个完全独立的项目。在组织设置中，这种模型可能是有利的，它允许您为公司内的不同部门或组创建项目空间。

实际上，这是一种完全不同的处理资源的方式。如果你想在 AWS 中独立的筒仓中运行一些东西，你通常会创建一个完全独立的账户，并使用合并的计费/AWS 组织[(这是一组完全不同的问题)](https://kyle.io/2017/03/exploring-aws-organizations/)。在 GCP，每个项目都是自己的小筒仓，默认情况下项目之间没有通信。

在习惯了 AWS(并使用 AWS 组织来处理每个项目的账户)之后，这是一种*非常*不同的思维方式。对我来说，主要有两个好处。

首先，转换资源的所有权非常简单——指派一个新的项目所有者，去掉现有的所有者，就完成了。最令人印象深刻的是(*据我所知*)，传输将在不中断任何当前运行的情况下完成。计算引擎实例将继续运行，云存储桶不需要复制内容，删除桶，然后在新帐户中重新创建，希望在此期间没有其他人窃取桶名。

第二个好处是项目的分割更加容易。如果你想为了安全而分离，你不需要每个项目都有一个 AWS 帐户。

#### 一个书呆子要去吗？

按项目分离的缺点是，GCP 似乎没有 AWS IAM 限制访问单个资源的功能。GCP 文件明确指出了这一点:

> 权限是项目范围的，这意味着项目中的开发人员可以修改和部署应用程序以及与该项目相关的其他云平台服务和资源。

我对这种情况感到矛盾。最佳实践表明，帐户/角色应该拥有尽可能少的权限。我尽可能将我的 IAM 策略锁定在特定的资源上。例如，一个用户只能与一个 SQS 队列[交互，因为我通过队列名](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-overview-of-managing-access.html#sqs-managing-access-to-resources)限制了附加的 IAM 策略。

在 GCP，在一个项目中要么全有，要么全无。如果我想允许访问一个主题，我必须允许访问所有的 PubSub 主题。

实际上，人们可以在 AWS 的 IAM 策略中自由使用*。但是，这些限制并不是默认内置的，这一事实令人担忧，特别是对于那些必须管理 IAM 策略(而不是按服务操作帐户)的大公司来说。

我认为谷歌已经意识到了这一点，并且正在[扩展 IAM(仍在 Alpha 中)](https://cloud.google.com/iam/docs/creating-custom-roles)以允许在支持的单个资源上定义权限(例如 [PubSub](https://cloud.google.com/pubsub/docs/access_control#tbl_perm) ， [Datastore](https://cloud.google.com/pubsub/docs/access_control#tbl_perm) )。看起来有可能使用 IAM API 来定义自定义角色，但是我还没有成功地这样做。我最终使用了项目隔离，它很有效，但是感觉很糟糕。

#### 认证

与 AWS 相比，GCP 有更多种类的方法来验证他们的 API。

*   计算引擎/应用程序引擎与 IAM 一起工作并获取凭证，非常类似于 EC2 实例角色。这些仅限于个别项目。
*   使用`gcloud` CLI 的开发人员可以使用 OAuth2 进行身份验证，并在项目之间切换。
*   GCP 以外的非交互式系统使用与特定项目相关的服务帐户。

使用 SDK 需要创建一个服务帐户，这会生成一个 JSON 文件(或 PKCS12，但我们忽略它)。最简单的方法就是在使用 SDK 的时候使用一个环境变量[GOOGLE _ APPLICATION _ CREDENTIALS](https://developers.google.com/identity/protocols/application-default-credentials)来设置文件的位置，让 SDK 来处理一切。

你可以用代码[定义文件位置](https://cloud.google.com/docs/authentication/production#obtaining_and_providing_service_account_credentials_manually)，就像 Boto 一样。(而且想必其他 AWS SDKs，我只真正用过 Python 版本。)

您也可以进行完整的 OAuth，[提供一个范围列表](https://developers.google.com/identity/protocols/OAuth2ServiceAccount#jwtsample_py)并完成 OAuth 过程，但是...没有。

与 AWS 简单的“IAM 用户获得一个访问密钥&秘密密钥”相比，这似乎过于复杂了。幸好使用了合理的默认值。

有趣的是，GCP 并不像 AWS 那样不可知论——对 G 套件的引用有时会出现。不是每个人都在设计在 G Suite 上运行的应用程序，所以我感觉这只是旧文档。