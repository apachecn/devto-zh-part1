# 模拟第 n 类 CSS 选择器

> 原文：<https://dev.to/brightdevs/simulating-nth-of-class-css-selector-pg>

我的前端 web 开发经验还不算长，然而，我已经遇到过几次困难，花了几个小时试图找到一个完美的解决方案。我认为其中一些值得描述。

[![Image](img/fa43a62c8a354649c98940498482a251.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RhQmL2um--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/45ptispl3du926mf02ym.jpeg)

我有一个 HTML 标签的列表，其中一些首先有一个特定的类，而其他的没有。我想要实现的是样式化这个类的最后一个元素。如果你在寻找一个纯 CSS 解决方案，没有 JavaScript，没有 HTML 的变化，我有一些坏消息:没有这样的。当然，你不应该相信我，自己去找。但是如果你碰巧发现了一个，请回来并随意评论。如果你失败了，请继续阅读，看看下面的解决方案是否适合你。

最初的情况是在`<li>`标签之间添加一个分隔符。这很简单:

```
li:not(:last-of-type) {
    border-bottom: 1px solid black;
} 
```

现在，更复杂的是，一些元素最初是隐藏的，可能在单击按钮时变成可见的。隐藏的元素有类`hidden`。对我来说，很自然的是添加类选择器并拥有:

```
li:not(.hidden):not(:last-of-type) {
    border-bottom: 1px solid black;
} 
```

令我惊讶的是，它没有起作用。我认为像`li.hidden:last-of-type`这样的选择器用类`hidden`过滤掉类型`li`的元素，然后选择最后一个。原来它实际上选择了类型`li`的最后一个元素，然后检查它是否有类`hidden`。细微的差别，对吗？

我的问题的解决方案实际上很简单——添加上边框而不是下边框。毕竟隐藏的元素是隐藏的，它们的边框也是隐藏的。

```
li:not(:first-of-type) {
    border-top: 1px solid black;
} 
```

使用相邻兄弟选择器更具可读性。

```
li + li {
    border-top: 1px solid black;
} 
```

这可能是结束。我已经找到了解决问题的方法，并付诸实施。但是如果我真的需要设计类的最后一个元素的样式呢？有办法做到这一点吗？如前所述，没有。至少现在还没有。

在[选择器 4 级编辑草案](https://drafts.csswg.org/selectors-4/#the-nth-child-pseudo)中有描述的第 n 个子伪类的最新版本。它引入了一个可选子句`of S`，该子句只过滤出与`S`选择器匹配的子元素。这还只是一个草稿，因此不应该用于任何产品代码——检查 caniuse.com[的浏览器支持。
确认在已经支持的 Safari 中工作:](https://caniuse.com/#search=nth-child) 

```
li:nth-last-child(1 of li:not(.hidden)){
    border-top: 1px solid black;
} 
```

我们现在能做什么？不幸的是，答案是:打破我们之前建立的一个规则。

1.  打破“HTML 中没有变化”的规则，即把元素分成两个单独的列表:一个列表中的元素总是可见的，另一个列表中的元素是隐藏的。
2.  打破“没有 JavaScript”的规则，只使用 jQuery:

```
$("li:not(.hidden):last").addClass("last-not-hidden") 
```

nice `last`选择器是特定于 jQuery 的，因此既不能用于 CSS 也不能用于 JavaScript。

1.  打破“没有 Javascript”的规则，使用纯 JavaScript:

```
const notHiddens = document.querySelectorAll("li:not(.hidden)");
const lastNotHidden = notHiddens[notHiddens.length - 1];
lastNotHidden.classList.add("last-not-hidden"); 
```

这比前一个更加冗长，但是对于简单的 DOM 操作，您可能不一定需要 jQuery。

好吧，老实说，我已经找到了一个“纯粹的 CSS 解决方案”，但对我来说，它既不是纯粹的，也不是一个解决方案，只是一个真正奇怪的走动。看一看吧，至少你会觉得有趣。

这就是了。没有清晰和好的解决方案。我很幸运，我不需要在任何实际项目中解决这个问题，你呢？你是如何处理这种情况的？

最初发布于 [brightinventions.pl](https://brightinventions.pl/blog/)

软件工程师@光明发明
[电子邮件](agnieszka.olszewska@brightinventions.pl)