# 大摇大摆的搜索:在你的微服务原始汤里找到一个端点

> 原文：<https://dev.to/danlebrero/swagger-search-finding-an-endpoint-in-your-microservice-primordial-soup--256>

IG 早在 2009 年就开始了面向服务的架构之旅。

第一个概念验证由三个服务组成，独立于现有的 monolith 部署:一个巨大的 J2EE 企业服务器。

我们如此热爱这种自由，以至于这些年来，IG 的服务领域一直在有机增长，现在已经拥有超过 250 种服务，拥有数十上百个终端。

边界清晰的完美服务诞生了，但由于时间压力、懒惰或其他不幸的现实，这些完美的婴儿成长为臃肿的青少年，积累了与他们无关的功能。

但随后，凭借某种无性繁殖，肥胖服务分裂成一套新的完美服务，注定要重复其父母的错误。

## 我们有吗...？

微服务架构不断发展的本质是一个优势，但它也带来了许多不同的挑战，其中之一可以总结为以下问题:

> 我们有提供 XXX 功能的端点吗？

为了记录什么是可用的，我们最终使用了一个服务注册来了解哪些服务是可用的，并使用 Swagger 来了解每个服务提供了什么。

因此，查找候选端点的工作流程应该是:

1.  转到服务注册表。
2.  Grep 寻找一个似乎合乎逻辑的名字。如果失败，扫描所有服务名的列表。
3.  找到服务的 Swagger 文档 url。
4.  扫描可用的端点以寻找合适的东西。

如果找不到服务或端点，我们将退回到“询问办公室”的方法。

这个工作流程起初有点烦人，但是随着我们对现有的服务进行提炼，越来越多的服务会有可疑的相似名称，使得流程变得更加繁重。

## 大摇大摆的搜索

为了减轻这种痛苦，IG 的 Clojure 俱乐部卷起袖子，建立了 Swagger Search:一种从所有服务收集所有 Swagger 文档的服务，并提供一个用户界面，允许您在您的微服务架构中找到端点。

看起来是这样的:

[![List services](img/293bfdf9478b6b29f79e7ad9b6b92e86.png "List all services found by the service discovery")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xYsUBe-V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://danlebrero.cimg/blog/swagger-search/list-of-services.jpg)

[![Search for endpoints](img/a6510c3b126be730482ecfebdcf5e863.png "Search results")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JxQJWg44--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://danlebrero.cimg/blog/swagger-search/search-endpoints.jpg)

该服务允许搜索端点文档、URL、HTTP 方法、其参数、类型或请求或响应正文中的任何字段。

我们所说的类型和字段是什么意思？如果你有以下端点:

```
"post":  {  "description":  "Adds a new moderator for the chat.",  "parameters":  [  {  "in":  "body",  "name":  "body",  "schema":  {  "$ref":  "#/definitions/LiveChatModerator"  }  }  ],  }  ...  "LiveChatModerator":  {  "properties":  {  "id":  {  "description":  "...",  "type":  "string"  },  "snippet":  {  "$ref":  "#/definitions/LiveChatModeratorSnippet",  "description":  "...."  }  },  "type":  "object"  },  "LiveChatModeratorSnippet":  {  "properties":  {  "liveChatId":  {  "description":  "...",  "type":  "string"  },  "moderatorDetails":  {  "$ref":  "#/definitions/ChannelProfileDetails",  "description":  "..."  }  },  "type":  "object"  }  ... 
```

Enter fullscreen mode Exit fullscreen mode

端点将显示在对 LiveChatModerator、LiveChatModeratorSnippet 和 ChannelProfileDetails 类型的查询中，以及对 id、Snippet、liveChatId 或 moderatorDetails 的查询中。

Swagger Search 内置了对[consult](https://www.consul.io/)和 [Etcd](https://github.com/coreos/etcd) 的支持，但是你可以插上自己的服务发现机制。

它有三种不同的风格:作为一个 [docker 容器](https://hub.docker.com/r/danlebrero/swagger-search/)，作为一个[可执行 jar](https://github.com/IG-Group/swagger-search/releases) ，或者作为一个[库](https://clojars.org/ig/swagger-search)。

IG 在 Apache 2 许可下开源了这个项目。你可以在 Github 找到[源码和说明。](https://github.com/IG-Group/swagger-search)

希望你也觉得有用。

## 鸣谢

非常感谢 IG 的 Clojure 俱乐部的所有成员，特别感谢所有那些被恶魔领主强迫为这个项目做出贡献的毕业生。你做得很好，你教我 Clojure 可以在不到一个月的时间内学会。

也感谢 [APIs.guru](https://apis.guru/) 提供了所有那些在测试中帮助巨大的大动作 API。

当然，还要感谢所有在 [Swagger](https://swagger.io) 背后的人。这是一个很棒的工具。

向所有人致敬！