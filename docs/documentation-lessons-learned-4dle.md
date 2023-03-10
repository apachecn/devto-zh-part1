# 文件、经验教训

> 原文：<https://dev.to/sergiodxa/documentation-lessons-learned-4dle>

在▲ZEIT [我们将推出](https://zeit.co/blog/api-2)我们新更新的 [API 文档](https://zeit.co/api)，从 ZDB 开始我就一直在做那个项目，负责编写文档和一些相关的代码。记录我们的整个公共 API 有助于我学习和理解更多关于我们的基础设施和 Now 如何在内部工作。

经过几个月的努力，我想分享我学到的一些想法。

> 最初发表于[sergiodxa.com/articles/documentation](https://sergiodxa.com/articles/documentation/)
> 于[sergiodxa.com/articles/documentacion](https://sergiodxa.com/articles/documentacion/)翻译成西班牙语

## 记录你的代码

这真的很重要，你需要尽可能完整地记录你的代码。我并不是说您需要为每一行代码添加注释，但是如果您正在做 HTTP 微服务，只需在您的存储库中添加一个自述文件，其中包含每个端点的简单描述、它接收到的请求以及它看起来是如何响应的。

```
## GET /teams/:id/members

Get the list of members of the team `:id`.

### Response
 * `uid` (`String`) The team member unique identifier
* `role` (`String`) The role inside the team, it could be OWNER or MEMBER
* `email` (`String`) The email address of the team member
* `username` (`String`) The username of the team member 
```

Enter fullscreen mode Exit fullscreen mode

像这样简单的事情就足够了。作为一名开发人员，我发现启动一个像库或 HTTP 服务这样的项目的一个非常好的方法是使用公共 API，创建自述文件，编写项目应该如何使用，然后编写代码，这就是所谓的 [RDD](http://tom.preston-werner.com/2010/08/23/readme-driven-development.html) 。

如果你关心其他人，特别是负责记录你的代码的人，请这样做。

## 错误很重要

没有人喜欢错误，但我们都有错误。没有人能指望一个 API 的用户会一直很好地使用它，并且永远不会遇到错误。他们通常比你想象的要多，你的工作就是记录并解释可能的错误，它们看起来如何以及可能的解决方案。

这可能会有很大的不同，因为如果你这样做，你就给了用户很容易搜索任何错误并得到解决方案的能力，这意味着用户更少的麻烦和公司更少的支持工作。

这也意味着你需要定义一个标准的方法来发送错误给用户，你不能只为一些错误发送状态码，而为另一些错误发送 JSON。It **必须**标准化，以便简单解释如何读取错误。

### [可寻址错误](https://rauchg.com/2016/addressable-errors)

在一条错误信息中，不管格式如何，解释的内容总是有限的，这就是为什么我们总是一遍又一遍地在谷歌上搜索确切的错误信息。

为您和您的用户提供帮助，为每个错误附上一个唯一的 URL，并尽可能多地解释错误的含义、出现错误的原因以及可能的解决方案。

额外的一点是，让它开源，这样人们就可以随着时间的推移来改进它，如果你曾经写过 PHP，你可能会看到他的文档充满了来自其他开发者的有用的评论，这些评论大部分时间都有助于你更快地理解一些特性。

## 尝试，一切

你可能想写文档而不测试它，在大多数情况下它可以工作，每个人都很高兴，在其他情况下它不会工作，你可能在你的文档上有一个打字错误，自述文件可能已经过时，或者你可能只是误解了代码。

这就是为什么您需要一种方法来尝试 API 的每一种可能的情况，在基于 HTTP 的 API 中，您可以向每个端点发送请求并检查响应。

## 让用户尝试一下

就像你需要尝试你的用户想要的所有文档一样。给他们一个简单的方法。在这里你有许多选择。

### REPL

第一个(通常更难实现)是添加一个 REPL，在▲ZEIT docs 上，我们决定放一个简单的编辑器来测试我们最重要的端点`/v2/now/deployments`，它允许您用一个 API 请求创建一个新的部署。

这个编辑器允许您编写一个`package.json`和`index.js`文件的代码，查看一个 cURL 示例请求和一个按钮，只需从文档中单击一下就可以部署它。

### 复制-过去就绪代码

另一种选择是编写现成的代码，用户只需复制粘贴即可。这样，用户只需进入终端、代码编辑器或 IDE，运行示例代码来检查结果和它是否工作。

在我们的例子[中，我们编写了一个内部组件](https://github.com/zeit/docs/blob/master/components/api/request.js)来发送 HTTP 请求数据并生成一个 cURL 代码，但是这个组件可以为 JS 或任何其他可能的代码生成一个 [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) 代码，并让用户定义他最喜欢的语言来查看示例。

## 尽量多写，尽量少写

API 文档应该尽可能完整，这意味着您需要编写大量示例代码和文本。但与此同时，没有一个人愿意仅仅为了理解一个 API 端点而去阅读一本书。

这让我们尽量少写，让文档超级简单。越简单和完整的文档，用户越有可能理解和使用 API。

## 设计至关重要

开发人员倾向于低估一个好设计的重要性，特别是对于他自己的开发，但是一个好的设计对于一个好的文档(和一般的任何项目)是必须的，并且帮助用户更快地理解它。

ZEIT 的设计师[邪恶的兔子](https://twitter.com/evilrabbit_)在文档 UI 的每个部分都做了出色的工作，甚至帮助决定如何组织它。

是的，文本组织是一个好设计的一部分，也是一个易于理解的 API 和一团乱麻的区别。

## 获得第二种意见

编写文档并发布是很容易的，但是你认为容易阅读和理解的东西实际上可能更难。获得第二种意见总是一个好主意(不仅仅是为了文档)。

可能是一个没有真正处理 API 文档的同事，也可能是一个只是阅读文档并给出有价值的意见和反馈的外部(但可信赖的)人。不要只是给每个人看，挑选你认为会帮助你改进文档的人。

## 给予暗示和提示

一个简单而无聊的文档只是告诉你每个端点是如何工作的。一份好的文档会告诉你如何更好地使用它。这可能非常简单，就像一张便条，也可能解释常见的用例。

通过这种方式，用户不仅可以学习如何使用你的 API，还可以知道最好的和推荐的使用方法。与为什么[错误是重要的](#errors-are-important)类似，这些提示和技巧可以减少 API 用户所需的支持工作，因为您已经在教他们如何使用它了。

[![Subscribe to learn when I publish new essays on https://sergiodxa.com/subscribe/](img/7e68c2ede6ebb6eea7da7724bdd9d783.png)T2】](https://sergiodxa.com/subscribe/)