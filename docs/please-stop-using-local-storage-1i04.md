# 请停止使用本地存储

> 原文：<https://dev.to/rdegges/please-stop-using-local-storage-1i04>

[![Grumpy Rage Face](img/34b26126b43c701c82f44061139d6a41.png "Grumpy Rage Face")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PSTXo3N_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ogcedxwio12syvr6wut2.jpg)

说真的。别说了。

我不知道到底是什么驱使如此多的开发人员将会话信息存储在本地存储器中，但是不管是什么原因:这种做法需要消亡。事情变得完全失控了。

几乎每天我都会偶然发现一个在本地存储中存储敏感用户信息的新网站，当我知道如此多的开发者这样做将自己暴露在灾难性的安全问题面前时，我感到很困扰。

让我们推心置腹地谈谈本地存储，以及为什么应该停止使用它来存储会话数据。

## 什么是本地存储？

[![HTML5 Local Storage](img/84722575885a5c15d59c2aacfddd07ec.png "HTML5 Local Storage")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YaPh5ULB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/77aoiv7pjx5hypqya951.jpg)

如果我之前脾气不好，我很抱歉。你不值得这样！见鬼，您可能甚至不熟悉什么是本地存储，更不用说使用它来存储您的会话信息了！

让我们从基础开始:本地存储是 HTML5 的一个新特性，它基本上允许您(一个 web 开发人员)使用 JavaScript 在用户的浏览器中存储任何您想要的信息。简单吧？

实际上，本地存储只是一个很大的旧 JavaScript 对象，您可以向它附加数据(或从中删除数据)。这里有一个 JavaScript 代码的例子，它将我的一些个人信息存储在本地存储器中，并返回给我，然后(可选地)删除它:

```
// You can store data in local storage using either syntax
localStorage.userName = "rdegges";
localStorage.setItem("favoriteColor", "black");

// Once data is in localStorage, it'll stay there forever until it is
// explicitly removed
alert(localStorage.userName + " really likes the color " + localStorage.favoriteColor + ".");

// Removing data from local storage is also pretty easy. Uncomment the lines
// below to destroy the entries
//localStorage.removeItem("userName");
//localStorage.removeItem("favoriteColor"); 
```

Enter fullscreen mode Exit fullscreen mode

如果您在测试 HTML 页面的浏览器中运行上面的 JavaScript 代码，您会看到短语“rdegges 真的喜欢黑色。”在一条警告信息中。如果您随后打开开发人员工具，您将能够看到`userName`和`favoriteColor`变量都存储在浏览器的本地存储中:

[![Local Storage Inspector](img/a54d12311b1548178593325f29c79c89.png "Local Storage Inspector")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ocp6GCQn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.rdegges.com/statimg/2018/local-storage-inspector.png)

现在，您可能想知道是否有某种方法可以使用本地存储，这样您存储的数据会在某个时候自动删除，而您不需要手动删除放入其中的每个变量。幸运的是，HTML5 工作组(喊出来吧！)有你撑腰。他们在 HTML5 中添加了一个叫做 sessionStorage 的东西，它的工作方式与本地存储完全相同，只是当用户关闭浏览器标签时，它存储的所有数据都会自动删除。

## 本地存储有什么酷的地方？

[![Happy Rage Face](img/878e844fc252e3fe95c217efc18725f5.png "Happy Rage Face")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XMCw7dKl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vwyhfcn4ttyznvknhxk9.png)

既然我们对什么是本地存储有了相同的看法，那么让我们来讨论一下是什么让它变得很酷吧！尽管本文的主旨是劝阻您不要使用本地存储来存储会话数据，但是本地存储仍然有一些有趣的属性。

首先:它是纯 JavaScript！cookies(本地存储的唯一真正替代品)的一个恼人之处是它们需要由 web 服务器创建。嘘！Web 服务器既无聊又复杂，很难使用。

如果你正在构建一个静态网站(比如单页应用)，使用本地存储意味着你的网页可以独立于任何网络服务器运行。他们不需要任何后端语言或逻辑来在浏览器中存储数据:他们可以随心所欲地这样做。

