# 我最喜欢的跨站点脚本攻击演示

> 原文：<https://dev.to/ben/my-all-time-favorite-demonstration-of-a-cross-site-scripting-attack>

这是几年前的一个光荣故事。如果你以前没读过，我就要告诉你了。如果你已经知道这个了，建议你分享一下。我认为这既好笑又是一个跨站点脚本漏洞的非常棒的野外演示。

首先，跨站脚本的[定义](https://www.acunetix.com/websitesecurity/cross-site-scripting/)

> 跨站点脚本(XSS)是指客户端代码注入攻击，其中攻击者可以向合法网站或 web 应用程序中执行恶意脚本(通常也称为恶意负载)。

如果这还不清楚，我们为什么不直接跳到 Tweetdeck 的例子。它应该澄清事情。

> — *andy (@derGeruhn) [June 11, 2014](https://twitter.com/derGeruhn/status/476764918763749376)

Tweetdeck 是一个第三方 Twitter 平台，自被 Twitter 以 4000 万美元收购以来，它允许 Twitter 超级用户使用更加定制的 Twitter feed。在这条推文发布的时候，它还允许浏览器以代码的形式执行推文的内容。ðŸ˜

让我们深入研究这条推文的代码。

```
<script class="xss"> 
```

Enter fullscreen mode Exit fullscreen mode

这是带有类“xss”的脚本标记的开始。这是一个普通的旧 HTML 节点，现在将在浏览器中执行。

```
 $('.xss') 
```

Enter fullscreen mode Exit fullscreen mode

这是一个 [jQuery](https://jquery.com/) 选择器，它利用 jQuery 已经包含在 Tweetdeck 页面中的事实，通过“xss”类来选择标签。这种攻击可能在没有 jQuery 的情况下进行，但是它使攻击变得更加容易。

```
.parents().eq(1) 
```

Enter fullscreen mode Exit fullscreen mode

此命令选择脚本标记的父元素。想必这就是推文的`<div>`。

```
.find('a').eq(1).click() 
```

Enter fullscreen mode Exit fullscreen mode

此命令选择选定父对象的第二个子对象，即定位标记，并单击它。

```
$('[data-action=retweet]').click() 
```

Enter fullscreen mode Exit fullscreen mode

该命令选择一个数据属性为`data-action=retweet`的元素并点击它。

```
alert('XSS in Tweetdeck') 
```

Enter fullscreen mode Exit fullscreen mode

所有的损害都已经造成了，但是为了更好的措施，一个警告对话已经被触发。

```
</script>â™¥ 
```

Enter fullscreen mode Exit fullscreen mode

因为 140 个字符对于执行这次攻击来说是远远不够的，所以这个脚本已经被用一个心形封了起来。â ï

当这条推文的`<div>`在 Tweetdeck 浏览器中呈现时，代码将执行，推文本身将被转发。这将导致它出现在更多的 Tweetdeck 客户端中，并且蠕虫将简单地继续传播。这就是病毒式传播的含义，展示了跨站脚本攻击的威力。自从网络计算出现以来，有很多关于计算机病毒以类似方式传播的故事。

让我们再来看看这条推文:

> — *andy (@derGeruhn) [June 11, 2014](https://twitter.com/derGeruhn/status/476764918763749376)

我不知道你怎么想，但我没有很多推文达到了这种转发数。

快乐编码。`</script>â¤ï¸`