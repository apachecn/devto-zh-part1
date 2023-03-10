# MongoDB 的 BaaS 产品概述:MongoDB Stitch

> 原文：<https://dev.to/kenwalger/overview-of-mongodbs-baas-offering-mongodb-stitch>

许多开发人员面临的挑战之一是跟上后端流程。像数据安全、服务集成和数据操作代码这样的事情会占用大量的时间和资源。因此，许多人已经开始利用后端即服务(BaaS)。如果您有一个由 [MongoDB](http://www.mongodb.com) 支持的应用程序，有一个新的 Baas 选项可用，MongoDB Stitch。

MongoDB 允许你免费开始使用 Stitch，这总是很棒的。目前，它由 MongoDB 的数据库即服务(DBaaS)产品 [Atlas](https://www.mongodb.com/cloud/atlas/) 提供支持。在[的另一篇帖子](https://www.kenwalger.com/blog/nosql/mongodb/mongodb-atlas/)中，我讨论了 Atlas，并对这两种服务的联系感到兴奋。

### BaaS 概述

后端即服务开发方法允许移动和 web 开发人员将其应用程序连接到后端云存储，并利用固有的处理能力。此外，它还提供了用户从站点和应用程序中要求的许多通用功能。这些功能包括安全性、用户管理、推送通知和社交网络集成。

### MongoDB 缝合优势

Stitch 提供了许多功能——让开发人员利用 MongoDB 中固有的特性以及 BaaS 概念。它提供了对数据库的直接访问，无论是已经存在的还是新的数据，Stitch 允许开发者专注于构建他们的应用程序。后端逻辑被处理和提供——这导致了更快的开发周期。

Stitch 将 MongoDB 的数据[安全特性](https://docs.mongodb.com/manual/security/)更进一步。它提供基于每个字段的最终用户身份验证和访问控制等功能。例如，如果营销部门不需要访问客户的财务信息，则这些字段可以从他们的访问权限中排除。这使得开发人员和应用程序利益相关者可以确信，信息和服务只对适当的用户可用。

MongoDB 以开放的方式构建了 Stitch。它提供了一个 API，允许访问 MongoDB 数据库和其他公共云服务。这允许微服务集成，并防止被局限于单一供应商。

另一个巨大的好处是与 Atlas 的集成。这允许随着应用程序的增长轻松扩展容量和性能。这是另一组不需要我们自己管理的职责。我们可以让 MongoDB 的开发人员团队处理后端应用程序和基础设施的细节，然后可以专注于应用程序本身。

### MongoDB 缝合组件

Stitch 有三个主要组成部分，[管道](https://docs.mongodb.com/stitch/pipelines/)，服务和[规则](https://docs.mongodb.com/stitch/rules/)。服务有两种不同的类型， [MongoDB 服务](https://docs.mongodb.com/stitch/services/mongodb/)和[合作伙伴服务](https://docs.mongodb.com/stitch/services/)，用于集成像亚马逊 S3 或 Twilio 这样的服务——用于消息传递。让我们快速看一下这些不同的组件是做什么的，以及它们如何适应一个应用程序。

###### 服务

Stitch 中的服务允许应用程序集成和常见操作的执行。有各种第三方(合作伙伴)服务的集成，例如通过脸书或谷歌、Slack、Twilio 和 AWS 的一些组件进行用户身份验证，等等。HTTP 服务允许 MongoDB Stitch 应用程序连接到 REST API 服务。MongoDB 有一个连接到 Atlas 的专用服务，并且有一些内置的管道操作。

###### 管道

缝合管道允许缝合服务按顺序执行一系列动作。这是一个强大的功能。它允许通过简单的 JSON 语法定义一系列动作。

管道分阶段构建，每个阶段按顺序运行。一个阶段运行并将它生成的信息传递给下一个阶段。例如，你可以从你的数据中做一个 MongoDB 聚合阶段，并确定在过去的一周里哪个用户在你的新 Snap-o-gram 应用上有最多的赞。然后，该阶段可以将该信息传递给 Twilio 服务，Twilio 服务可以将这一令人惊叹的成就发送到他们的手机上。

有一些用于过滤管道输入的内置操作，以一种显式的方式定义阶段的输出是什么，针对输入文档的表达式评估，以及项目特性，等等。MongoDB 提供的另一个很好的特性是[命名管道](https://docs.mongodb.com/stitch/reference/namedpipelines/)的概念。这允许在 Stitch 应用程序中重用设计的管道，并通过名称引用。这对于遵循 DRY(不要重复)原则编写代码有很大的帮助。

###### 规则

MongoDB 缝合的最后一个主要组成部分是规则。正如人们可能猜测的那样，规则允许对服务采取的动作进行控制。规则是以 JSON 格式设计和编写的，管道也是如此。

例如，可以为读取、写入和验证操作定义规则。这些可以在文档中使用，也可以在字段级别使用。想要阻止服务读取文档中的特定财务数据吗？为此写一条规则。这是 MongoDB Stitch 的一个强大特性，为您的数据提供了额外的安全性。

### MongoDB 针法开发

现在，开发一个 Stitch 应用程序有三种不同的选择。使用 JavaScript 进行网络应用和移动开发，有适用于 Android 和 iOS 的选项。MongoDB 已经收集了一些很棒的教程和入门指南。

### 包裹

Stitch 这个名字来源于将应用程序的各个部分缝合在一起的想法，而不是来自《Stitch [![](img/e440ea69ceaafa40cb5034f4e23266d5.png)](//ir-na.amazon-adsystem.com/e/ir?t=kenwalgersite-20&l=am2&o=1&a=B00009XMNJ) 中的迪士尼角色。考虑到 MongoDB Stitch 带来的好处和特性，我认为这是一个非常合适的名字。就像[缝纫](https://www.amazon.com/gp/product/B015KM765Y/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B015KM765Y&linkCode=as2&tag=kenwalgersite-20&linkId=79470d1ee6a76031c019d66162f4c4ba) [![](img/bdefcd49e98d7e606ee6ba98759e1a5b.png)](//ir-na.amazon-adsystem.com/e/ir?t=kenwalgersite-20&l=am2&o=1&a=B015KM765Y) 将一件衣服的所有部分缝合在一起，MongoDB Stitch 为您的应用程序做了同样的事情。

我知道我计划在我的下一个项目中使用这项服务。

与任何“预打包”的服务一样，您需要放弃一些灵活性和对应用程序的控制。然而，为了能够保持对你的 MongoDB 数据库的直接访问，以及它包含的[集合](https://docs.mongodb.com/manual/reference/glossary/#collection)和[文档](https://www.kenwalger.com/blog/nosql/document-model/)，Stitch 是一个很好的选择。与任何测试产品一样，最终产品可能会有所变化。

这篇文章中有几个 MongoDB 特定的术语。我为[亚马逊 Echo](https://www.amazon.com/gp/product/B01DFKC2SO/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B01DFKC2SO&linkCode=as2&tag=kenwalgersite-20&linkId=f9e513223de2525a72b95cf9561db55b) 系列产品创建了一个 [MongoDB 字典](https://www.echoskillstore.com/MongoDB-Dictionary/45103)技能。检查一下你可以说“Alexa，问一下 MongoDB 关于认证的定义？”并得到有益的回应。

* * *

*在 Twitter [@kenwalger](https://www.twitter.com/kenwalger) 上关注我，获取我发布的最新消息。*