# AWS app sync–graph QL 即服务

> 原文：<https://dev.to/alexcasalboni/aws-appsync--graphql-as-a-service-4j3o>

re:Invent 2017 的第二天非常拥挤，令人兴奋。到目前为止，我最喜欢的公告是**新的 AWS AppSync** ，因为它符合无服务器社区采用的最有前途(但多少有些争议)的设计原则之一: **GraphQL** 。如果你不熟悉 GraphQL，我们最近解释了[如何使用 AWS Lambda](https://cloudacademy.com/blog/how-to-write-graphql-apps-using-aws-lambda/) 编写 GraphQL 应用，并举办了一场关于无服务器和 GraphQL 之间爱情故事的[网络研讨会。下面简单介绍一下关于 AWS AppSync 你需要了解的内容。](https://cloudacademy.com/webinars/serverless-graphql-love-story-46/)

## 移动和网络应用挑战

我参加了关于 AWS AppSync 的第一次深度会议，该会议出色地总结了大多数移动和网络应用程序面临的主要技术挑战:

*   身份验证和用户管理
*   高效的网络使用
*   多设备支持
*   设备间的数据同步
*   离线数据访问
*   实时数据流
*   云数据冲突检测和解决
*   运行服务器端代码(不管理服务器)

这些挑战中的每一个都可以有自己的文章，但我假设每个人都经历过非常糟糕的移动 UX，并且从用户的角度来看至少有一次(任何对 AWS re:Invent 应用程序的暗示显然都是无意的)。此外，这些挑战中的大部分都是我们为 web 和移动设备设计 RESTful 界面的直接结果。

如果您是 web 或移动开发人员，GraphQL 可以帮助您解决许多挑战，尤其是与网络优化相关的挑战，这要归功于动态查询，以及实时数据流，这要归功于 GraphQL 订阅。

### graph QL 怎么比 REST 好？

GraphQL 可以减轻许多复杂问题带来的痛苦，这些问题仅仅依靠 REST 是无法解决的。这包括 **API 资源关系**，**API 响应中减少或定制的信息**，**动态查询支持**，**高级排序和分页**，**推送通知**等。

GraphQL 基本上是 API 的一种**查询语言，它使得在已经存在的数据和 API 之上添加一个抽象层变得非常容易。另外，它是**强类型**，可以作为客户端和服务器之间的**自文档化** **契约**。**

同样，如果您不熟悉 GraphQL 查询、变异和订阅的概念，[请阅读这里的](https://cloudacademy.com/blog/how-to-write-graphql-apps-using-aws-lambda/)(从现在开始，它变得更加专业！).

[![](img/9b951c06f2f990cb56a75a0c770f4220.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--N2W9oahb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2f9gqwlnfnjcb.cloudfront.net/blog/wp-content/uploads/2017/11/aws-appsync.jpg)

### AWS AppSync 功能&明白了

AWS AppSync 允许您专注于构建应用程序，而不是管理运行 GraphQL 所需的所有基础架构(无论有没有服务器)。

AppSync 将查询连接到 AWS 资源，它通过客户端库提供**内置的离线和实时流支持，客户端库还提供不同的**数据冲突解决策略**(甚至是定制的基于 Lambda 的实现！).该服务提供企业级**安全特性**，如 API 密匙、IAM 和 Cognito 用户池支持。**

与任何 GraphQL 应用程序一样，您的开发工作流如下所示:

1.  定义您的模式(查询、变异和订阅)
2.  定义解析器(数据源)
3.  使用客户端工具通过 GraphQL 端点获取数据

AWS AppSync 为三种数据源提供内置支持:

*   DynamoDB
*   弹性搜索
*   Lambda(用于自定义或生成的字段)

AppSync 客户端库可用于**移动和 web 客户端** (iOS、Android、JavaScript 和 React Native)。离线支持非常简单，当网络可用时，客户端会自动同步数据。因为 GraphQL 是一个**开放标准**，所以不强制您使用 AppSync 客户端。事实上，**任何开源的 GraphQL 客户端都可以工作**。当然，AppSync 端点也可以被服务器使用。

变异解析器可以通过**映射模板(Velocity)** 映射到数据源。与 API 网关模板一样，它们附带了一些实用工具，如 JSON 转换、惟一 ID 生成等。，以及现成的示例模板。基于特定的数据源，模板将允许**完全定制后端查询**(例如，DynamoDB PutItem、ElasticSearch 地理位置查询等)。).

由于您将花费大部分时间使用 Schema Editor，我很高兴地注意到它非常用户友好，并且它带有**搜索和自动完成功能**。

### AWS AppSync 和 AWS Lambda

您可以使用 AWS Lambda 来计算动态字段，最终以批处理的方式。例如，假设您的模式定义了一个“*payed*”字段，需要为每张发票从第三方支付系统获取该字段。您可能会定义一个 *ComputePaid* 函数和一个 *ComputePaidBatch* 函数。

将自动调用批处理函数来处理多个记录的值，而不会导致 N+1 问题。当然，您可以对单个和批处理使用相同的 Lambda 函数。

### AWS AppSync 定价

AppSync 将根据**总操作数和实时更新数**收费。更详细的实时更新是根据更新次数和连接的**分钟数来收费的。**

以下是数字:

*   每百万次查询和数据修改操作 4 美元
*   每百万次实时更新 2 美元
*   连接到 AWS AppSync 服务每百万分钟 0.08 美元

请注意，数据传输按 EC2 数据传输速率收费，实时更新按传输的每 5kb 有效负载数据定价(按比例)。虽然该服务在预览阶段是完全免费的，但一旦正式推出，它将附带一个免费层。

另请注意，与 Graphcool 等其他类似产品相比，AppSync 没有最低费用或强制性服务使用。

### 结论

我预计 AWS AppSync 将会受到无服务器社区的欢迎，它将使 GraphQL 更容易被许多用例采用。

对于需要显著优化数据交付或者透明地管理许多数据源的应用程序来说，这将是一个很好的选择。此外，它还允许开发人员设计由强类型系统和一组强大的开发人员工具支持的自文档化 API，这将简化 API 的发展。

请记住，AWS AppSync 仍处于预览阶段，但您可以通过在此注册来[开始使用它。](https://pages.awscloud.com/awsappsyncpreview.html)

我期待着下周自己使用它，我迫不及待地想与社区分享我的第一个成果。如果您已经在生产中使用 GraphQL，请在下面的评论中告诉我们您对 AWS AppSync 的看法！