这是一个非常强大的概念，也是本地存储如此受开发人员欢迎的主要原因之一。

本地存储的另一个优点是它没有 cookies 那么多的大小限制。本地存储为所有主要的 web 浏览器提供了至少 5MB 的数据存储，这远远超过了您可以在 cookie 中存储的 4KB(最大大小)。

如果您想在浏览器中缓存一些应用程序数据以备后用，这使得本地存储特别有用。由于 4KB(cookie 最大大小)并不多，本地存储是您唯一真正的替代选择之一。

## 本地存储有什么不好

[![Frowning Rage Face](img/74a39b9930eb7fa5ac60f664c0dd5d69.png "Frowning Rage Face")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7sGuEqMf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vp8ht3hahkpvfsd42r6w.png)

好的。我们谈了好，现在让我们花一分钟(或两分钟！)说的是不好的。

本地存储很简单。咻。一吐为快，我感觉好多了。本地存储只是一个非常基本、简单的 API。

我觉得大多数开发人员没有意识到本地存储实际上有多基本:

*   它只能存储字符串数据。嘘。这使得它对于存储比简单字符串稍微复杂一点的数据来说毫无用处。当然，您可以将包括数据类型在内的所有东西序列化到本地存储中，但这是一种丑陋的做法。

*   它是同步的。这意味着您运行的每个本地存储操作将是一次一个。对于复杂的应用程序，这是一个大禁忌，因为它会降低应用程序的运行速度。

*   它不能被 [web 工作者](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Using_web_workers) =/这意味着如果你想构建一个利用后台处理来提高性能的应用程序，chrome 扩展，诸如此类的东西:你根本不能使用本地存储，因为它对 web 工作者是不可用的。

*   它仍然限制了你可以存储的数据大小(在所有主流浏览器中大约 5MB)。对于开发数据密集型或需要离线运行的应用程序的人来说，这是一个相当低的限制。

*   页面上的任何 JavaScript 代码都可以访问本地存储:它没有任何数据保护。由于安全原因，这是一个大问题(也是我近年来最讨厌的问题)。

简而言之，这里是您应该使用本地存储的唯一情况:当您需要存储一些公开可用的信息时，这些信息一点也不敏感，不需要在高性能应用程序中使用，不大于 5MB，并且完全由字符串数据组成。

如果你正在使用的应用不符合上述描述:*不要使用本地存储*。使用其他东西(稍后会详细介绍)。

## 为什么本地存储不安全，你不应该用它来存储敏感数据

[![Stick Figure Instructor](img/7e5d33261dcd1064ac41a8f33037a091.png "Stick Figure Instructor")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hCUkqN8R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wpj8oyxs3qhog709aa24.png)

事情是这样的:本地存储的大部分坏处并不那么重要。你仍然可以使用它，但你只是有一个稍微慢一点的应用程序和轻微的开发者烦恼。但是安全就不一样了。了解和理解本地存储的安全模式非常重要，因为它会以你可能没有意识到的方式极大地影响你的网站。

本地存储的问题是它不安全！一点也不！每个使用本地存储来存储敏感信息的人，比如会话数据、用户详细信息、信用卡信息(甚至是临时的！)和任何你不想公开发布到脸书的东西都是错误的。

本地存储*并没有被设计成*在浏览器中用作安全存储机制。它被设计成一个简单的只有字符串的键/值存储，开发者可以用它来构建稍微复杂一点的单页应用。就是这样。

世界上最危险的事情是什么？没错！JavaScript。

请这样想:当您在本地存储中存储敏感信息时，您实际上是在使用世界上最危险的东西将您最敏感的信息存储在有史以来最糟糕的保险库中:这不是最好的主意。

