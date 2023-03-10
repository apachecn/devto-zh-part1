# API 必须执行业务规则

> 原文：<https://dev.to/imwiss/business-rules-must-be-enforced-by-the-api>

设计 web 应用程序时要记住的一个重要概念是，业务规则**必须**由 API 在后端执行。在本文中，我将展示为什么这是至关重要的，以及如何避免这样做将使您的应用程序暴露于安全问题和错误。

### 什么是业务规则

在进入技术内容之前，让我澄清一下我所说的*业务规则*是什么意思，这样我们就都在同一页上了。

业务规则，有时被称为*业务逻辑*或*应用逻辑*，是控制你的应用**应该如何运行以及应用什么验证的规则。例如，Twitter 的业务规则是 tweets 不能包含超过 140 个字符(如果我们为了这个例子而简化它，并删除诸如 URL 缩写、回复某人等逻辑)。这意味着在发送推文之前，Twitter 需要确保遵守字符限制。**

[![Max character count](img/538d55ec4d47774b23f046fc79b2aa97.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iTerAsM5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.designingforscale.com/conteimg/2017/08/Screen-Shot-2017-08-11-at-10.08.29-PM.png) 
*我超过了ðÿ'†.的性格极限*

应该在哪里进行验证正是本文的主题。让我们开始吧！

### 客户端验证

客户端验证对于向用户提供*即时*反馈非常有用，这有助于更好的用户体验。例如，当写一条推文时，我们可以立即看到我们是否超过了 140 个字符的限制。

然而，客户端验证本身是不够的，因为它们很容易被绕过。浏览器(和移动应用程序)可以被“入侵”或玩弄，以绕过应用程序开发者设置的某些限制。事实上，浏览器允许你很容易地检查和修改 html 和 JavaScript，所以只依靠前端验证不是一个好主意。

例如，即使我们超过了 140 个字符的限制，也很容易在浏览器中重新启用`Tweet`按钮。通过这样做，我们绕过了客户端验证，将较长的 tweet 发送到后端，如下所示:

*   重新启用`Tweet`按钮:

    [![Re-enabling the Tweet button](img/2f22faca2f2e3df7757f9c094d087450.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mU5OHwMf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.designingforscale.com/conteimg/2017/08/Screen-Shot-2017-08-11-at-10.20.55-PM.png)

*   将长推文发送到后端，如 POST 请求所示:

    [![HTTP POST Request](img/13b02d745c99756e4019935a91cd18ba.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--34bVpjx3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.designingforscale.com/conteimg/2017/08/Screen-Shot-2017-08-11-at-10.21.48-PM.png)

    [![Data sent to the backend](img/9418fe8321d774d44d02dce90eeaea70.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--icPCe6UI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.designingforscale.com/conteimg/2017/08/Screen-Shot-2017-08-11-at-10.22.00-PM.png)

*   这在服务器上得到很好的处理，并向用户显示一条友好的错误消息:

    [![Error returned by the server and displayed to user](img/477739e882f26a8037a6141c14d1ecf6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_c1HUpWx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.designingforscale.com/conteimg/2017/08/Screen-Shot-2017-08-11-at-10.21.20-PM.png)

> 为了保护您的数据和应用程序的完整性，还必须在后端实施您的业务规则。

### 后端验证

后端验证允许您再看一眼数据——现在数据已经从客户机传到了服务器——并确保它遵守业务规则。在我们之前的例子中，这是您要确保发送的 tweet 确实是 140 个字符或更少的地方。如果没有，服务器应该向客户机返回一个适当的错误，这样就可以很好地处理这个错误，并且用户知道下一步该做什么。

后端也是通过验证保存到数据库的信息来确保数据完整性的地方，确保它不包含任何恶意信息，类型是正确的，等等。这也应该在比 API 更低的层次上完成，比如 ORM 或者甚至数据库，但是我包括了那些在大的*后端*气泡中的。分离关注点以及在哪里**应用这些验证将在另一篇文章中讨论。**

后端验证更适合业务逻辑的原因之一是，与客户端代码相比，您可以控制服务器和在服务器上执行的代码。这不是在浏览器或移动设备上运行，也不是由用户操作。这是你写的代码，你知道它将如何表现。

### 结论

我想在这里澄清的一件重要的事情是，前端和后端验证都很重要，但它们的主要目标是不同的。

前端验证对于向用户提供反馈非常有用。想象一下，有一个用户填写了一个很长的表单并提交了它，结果却因为一个空输入而被服务器拒绝，并提示用户重新填写表单。这显然会让用户感到沮丧，使用前端验证是可以避免的。

另一方面，后端验证是为了确保业务规则确实得到应用和尊重，以及数据完整性。

我希望这是有帮助的，澄清了双方的分歧。如果你有任何问题或意见，请告诉我。感谢您的阅读。

*本帖最初发表于[为规模](https://designingforscale.com/?utm_source=business-rules&utm_medium=guest-post&utm_campaign=devto)设计*