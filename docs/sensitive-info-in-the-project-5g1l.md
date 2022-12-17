# 项目中的敏感信息！

> 原文：<https://dev.to/saurabhgoyal/sensitive-info-in-the-project-5g1l>

我最近越来越多地参与到部署和服务器管理等任务中，我仍然在努力完成整个过程的具体细节，从推动存储库中的更改到部署这些更改，并确保基础架构中的每个资源都成功地开始运行新的更改。

前几天在浏览我们的部署脚本和资源时，这个问题突然出现在我的脑海中。

> 我们如何管理项目中的敏感信息？

敏感信息包括以下内容及更多-

*   `API tokens`向第三方服务。(比如 Google APIs，Twilio 等。)

*   连接到你自己或他人的资源。(例如 AWS 秘密和访问密钥、诸如服务器、数据库实例等资源的地址。)

*   `Configurations`为您的应用提供各种服务。(例如电子邮件配置、加密密钥、关键设置或标志等。)

经过一番挖掘，我发现我们已经将它们存储在代码库中，并且包含在版本控制系统(git)中。这是人们在一个项目的初始阶段犯的一个基本错误，没有理解后果。`I'll talk about it more later in the post.`

在继续之前，我们必须了解上述情况是如何发生的以及为什么会发生。因此，您已经创建了一个出色的应用程序，它使用了一些第三方 API 或一些关键配置。这些数据是敏感的，不得公开，因此您已经设置了您的项目，以便无论何时有人启动您的应用程序，他或她都将在配置文件中或通过环境变量手动提供所有敏感数据，您的应用程序将使用这些数据。

你所有的敏感信息都脱离了代码库，安全地交给一个或多个授权人员，一切正常`UNTILL`你的产品开始增长，你面临以下问题。

*   您有`multiple environments`个应用程序-开发、试运行、生产，每个都有一组不同的敏感数据/配置。

*   随着`number of services and resources you use increase`，敏感数据/配置本身开始变大。

*   你有完整的`system and separate team for DevOps`，工作不再局限于一组人员。他们存储/管理敏感数据的方式是您的一个大问题，在最糟糕的情况下可能会给您带来大量损失。

也就是说，**离线方法是不可扩展的**。当发生上述情况并且存在多个版本的配置文件时，您需要找到一种方法来安全地存储它们并更好地管理它们，同时确保它们的可用性和完整性。你不能再让这些都离线了。

直观且可能是最好的选择是将它们放在一个私人的安全位置(只有 devops 工程师等授权人员才能访问)，每当实例化该应用程序时，所有数据都从那里提取。

*   这些位置可以是类似于`repository`或`AWS S3 bucket`的地方，或者是存储敏感数据的多种可用服务中的任何一种(`PassPack, Swordfish, CHEF server`等)。).在任何情况下，**我们必须始终将加密的数据存储在这些位置，并在我们端使用之前进行解密，以确保安全，即使来自该位置的数据受到危害。**

*   但是，在这种情况下，您必须提供密钥/凭证才能从该安全位置获取数据。但这与以前不同，因为我们已经成功地提取了所有敏感数据，这可能会增加很多，在一个安全的位置/服务后面，凭据的大小是并将永远是固定的。

以上是关于如何自动部署和配置一个或多个服务器，同时在一个地方安全地管理敏感数据的基本想法。当然，我们需要做一些工作来相应地修改我们的部署脚本。为了让你更好的理解，我将解释我们在我的一个项目中所做的-

*   我们把所有敏感数据都存储在一个 S3 桶里。数据在上传前用密码加密。我们使用名为`Ansible-Vault`的工具进行加密，这很好，因为我们将`Ansible`用于所有的部署脚本，而`Ansible-Vault`是`Ansible`的一个模块。使用`Ansible-Vault`的主要好处是，当运行 Ansible 脚本时，如果我们提供与 Ansible-Vault 一起使用的加密密码，那么`Ansible`在处理任何变量文件时，会在使用之前自动解密文件。

*   或者，我们可以使用其他东西进行加密，并修改我们的部署脚本来手动解密文件。

*   现在，无论何时部署，脚本所需要的只是从 S3 获取文件的 AWS 密钥和解密该文件的密码/密钥。

**注意:- *就敏感数据本身的修改和历史管理而言，存储库更好，这在 S3 中不完全可用。好处主要来自于当你有很多很多的敏感数据和多个 DevOps 工程师可能同时对同一个键进行修改的时候。尽管这在大多数项目中不太可能。*T3】**

* * *

现在来讨论一下我之前写的——**把任何敏感数据提交给版本控制系统都是一个很大的错误。**因为——

*   敏感数据即使在移出后仍保留在历史记录中。适当的移除需要-

    *   `History manipulation`——虽然可能不是很干净光滑。
    *   一旦一份敏感数据公开，使用它永远不会完全安全。最终你将不得不旋转它以获得一个新的值，这可能是基于它的用途和它被使用的位置的一些工作。

如上所述，有多种工具，如`truffleHog`可以在完整的历史记录中搜索任何敏感信息。如果不自信，必须使用它们。