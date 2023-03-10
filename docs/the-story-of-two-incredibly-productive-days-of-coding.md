# 两天令人难以置信的高效编码的故事

> 原文：<https://dev.to/ben/the-story-of-two-incredibly-productive-days-of-coding>

两天前，我发了这条微博:

> ![Ben Halpern profile image](img/3814cc9cbc453345725bf2b5e578e8a0.png)本哈尔彭@本哈尔彭![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)我们计划在周四推出一个功能，但我还没有写任何代码。祝我好运❤️2017 年 6 月 20 日下午 13:36[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=877158153011568641)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=877158153011568641)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=877158153011568641)

由于日常运营、新开发人员入职和一般拖延的各种责任，我已经错过了唯一一个有截止日期的编码任务。两天时间足够编写一个快速的特性了...*也可能不是*。这就是问题所在。当我去承担这个任务时，我真的不知道要花多长时间。

有问题的功能是为[开发者到](https://dev.to/)的原生视频播放器，因为今天是我们的发布日，所以你可以在这里看到它的运行:

[![video camera](img/162cbaf8d6272b73c3862dce71e9fe2e.png) 00:00 ](/thepracticaldev/moving-from-a-java-monolith-to-microservices-at-squarespace) [![thepracticaldev](img/9beda539d204a28e09d7b574a6cb63a3.png)](/thepracticaldev) [## 在 Squarespace 从 Java Monolith 转移到微服务

### 开发人员至员工 6 月 22 日 171 分钟阅读

#java #microservices #architecture #videos](/thepracticaldev/moving-from-a-java-monolith-to-microservices-at-squarespace)

我们选择了[jwp player](https://www.jwplayer.com/)作为服务，它符合我们对播放器和视频托管的直接要求。这似乎是我们现在想要的正确选择:轻量级、可定制的视频播放器库和简单的托管服务，以及我们未来想要的:额外的配置、可扩展的解决方案，用于更大的操作。但是我没有阅读任何文档，我们也不完全确定方向。如果顺利的话，两天时间足够了。如果遇到障碍，两天时间并不算长。我们可以依靠其他选择，但我们希望这一切顺利进行，这样我们就不必运送用胶带粘在一起的东西，只是后来被替换了。我们最终得到的是一个最小可行的产品*，我们可以在*的基础上构建，而不是一个我们需要在未来完全取代的产品。

你猜怎么着...进展非常顺利，我实际上在大约 90 分钟内完成了整个项目。在这个过程如此顺利之后，我对那条推特感到有点傻。星期二下午，我完成了。

有了这些额外的时间，除了赶上其他事情和帮助其他开发人员完成他们的任务，我决定通过给评论添加自动时间戳链接来更好地利用这个功能，类似于 YouTube 的做法。

这里有一个例子:

[![andy profile image](img/2c07459d0534ba0281ecd9b826829ae9.png) ](/andy) [ Andy Zhao (he/him) ](/andy) • [<time datetime="2017-06-22T15:59:56Z" class="date-short-year"> Jun 22 '17 </time>](https://dev.to/andy/comment/b8d) 

我喜欢在 [4:30](/thepracticaldev/moving-from-a-java-monolith-to-microservices-at-squarespace?t=4:30) 的 web 应用程序类比，关于在卡车移动时更换车轮。此外，我注意到我有一种倾向，想在推出一个功能时让它变得完美。作为一个非常初级的开发人员，听到你们如何采取行动令人耳目一新。

这项功能也非常顺利。对整个事情感觉很好，我很好地 TDD 了这个过程，甚至自己编写了所需的正则表达式，没有查找它。

下面是添加到注释 html 处理方法中的 Ruby 代码，以实现这一点:

```
def wrap_timestamps_if_video_present!
    return unless commentable.video.present?
    self.processed_html = processed_html.gsub(/(([0-9]:)?)(([0-5][0-9]|[0-9])?):[0-5][0-9]/) {|s| "<a href='#{commentable.path}?t=#{s}'>#{s}</a>"}
  end 
```

Enter fullscreen mode Exit fullscreen mode

这个过程不是没有一些牦牛毛。为了实现时间戳链接，我希望确保应用程序的缓存和我们的 CDN 都忽略时间查询参数，这样我们就不会为每个不同的参数提供不同版本的页面。这是一项早就应该完成的任务，它影响了这个 web 应用程序的许多其他部分。这意味着通过 VCL 配置向 CDN [提供一个我们的应用将会关心的参数的白名单。我很高兴我有机会这样做。这主要是阅读文档的问题，一旦我理解了整个事情，实现起来就非常简单。](https://docs.fastly.com/guides/vcl/query-string-manipulation-vcl-features)

很难做出预测，不仅因为任何技术问题都有许多未知的复杂层面，还因为对我们时间的需求同样难以预测。在过去的几天里，我无数次地被拉离“低头时间”,去处理一些小问题，回复邮件，或者变得全神贯注。我试图限制这些干扰，但它们还是发生了。

这个过程非常富有成效和令人满意。希望大家喜欢平台上的视频系列和未来的视频内容。目前，视频上传纯粹是一项管理功能。如果你想上传你自己的视频并分发给[开发者至](https://dev.to/)社区，请发送电子邮件至 [yo@dev .至](//mailto:yo@dev.to)。