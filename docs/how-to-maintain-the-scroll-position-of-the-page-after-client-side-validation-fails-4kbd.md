# 客户端验证失败后如何保持页面的滚动位置

> 原文：<https://dev.to/diskdrive/how-to-maintain-the-scroll-position-of-the-page-after-client-side-validation-fails-4kbd>

这是我今天被卡住了一会儿的事情，我在工作中处理一个页面。

我正在处理一个特殊的页面，它有许多验证器和多个验证摘要对象——包括客户端和服务器端。一个位置是一些客户端验证(通常是底部控件的验证)，如果它在客户端验证失败，它会直接把你带到页面的顶部。对使用者来说有点迷惑。

谷歌搜索表明，这实际上是一个相当普遍的问题，微软已经记录在案，并与的内部有关。NET 要求 FieldValidator 在其 ScrollTo 事件中执行一些代码。

似乎 maintaisincollpositiononpostback，如其名称所示，只在回发后工作。客户端验证不包括回发。

大多数建议是用一个简单的 JavaScript 函数覆盖 ScrollTo 函数，如下所示...

```
<script type="text/javascript">    window.scrollTo = function() { }</script> 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，这对我不起作用。虽然它确实修复了 RequiredFieldValidators 的问题，但它重置了我的 CustomValidators 的位置，这些位置都在服务器端。我没有对为什么会发生这种情况进行任何调查。

最后，我的解决方案是，不要确保只在控件的 onClick 事件上覆盖 ScrollTo，而是像这样初始化验证...

onclient click = " window . scroll to = function(x，y){ return true；};"

到目前为止，这似乎已经解决了问题。然而，我今天才刚刚修复它，所以时间会证明它是否导致了任何其他意想不到的行为。

希望这个帖子有用。

 [T3】](http://msdn2.microsoft.com/en-us/b0eaf8ew)