真正的问题归结为跨站点脚本攻击( [XSS](https://www.owasp.org/index.php/Cross-site_Scripting_(XSS)) )。我不会用 XSS 的完整解释来烦你，但这里是高水平的:

如果攻击者可以在您的网站上运行 JavaScript，他们就可以检索您存储在本地存储中的所有数据，并将其发送到他们自己的域中。这意味着您在本地存储中获得的任何敏感信息(如用户的会话数据)都可能受到威胁。

现在，你可能会想“那又怎样？我的网站很安全。任何攻击者都无法在我的网站上运行 JavaScript。”

这是一个合理的观点。如果你的网站是真正安全的，没有攻击者能够在你的网站上运行 JavaScript 代码，那么从技术上来说你是安全的，但实际上这很难实现。让我解释一下。

如果您的网站包含来自您域名之外的任何第三方 JavaScript 代码:

*   引导链接
*   jQuery 的链接
*   链接到 Vue，React，Angular 等。
*   链接到任何广告网络代码
*   链接到谷歌分析
*   任何跟踪代码的链接

那么您目前就面临着攻击者在您的网站上运行 JavaScript 的风险。假设您的网站嵌入了以下脚本标签:

```
<script src="https://awesomejslibrary.com/minified.js"></script> 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，如果 awesomejslibrary.com 受到威胁，他们的`minified.js`脚本被修改为:

*   遍历本地存储中的所有数据
*   将它发送到为收集被盗信息而构建的 API

...那你就彻底完蛋了。在这种情况下，攻击者可以轻而易举地破坏您存储在本地存储器中的任何东西，而您永远不会注意到。不理想。

作为工程师，我认为我们经常容易认为我们永远不会在我们的网站中嵌入第三方 JavaScript。但是在现实世界中，这种情况很少发生。

在大多数公司，营销团队使用不同的 WYSIWYG 编辑器和工具直接管理公共网站。你真的能确定你的网站上没有使用第三方 JavaScript 吗？我会说“不”。

所以，为了谨慎起见，大大降低安全事故的风险:**不要在本地存储中存储任何敏感的东西**。

## PSA:不要在本地存储中存储 JSON Web 令牌

[![Stick Figure with Stop Sign](img/fbadb24944176fac81984ed71bcdefd4.png "Stick Figure with Stop Sign")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YL04pAyR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7ejjtcb7tkcqa972q9gd.png)

虽然我觉得在前面的部分中我已经说得很清楚了，您不应该*永远不要*在本地存储中存储敏感信息，但是我觉得有必要特别指出 JSON Web token(jwt)。

我今天看到的最大的安全违规者是那些在本地存储中存储 jwt(会话数据)的人。许多人没有意识到 jwt 和用户名/密码本质上是一样的。

如果攻击者能够[获得你的 JWT](https://stackoverflow.com/questions/34259248/what-if-jwt-is-stolen) 的副本，他们可以代表你向网站发出请求，而你永远不会知道。像对待信用卡号或密码一样对待 jwt:永远不要把它们存储在本地存储中。

有成千上万的教程、YouTube 视频，甚至大学的编程课程和编码训练营都错误地教导新开发人员将 jwt 存储在本地存储中作为一种认证机制。这个信息是错误的。如果看到有人叫你这么做，赶紧跑！

## 用什么代替本地存储

[![Pointing Man](img/3cc95713320fd4c737f7cb27d5842cb8.png "Pointing Man")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nD9yO_1x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t32tt3y6t9c321b23arh.png)

那么本地存储有这么多缺点，你应该用什么来代替呢？让我们探索替代方案！

### 敏感数据

如果需要存储敏感数据，应该始终使用服务器端会话。敏感数据包括:

*   用户 id
*   会话 id
*   JWTs
*   个人信息
*   信用卡信息
*   API 键
*   还有什么是你不想在脸书公开分享的

如果您需要存储敏感数据，请参考以下方法:

*   当用户登录到您的网站时，为他们创建一个会话标识符，并将其存储在加密签名的 cookie 中。如果你使用的是 web 框架，查找“如何使用 cookies 创建用户会话”并遵循该指南。

*   确保您的 web 框架使用的任何 cookie 库都设置了`httpOnly` cookie 标志。这个标志使得浏览器无法读取任何 cookie，这是*为了安全地使用带有 cookie 的服务器端会话所必需的*。阅读杰夫·阿特伍德的文章了解更多信息。他就是那个*男人*。

*   确保你的 cookie 库也设置了`SameSite=strict` cookie 标志(防止 [CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) 攻击)，以及`secure=true`标志(确保 cookie 只能通过加密连接设置)。

*   每次用户向您的网站发出请求时，使用他们的会话 ID(从他们发送给您的 cookie 中提取)从数据库或缓存中检索他们的帐户详细信息(取决于您的网站有多大)

*   一旦你获取并验证了用户的账户信息，就可以随意获取任何相关的敏感数据

这个模式简单、直接，最重要的是:*安全*。是的，你可以使用这种模式来扩展一个大型网站。不要告诉我 jwt 是“无状态”和“快速”的，你必须使用本地存储来存储它们:你错了！

### 非字符串数据

如果您需要在浏览器中存储不敏感且不纯粹是字符串数据的数据，那么最好的选择是 IndexedDB。它是一个 API，允许您在浏览器中使用类似数据库的对象存储。

IndexedDB 的伟大之处在于，您可以用它来存储类型化信息:整数、浮点数等。您还可以定义主键、处理索引和创建事务以防止数据完整性问题。

学习(和使用)IndexedDB 的一个很好的教程是这个 [Google 教程](https://developers.google.com/web/ilt/pwa/working-with-indexeddb)。

### 离线数据

如果您需要您的应用程序离线运行，您的最佳选择是结合使用 IndexedDB(如上)和 Cache API(它是 Service Workers 的一部分)。

缓存 API 允许您缓存应用程序需要加载的网络资源。

学习(和使用)缓存 API 的一个很好的教程是这个 [Google 教程](https://developers.google.com/web/fundamentals/instant-and-offline/web-storage/cache-api)。

## 请停止使用本地存储

[![Happy Rage Face](img/878e844fc252e3fe95c217efc18725f5.png "Happy Rage Face")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XMCw7dKl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vwyhfcn4ttyznvknhxk9.png)

既然我们已经有机会讨论本地存储，我希望您理解为什么您(可能)不应该使用它。

除非您需要存储公开可用的信息:

*   一点也不敏感
*   不需要在超高性能应用中使用
*   不大于 5MB
*   由纯字符串数据组成

...不要使用本地存储！使用合适的工具完成工作。

请不要在本地存储中存储会话信息(比如 JSON Web 令牌)。这是一个非常糟糕的想法，它会让你面临各种各样的攻击，这些攻击绝对会让你的用户陷入瘫痪。

有问题吗？[给我发邮件](//mailto:r@rdegges.com)。

在外面注意安全=)

**注意**:对于那些想知道为什么我没有明确提出[内容安全政策](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP)作为减轻 XSS 影响的一种方式的人，我特意选择不包括这一点，因为它对我上面描述的情况没有帮助。即使您使用 CSP 将所有第三方 JavaScript 域列入白名单，如果第三方提供商受到威胁，也无法阻止 XSS。

虽然我们正在做:[子资源完整性](https://developer.mozilla.org/en-US/docs/Web/Security/Subresource_Integrity)(虽然酷)也不是这个问题的全球解决方案。对于大多数营销工具、广告网络等。(这是迄今为止最常用的第三方 JavaScript 类型)，子资源完整性几乎从未被使用过，因为这些脚本的提供者*希望*经常更改它们，以便他们可以悄悄地为他们的用户更新功能。

**更新**:我不是唯一一个认为永远不要在本地存储中存储任何敏感内容的人。[奥瓦斯普](https://www.owasp.org/index.php/HTML5_Security_Cheat_Sheet#Local_Storage)也一样:

> ...换句话说，对存储数据的机器具有本地特权的用户可以绕过应用程序要求的任何身份验证。因此，建议不要在本地存储中存储任何敏感信息。