# 无头 CMS 买家指南:你应该考虑的 6 件事

> 原文：<https://dev.to/rogerjin12/headless-cms-buyers-guide-the-6-things-you-should-consider>

*想了解更多类似的内容，请在推特上关注 [ButterCMS，并订阅我们的](https://twitter.com/ButterCMS)[博客](https://buttercms.com/blog/)。*

## **什么是无头 CMS？**

如果你知道什么是无头 CMS，跳过这一节。

构建一个基于 CMS 的网站的典型方式是选择一个完全集成的解决方案，像 [WordPress](https://wordpress.org) 一样，把你的网站构建成一个紧密嵌入的模板集合。或者，如果您想要更多的控制，您可以使用您喜欢的技术堆栈来构建自己的集成 CMS。

像 [ButterCMS](https://buttercms.com) 这样的无头 CMS 解决方案是一种相对较新的内容管理方法，与“旧”方法相比有许多优势。Headless CMS 允许你构建与内容管理工具分离的网站和应用程序，并通过 API 集成。这为您提供了使用首选工具(如 Rails、Node.js、Angular)构建前端的灵活性，同时能够轻松集成定制的健壮 CMS。无头方法可以在初始实现和后续维护中为您的团队节省大量时间和金钱。

## **导航选择**

所以你决定一个无头的 CMS 是适合你的，所以你打开谷歌，开始探索选项。你会发现许多不同的 CMS 出售基于 API 的方法，并提供各种各样的特性和附加功能。你会遇到收费超过 1000 澳元/月的 SaaS 选项、被放弃的开源 Github 项目，以及诸如“内容对象”等令人困惑的术语。您还可以找到关于 WordPress 的 API 和使用 Drupal 的独立前端的文章

你如何开始理解这一切？我创建了这个指南来帮助你。

## **细分类别**

headless-CMS 是一种令人困惑的野兽。一些新产品自称为“API 优先”，而许多其他产品已经发展到提供基于 API 的方法。

例如，WordPress 最近推出了一个 REST API，并维护了一些客户端库，如 [node-wpapi](http://wp-api.org/node-wpapi/) 。Drupal 8 包含一个 RESTful [网络服务 API](https://www.drupal.org/docs/8/api/restful-web-services-api/restful-web-services-api-overview) ..使用这些 API，您可以将任何前端与 WordPress 或 Drupal 实例集成。让我们称这些“传统供应商”为**。**

另一方面，有一些“API 优先”的 SaaS 产品，如[ButterCMS](../)——它们完全是围绕 API 集成构建的。

那么，考虑到传统与无头，更好的方法是什么？

看情况。两种选择都可行。如果你熟悉传统的开源解决方案，比如 WordPress 或 Drupal，那么利用它们的 API 来获得前端自由可能会很有吸引力。但是 ButterCMS 和 CloudCMS 是为无头方法设计的，提供了更好的开发人员体验。

例如，WordPress 只为两种语言提供官方 [API 客户端](https://developer.wordpress.org/rest-api/using-the-rest-api/client-libraries/)，并且没有官方指南。ButterCMS 提供了七种语言的 [API 客户端和十几个不同框架的指南。](../docs/)

当通过 API 集成时，像 WordPress 这样的传统提供商在灵活性和定制方面也会受到严重限制。这听起来令人惊讶，因为 WordPress 占网络的 27%,并以其丰富、成熟的插件生态系统而闻名，允许你构建从大型出版网站到电子商务商店的任何东西。

问题是 WordPress 生态系统的大部分并没有解决 API 的使用。例如，在 WordPress 中启用自定义字段需要一个[插件](https://codex.wordpress.org/Custom_Fields)和一个自我管理的 WordPress 实例。但是一旦插件安装完毕，自定义字段创建完毕，就没有文档说明如何通过 API 获取自定义字段。强调这个问题的是另一个叫做 [WP REST API 自定义字段](https://wordpress.org/plugins/wp-rest-api-custom-fields/)的插件的存在，旨在解决这个问题。然而，在撰写本文时，这个插件已经两年多没有更新了。

在这种情况下，像 WordPress 这样的传统提供商基于插件的可扩展性变得毫无用处，并且可能无法解决。另一方面，无头解决方案是从头开始构建的，以支持灵活性和定制。例如，ButterCMS 不仅允许您创建定制的内容模型和字段，它甚至允许您将管理 UI 定制为用户友好的*工作区*。

CMS 解决方案缺少可用的插件，它们通过集成到您完全拥有和理解的代码库和技术栈中来弥补。这意味着你可以根据需要加入其他有用的开源插件或[后端即服务](http://nordicapis.com/why-you-should-build-apps-with-an-api-backend-baas/)提供商。例如，您可以将 headlessÂ CMS 与基于 API 的电子商务后端(如 [Moltin](https://www.moltin.com/) )结合起来，构建一个完整的电子商务网站，而无需数据库(甚至服务器！).

## **托管 vs 自托管**

另一个要考虑的问题是是否要控制住自己。

如前所述，对于像 WordPress 这样的传统提供商，如果你想在默认设置之外定制你的 CMS，自我管理的主机可能是你唯一的选择。当考虑自托管管理时，请记住你不只是处理托管费用，而且，完全负责确保您的网站保持在线和安全。这包括数据库维护和备份、安装安全升级以及安装服务器监控系统。

很可能，您的开发团队不喜欢花时间备份数据库、设置主机、安装升级以及负责监控内容管理系统的正常运行时间。使用托管解决方案意味着您几乎 100%免于任何类型的维护。

对于 headless，自托管是一种选择(例如，CloudCMS 提供这种功能)，但托管服务更为标准。这种方法的好处是您永远不必担心 CMS 的维护或正常运行时间。你可以建立自己的网站，然后去做更重要的事情。

## **了解定价**

在考察 SaaS 提供商时，有四个关键领域:

1.  您可以管理的内容量
2.  允许的 API 调用次数
3.  用户帐户数量
4.  正常运行时间

不同的产品在其定价计划中使用不同的术语，但通常可以归结为这四个标准。例如，ButterCMS 提供无限的 API 调用，但对内容的数量有上限，而 CloudCMS 提供 API 调用的上限，但对内容的数量没有限制。简单吧？还要考虑你可能关心的附加组件，比如发布工作流、本地化和 webhooks。

需要考虑的一个重要因素是支持。当您的团队遇到时间敏感的问题时，来自知识渊博的支持工程师的快速帮助是非常有益的。无头 CMS 提供商正在世界各地涌现，所以除了响应能力之外，还要考虑可能影响沟通的时区和语言障碍。

另一件需要密切关注的事情是正常运行时间——使用无头方法，您的网站高度依赖于您的 CMS API。在大多数情况下，如果你的 CMS 的 API 关闭，你的网站也会关闭，因为它将无法获取内容。因此，确保供应商提供接近 99.99%正常运行时间水平的保证，并主动防止数据丢失。例如，ButterCMS Ruby 客户端提供了设置一个[本地后备数据存储库](https://github.com/buttercms/buttercms-ruby#fallback-data-store)如 Redis 的能力，这样即使您的网站无法成功连接到 ButterCMS API，它也能正常工作。

对于较大的项目，您可能对企业级服务水平协议和白手套支持感兴趣，例如直接与供应商的解决方案工程师合作。

## 评估检查表

到目前为止，我们已经介绍了很多信息。即使掌握了这些知识，考虑到选项的数量，做出决定也是困难的。为此，在指导您的评估和决策时，请遵循以下逐步清单:

*   可用性:文档、SDK 和 API 有多简单和直观？
*   **灵活性**:CMS 的灵活性是否足以满足您的项目需求？
*   **正常运行时间**:API/SDK 是否有弹性，是否满足您的正常运行时间要求？
*   **内容编辑器**:你的内容编辑器的界面有多直观？
*   **支持**:供应商位于哪个国家和时区，他们的支持和响应水平如何？
*   **定价**:对于你的项目来说，定价是可伸缩的和可预测的吗(如果你的网站流量增加，基于 API 使用的定价会不可预测地飙升吗)？

## **总结起来**

请记住，无头 CMS 的主要好处是节省您的开发团队的时间和金钱。在几乎不需要维护的情况下，使用您最喜欢的技术是再好不过的了。

如果你是一家代理公司，你可以更快地启动你的项目，然后进入下一个项目。如果你是一家技术初创公司，你可以在一天内完成 CMS 需求，然后专注于核心产品。

为了最大化这一点，在所有其他因素之上，我们建议根据开发人员的经验来选择您的解决方案。哪个 CMS 最容易整合？哪个 CMS 的文档最好？哪个 CMS 的 SDK 最简单？不要在订阅费用上浪费额外的钱——你的开发团队几天的时间可能比你担心的订阅费用要贵得多。

*此文原帖[此处](https://buttercms.com/blog/headless-cms-buyers-guide)。*