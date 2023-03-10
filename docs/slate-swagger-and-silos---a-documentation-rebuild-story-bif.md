# 石板、大摇大摆和筒仓——一个文档重建的故事

> 原文：<https://dev.to/preciselyalyss/slate-swagger-and-silos---a-documentation-rebuild-story-bif>

2016 年，我将一个电商平台的 API 和前端文档从 Contentful 迁移到 [Slate](https://github.com/lord/slate) ，作为我的第一个 solo(也是主要的)ruby 项目。在它被迁移的时候，大概有 20 个 API 端点被记录在案，但是没有一个有 swagger 规范。不仅有 API，而且有两个前端框架需要包含在同一个站点中。我这篇文章的目的是讨论这个项目提出的具体问题，如何解决这些问题，以及一些限制。如果您正在研究将静态站点或 Slate 作为文档解决方案，我希望您能从我的经历中找到价值。
T3![screenshot of slate api documentation](img/d47c856dc5392c39f75441577eebfa51.png)T5】

## 问题

和任何产品一样，我有一个改进的愿望清单。但是很容易眼大肚子小。

[![young puppy eating from an overflowing dog food bowl](img/76b99b641562b1e981405523f1e9e558.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Rfsz1Zzs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qmlg71714sj8yjl8vsos.jpg)

*   信息架构很糟糕。用户倾向于给页面添加书签，而不是忍受这种结构。
*   API 资源页面被分割，彼此之间没有链接。
*   没有搜索，使得上述问题更加严重。
*   代码样本只存在于托管在 github 上的 SDK 和客户端中。
*   基本入门信息被隐藏
*   加载时间是 meh。
*   更新内容需要用户席位。
*   没有 SAML/SSO，这对于通过 LDAP 管理离线和在线的公司来说很重要。

这个列表看起来并没有那么糟糕，但是由于知识的诅咒，我知道每个列表都比看起来的要多。有幸体验了 Twilio 的 API 文档，很难知道它的高标准。

*   代码示例会有语法高亮显示吗？
*   搜索网站是广泛的还是只有页内内容？
*   将如何处理访问？
*   我如何管理 IA(信息架构)？可以轻松更新吗？
*   新管理员的学习曲线是怎样的？

## 解功勋。石板

开箱即用，Slate 只有一个页面。索引页面通常被其他 Slate 分支用作登录页面(对于多个端点)，或者作为对 API 的第一步介绍，标题作为介绍。鉴于 API 和前端文档的性质，我需要将它用作登录页面。
[![screenshot of navigation bar and hero image](img/964a28c903b76def7048e5b44a7b6b0d.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--YBNtkPiK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dqs8zkfgipdp6z34d1i8.png)

### 信息架构

因为 Slate 是基于中间人的，所以目录结构感觉非常简单。我可以创建子目录，比如`/api`，在其中创建一个新的`index.html`，然后 boom: localhost:4567/api/。我在使用它时遇到的主要问题有三个:

*   从用户或开发人员的角度来看，信息架构很容易变得复杂。对于两个主要的 API 版本，我需要`/api/v2/`和`/api/v3/`。除此之外，文档中还包括 25 个或更多的资源(不包括子资源)。我必须决定两者孰轻孰重:一个很长的页面(但是有搜索和侧边导航)或者许多子目录和登陆页面。
*   [后来学到的经验]在 Slate 中，包含大量内容的页面可能会有一些有趣的问题。具体来说，使用导航，一些内容不会显示，直到你刷新，但页面已经完全加载。我部署 Slate 的方式是通过 github 页面，所以这个网站是用静态 html 预先构建的。没有 JS 或后端废话应该阻止渲染。我一直没弄明白这个。
*   如果我希望在同一个子目录中有多个页面，将文件命名为类似于`api-endpoint.html`而不是`index.html`的名称相对容易。我遇到的这个问题相对较小，但是各种 gem 之间的最终 html 呈现在一些地方(表格)不喜欢这样。如果没有像 Algolia 这样的联合搜索，它也会影响搜索效率。

### 速度

没有抱怨 Slate 速度的余地。它是独立的，只有在你最终制作它的时候才会变得臃肿。在我写这篇文章的时候，Slate 的 dev 分支有一种预渲染侧边导航的方法。在迁移工作中，它主要使用 javascript。在使用 Chrome 开发者工具和各种站点速度工具测试页面速度时，我注意到有一个页面是异常的。在与 DevTools 进一步争论之后，我能够获得页面负载的 javascript 概要文件，这有助于我识别花费时间最长的 javascript 函数。

在我试图让每个端点都在同一个页面上的时候(`/api/v2/`)，生成侧导航的 js 花了很长时间。桌面总计 1s+左右。我[向](https://www.slideshare.net/AlyssNoland/prerendering-with-nokogiri-75792512)展示了一点关于使用 Nokogiri 来预渲染目录表的内容，因为我曾与 Lord (Slate 的创建者)异步处理过它。实现 [Nokogiri](https://github.com/sparklemotion/nokogiri) 预渲染后，加载时间为👌。通过将 ToC(目录)构建功能从客户端改为服务器端，最大的损害得到了缓解。

### 搜索

作为一个静态网站，Slate 没有全站点搜索，但它有带 [lunr.js](https://lunrjs.com/) 的页面内搜索。我唯一的改变是突出显示功能。我注意到对于大页面，在输入一个字符后会有一种明显的滞后感。将这个数字增加到三个字符更有可能与用户相关，而不会影响他们的体验。

自从离开我实现这一点的公司后，他们也将把 Algolia 整合为跨多个站点的联合搜索。这是一个很好的解决方案，如果使用 [DocSearch](https://community.algolia.com/docsearch/) ，它会和 Slate 一起很好地工作。

## 昂首阔步，迎接石板

[![two businessmen shake hands](img/e08704f443cdb585db51e935101201b7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qDXlKt3X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9neaz9qeh871y859589d.jpg) 
在开发过程中，我所在的公司正在构建一个新的主要 API 版本。这个 API 版本突出了每个端点的 Swagger 规范(对此我欣喜若狂)。这是一个有趣的项目。

过去，每个 API 更新都是手工记录的。我是通过阅读 PHP 做到这一点的，而其他人是通过与工程师交谈或挖掘吉拉门票和拉请求做到这一点的。Swagger 提供了一致的格式，现有的编辑器提供了文档和示例，并有一个生态系统来支持它。由于一个热爱代码的产品经理的主要贡献，我们最终使用 [Widdershins](https://github.com/Mermade/widdershins) 来消费 swagger 文件并输出与 Slate 兼容的 markdown。Widdershins 还使用 [doT.js](http://olado.github.io/doT/index.html) 作为模板帮助创建代码样本。

如果您正在使用 Slate 或对其进行评估，并且有任何问题，请随时联系我。🕺

感谢[埃拉纳](https://twitter.com/elanatee)和[尼克](https://twitter.com/TheNickWalsh)帮助完成这篇文章的最终编辑。