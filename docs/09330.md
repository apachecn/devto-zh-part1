# 从定制博客到媒体:我们的转变(第二部分)

> 原文：<https://dev.to/qlikbranch/from-custom-blogs-to-medium-our-transition-part-2-3dej>

[![](img/5b81cce6ccc96f879ffdbb2dcdec0e24.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hzWtY2xv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AIDHG41ZqotgLUpwMj6gqpA.jpeg)

没多久我就意识到不仅仅是 API 的问题，RSS 提要本身也有局限性。在我们的出版物上发表了一些故事后，我们意识到这些故事已经被从意义搜索中删除了。你想猜猜为什么吗？RSS 源只提供最新的 10 个故事！！！负载测试给你上了一课。

我现在陷入了两个问题:

### **问题一:如何正确保持 app 最新**

我决定再看看可能的解决方案。我的大脑不停地重复“肯定有比这更好的方法。”

在做了更多的挖掘和搜索之后，我了解到了 [PubSubHubbub](https://en.wikipedia.org/wiki/PubSubHubbub) 。是的，真的是这个名字。你可能会认为这是某种公司或产品的名称，但它实际上是一个非常类似于 WebHooks 的协议。

基本上你订阅了一个 PubSubHubbub 提要，给出了一个回调 url。然后，PubSubHubbub 提供者向回调 url 发送 POST 请求以确认订阅。

一旦这个过程完成，提供者将在新内容发布到回调 URL 时向它发送 GET 请求。听起来很完美！

然而这一次我持怀疑态度。API 没有帮助。RSS 有局限性。肯定会出问题的。在对流程进行了一次测试运行并了解到来自 Superfeedr (Medium 的 PubSubHubbub 提供者)的 GET 请求实际上并没有文章的内容，使其变得无用之后，我的假设被证明是正确的。

了解到这一点后，我决定最好的办法是在这一点上坚持使用 RSS 提要，并简单地[删除从 db](https://github.com/Qlik-Branch/branch-resource-library/blob/67f075a487d2751406cb8a3990230a54284943e1/feed-pull.js) 中删除故事的代码。

### 问题二:如何把缺失的故事找回来

由于 RSS 只显示最近的 10 篇文章，而 PubSubHubbub 方法是为未来的文章准备的，所以我有两个可能的选择。首先是手动将信息输入数据库，作为一名编码人员，这将违背我大脑中的每一个冲动。

所以剩下的唯一选择是编写一个脚本，通过 HTTP 请求下载数据。这涉及到以下几个方面:

1.  找出 Medium 为出版物列出故事的位置([https://medium.com/](https://medium.com/)//)
2.  分析 HTML 以确定如何为每个故事提取链接
3.  分析故事页面的 HTML 以确定如何最好地从中获取数据

1 和 2 本身非常简单，Medium 对第三个非常有帮助，它在 meta 标记中提供了我们需要的大部分数据。至于内容本身，只需要找到正确的 div 即可。有了这些信息，我只需要找到一个 npm 包来帮助我解析 HTML(谢谢 [cheerio](https://www.npmjs.com/package/cheerio) )并编写[脚本](https://gist.github.com/rjriel/8ec8cbfb0b87f1d5c65989d2e675873b)。

### 那么现在我们完成了，对吗？

我们可以，或者我们可以让事情变得更好。如果媒体上的故事被删除，仍然没有任何代码来删除它们，可能有一种方法可以使用 PubSubHubbub 来使用订阅过程，而不是每隔一段时间运行一次脚本。

如果你想找点乐子，我鼓励你进行[回购](https://github.com/Qlik-Branch/branch-resource-library)，尝试解决这些问题，如果你做了，提交一个拉取请求。

* * *