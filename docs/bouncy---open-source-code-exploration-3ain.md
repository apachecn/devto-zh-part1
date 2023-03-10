# Bouncy -开放源代码探索

> 原文：<https://dev.to/theopensourceu/bouncy---open-source-code-exploration-3ain>

[![Bouncy - Open Source Code Exploration](img/5866728e5ee8b907a8125a134ccb5014.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qT0xY3KD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://theopensourceu.org/conteimg/2017/08/code-2620118_1280.jpg)

[Bouncy](https://github.com/substack/bouncy) 是一个“将原始 http 流量从一个传入的 http 请求传输到另一个远程端点”的工具

这是一个有趣的工具，对我来说，是一个有用的发现。不要透露太多，但这个网站运行在其他技术中的节点上。

我认为这个工具有很多可能的应用<sup>[【1】](#fn1)</sup>但对我来说，它充当的是[虚拟主机](https://en.wikipedia.org/wiki/Virtual_hosting)路由器。我不想在这里进入太多的细节(你可以看到[这里](https://en.wikipedia.org/wiki/Virtual_hosting)了解更多信息)，但在我的场景中，我想设置我的服务器，以便我可以在同一服务器上托管另一个网站，如果需要或希望的话。

弹性让我可以这样做；使用 bouncy，我创建了一个监听端口 80(网站端口)的小型定制程序。当它接受一个传入的请求时，它将它路由到运行在另一个端口(在同一个服务器上)上的正确站点，比如说 8000。

它通过请求主机头(`req.headers.host`)确定站点。

**示例配置概要:**

*   弹力=端口 80
*   开源 U =端口 8000
*   *可能的*未来站点=端口 8001、8002 等

它是一个小库(也可以作为 CLI 工具获得)，我认为它是第一篇文章的绝佳候选。坦率地说，我很好奇它是如何工作的，它小到足以整理这些帖子的结构。

出于本文的目的，我已经将 git 库分支到了[https://github.com/TheOpenSourceU/bouncy](https://github.com/TheOpenSourceU/bouncy)

我将在本文中使用和引用这个版本。因为父/源项目可以继续开发，所以我创建了一个名为 **[tOSU/explore](https://github.com/TheOpenSourceU/bouncy/tree/tOSU/explore)** 的分支。这将与本文保持一致，而硕士可以偏离。我想让 master 和 sources 的 master 保持一致。

## 技术先决条件

我会尽我所能链接或解释相关的技术和概念。该网站的一个主要目标是吸引具有不同背景和经验水平的人。

也就是说，我确实对一些技术的基本熟悉程度做了一些假设。目前，我在这个网站上没有公开评论，但如果你需要提供批评性(尊重的)反馈或提问，请使用位于此处的的 [GitHub](https://github.com/TheOpenSourceU/TheOpenSourceU.github.io/issues) 问题[。](https://github.com/TheOpenSourceU/TheOpenSourceU.github.io/issues)

## 指标的依赖关系

了解和理解包/程序依赖关系的高级目的有助于理解父应用程序。

Bouncy 有两个主要的依赖项(对于那些不熟悉的人，这在 [`package.json`](https://github.com/TheOpenSourceU/bouncy/blob/tOSU/explore/package.json) 文件中。

```
// ...
"dependencies" : {
 "through": "~2.3.4",
 "optimist": "~0.3.5"
},
// ... 
```

Enter fullscreen mode Exit fullscreen mode

*   [到](https://github.com/dominictarr/through):创建一个可读可写的流。
*   [optimist](https://github.com/substack/node-optimist) : ( [弃用](https://github.com/substack/node-optimist#deprecation-notice))是一个命令行选项解析器，由 Bouncy 的同一个作者编写。

## index.js

入口点和主要实现。Bouncy 构建在 bog 标准 NodeJS[http](https://nodejs.org/api/http.html)/[https](https://nodejs.org/api/https.html)库之上，在某种程度上，只是简单地包装了它。鉴于它与`http` / `https`的关系，Bouncy 是事件驱动的。

创建正确的服务器类型(安全或不安全)；然后给三个事件分配处理程序。第一个是由匿名函数处理的`connection` *或* `secureConnection`事件，我们称之为“连接事件处理程序”。另外两个项目是`upgrade`和`request`，它们都被指定为`onrequest`。

### 连接事件处理程序

这接受参数`stream`。这表示为请求而进入的网络数据流。该处理程序添加了一个名为`_bouncyStream`的属性，它是 stealthBuffer()的结果。

下一个片段是处理程序的具体细节。

```
var src = stream._bouncyStream = stealthBuffer();
// ... old version work around ...
else stream.pipe(src); 
```

Enter fullscreen mode Exit fullscreen mode

此时，`stream`是[套接字](https://nodejs.org/api/net.html#net_class_net_socket)的实例，`src`是[流](https://nodejs.org/api/stream.html)的实例。(注意，尽管名字如此，*流*不是*流*——大写的“S”很重要。)

`src`(流)通过管道(发送)到套接字。这就完成了连接的建立。现在，bouncy 将等待传入的请求，并通过`onrequest`路由它们。

### `onrequest`

**这是蹦蹦跳跳**的主要*目的*。这最终通过`cb`参数将请求反弹到另一个端口，因为这是作为回调提供的用户实现的代码。

下面是 GitHub 页面上给出的[示例](https://github.com/TheOpenSourceU/bouncy/tree/tOSU/explore#routejs)。

```
var bouncy = require('bouncy');

var server = bouncy(function (req, res, bounce) {
    if (req.headers.host === 'beep.example.com') {
        bounce(8001);
    }
    else if (req.headers.host === 'boop.example.com') {
        bounce(8002);
    }
    else {
        res.statusCode = 404;
        res.end('no such host');
    }
});
server.listen(8000); 
```

Enter fullscreen mode Exit fullscreen mode

匿名函数被传递到`bouncy`中，并在一些主要创建`bounce`方法的设置之后被调用。最终用户然后用适当的端口调用`bounce`，并且所述方法`pipes`连接到新路径。

这将请求“反弹”到新端口。

### `stealthBuffer()`

`stealthBuffer()`返回依赖关系`through`的实例；回想一下，这提供了使流可读和可写的方法。

这最终会在连接事件处理程序中使用。底线是它增加了将它传递给`onrequest`处理程序中的`bounce`方法的请求。

* * *

1.  我想知道 Bouncy 是否可以用于在 NodeJS 的两个不同实例上对同一个站点进行负载平衡。我强烈怀疑它可以，并希望尽快测试... [↩︎](#fnref1)