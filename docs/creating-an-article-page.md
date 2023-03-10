# 创建文章页面。

> 原文：<https://dev.to/rounakpolley/creating-an-article-page>

参见 [CodePen](https://codepen.io) 上 Rounak Polley ( [@cfjr](https://codepen.io/cfjr) )的一篇交互文章页面的 Pen [语义正确响应式设计。](https://codepen.io/cfjr/pen/OjQzZw/)

[![Preview of the design](img/b09f5a0bb57e6f64e9df583f6afa1690.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gYAPPAvS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oiceybajo3jv8zns14ao.PNG)

*有时候我们会忘记编写正确的 html*
的重要性，虽然几乎所有的事情都可以通过使用`<div>`和`<span>`标签来实现，但是它并没有告诉我们页面上的内容实际上是什么，在哪里。
语义 html 是利用 html 来强化结构意义。它是关于使用标签、类名和 id 来强化标签中内容的含义。通过向我们的文档添加语义标签，我们提供了关于该文档的附加信息，这有助于交流。具体来说，语义标签让浏览器清楚地知道页面及其内容的含义。这种清晰性也会传达给搜索引擎，确保为正确的查询提供正确的页面。每个人都希望自己的网站在搜索引擎结果中排名靠前，这一点非常重要。)

在 [HTML5 语义元素](https://www.w3schools.com/html/html5_semantic_elements.asp)阅读更多内容。
[![A pictorial representation of html5 semantics](img/226f6748696a02b90f52ae074fbe4607.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vRRReFYA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://www.w3schools.com/html/img_sem_elements.gif) 
(这里，在这个例子中我没有加上`<nav>`、`<header>`或`<footer>`，因为它们可以单独设计)。

现在，实际上进入文章页面的制作。文章页面从一开始就在互联网上非常普遍，并以博客、新闻等形式继续存在。
当文章本身很大时，必须使用带有单独`<headers>`的`<section>`标签将它分成几个部分，并且`<article>`本身需要一个`<header>`。用可选的`<figcaption>`将图像放置在`<figure>`中是一个很好的做法，当然，`<img>`必须有一个`alt`属性。

但是，我们可以做得更多，因为对于网页设计来说**魔力在于细节**。
我们可以帮助用户在我们的文章中找到正确的内容，这可以通过添加页面内导航来实现，一种方法是使用`<aside>`和`role="navigation"`(简化辅助技术)。同样，我们可以使用 jQuery 来指示用户当前所在的文章部分，并平滑地滚动各个部分，而不是使用简单的链接锚。

接下来，响应式设计(使用**自举**)总是很棒的！

如果你还在使用表格布局，是时候学习 Bootstrap 了，因为使用表格来布局页面是一个非常糟糕的做法。
*(为什么网络开发者走出咖啡馆？因为他不喜欢桌子的布局！)*

当使用 Bootstrap 而不是显式指定图像的宽度和/或高度时，我们可以在当前列(或列组)中添加另一个“行”,并指定我们希望图像在该列中占据多少宽度(添加对`<img>`标签的适当 Bootstrap 调用),默认情况下保持纵横比。
*这就像嵌套引导行(行中的行)。*

我们应该总是在开发者工具模拟器中检查我们的设计，并且尽可能地调整浏览器的大小。此外，在 Chrome、Mozila、Edge 和 IE 中，我发现 IE 带来的麻烦更多(毫无疑问，人们讨厌它在 IE 上创建所有的迷因和帖子)。所以，也去 IE 上看看吧(因为人们仍然在使用它，而且它实际上也没那么糟糕)。
所以，我发现固定位置`<aside>`实际上被部分隐藏了，所以我必须添加一个`overflow-y : auto`。

简单的设计有时候更优雅更合适。简单明了的字体和各种深浅搭配良好的灰色通常可以代替大量的颜色和花哨的字体。

最后，但不是最不重要的，我们可以在这里添加`<summary>`，我放了一些引号(使用`<q>`而不是常规的`" "`)。

了解更多详情...

参见 [CodePen](https://codepen.io) 上 Rounak Polley ( [@cfjr](https://codepen.io/cfjr) )的一篇交互文章页面的 Pen [语义正确响应式设计。](https://codepen.io/cfjr/pen/OjQzZw/)