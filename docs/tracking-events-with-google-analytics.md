# 使用 Google Analytics 跟踪事件

> 原文：<https://dev.to/traaidmark/tracking-events-with-google-analytics>

*请注意:这篇文章写于 2015 年 7 月 14 日我自己的博客上。我将相关(有趣)的文章移到这里。我现在做了一些编辑。*

谷歌分析可以做一些非常棒的东西，它几乎是任何网站的标准补充。我们用它来看有多少(或很少)人访问我们的网站，从哪里来，到哪里去。今天的小实验是关于以一种友好和前端快乐的方式跟踪网站内的特定链接。

事实证明，谷歌分析 API 非常强大，你可以用它做很多事情。当我设计这个网站的时候，我添加了一大堆行动号召类型的链接，我觉得看到它们对我的最终战略有多大价值会很棒。对我来说，最简单的方法就是将这些链接视为目标，并专门跟踪它们。

## 让我们来构建一个可重用的事件跟踪器！

我们的计划是以这样一种方式创建 google 事件跟踪器，它很容易在您的网站中实现，并且可以根据我们的需要随时重用。

### 该功能

跟踪器本身绝不是庞大的代码，但每次重写都很麻烦，尤其是如果我们可以将其缩减为一行调用。

这确实是一个最基本的例子，但是它非常有用，可以为你的站点提供很多价值。

```
$('.ga-tracker').on('click', function() {

  // I want to know on what page the event fired, as my links are on many pages.
  var LOCATION = window.location.href

  // The category of the event. On my site I have two - general tracker and social trackers.
  var CATEGORY = this.data('category')

  // The action that takes place. Example: On my social tracker I made it 'share'.
  var ACTION = 'Click'

  // The label. I add the label we pass in track(); function and add the location.
  var LABEL = LOCATION + ' > ' + this.data('label')

  // Send data to Google Analytics
  ga('send', 'event', CATEGORY, ACTION, LABEL)

}) 
```

### 在你的 HTML 中实现这一点

上面的事件追踪器是一个非常基本的东西，它只是追踪特定的链接。因此，我们的实现同样简单。我们只需将我们在上述要点的“B”部分中创建的跟踪器中命名的类附加到我们想要跟踪的链接上:

```
<a href="#" class="ga-tracker" data-category="General" data-label="Link #1">This is my tracked link</a> 
```

真的是这样。如果你点击链接几次，你会在谷歌分析中看到结果:

*   **举报** >行为>事件

## 何去何从

这是一个很小很小的功能，做一点点工作。这是基本的。那么我们能做些什么呢:

*   实际数据
*   获取更多有用的数据

### 处理数据

我注意到的第一件事是，活动数据很酷，但不太友好，相当隐蔽。我创建了一个定制的仪表板，这很酷，但对我来说并不那么令人兴奋。它没有告诉我太多。

所以我设立目标，基本上就是把那些事件转化成转化，这才是你真正想做的。现在，您可以将被激发的事件视为人们在做您希望他们做的事情。如果这个数字很高，那就是好东西！如果相对于你的页面浏览量和独立访问量来说很低，那么你就有一个很好的机会去找出你做错了什么！

不过这已经超出了本文的范围，所以让我们回到代码语言。

### 获取更多有用的数据

我的例子跟踪链接。就这么简单。但是我们可以追踪人们在网站上做的许多其他事情。想象一下知道人们何时滚动的可能性？

就 UX 的设计而言，我们可以追踪这个奇特的旋转木马功能到底有多有价值。

我们能做的事情太多了，所以我想我们需要做一点实验。敬请期待！

## 测试

由于 Google Analytics 的报告通常会延迟一点，测试可能会有点麻烦。有一个 [Chrome 扩展](https://chrome.google.com/webstore/detail/google-analytics-debugger/jnkmfdileelhofjcijamephohjechhna)可以用来检查(如果你不介意筛选控制台消息的话)。如果你真的使用它，你可以寻找这样的东西:

```
Running command: ga("send", "event", "Social", "Share", "Facebook : http://localhost:4000/blog/code/track-events-with-google-analytics/") 
```

最简单的测试方法是简单地使用谷歌分析实时视图。这有点危险，但确实工作得相当好。

## 进一步阅读

*   [事件跟踪-网络跟踪(analytics.js)](https://developers.google.com/analytics/devguides/collection/analyticsjs/events)