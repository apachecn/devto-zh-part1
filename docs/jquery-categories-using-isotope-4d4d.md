# 使用同位素的 jQuery 类别

> 原文：<https://dev.to/adamkdean/jquery-categories-using-isotope-4d4d>

虽然我现在不怎么做设计，但只要有机会，我还是很喜欢。在这个周末和我的另一半讨论了一个带有动态分类的砖石风格的网格后，我试着在网上找到了实现。如果失败了，我认为这是一个做一些*设计*的好机会。

我想要的是一个 div 的网格，我可以根据它们的类别进行过滤。例如，这可以用于投资组合，允许用户过滤他们是否希望看到网站、代码片段或图形设计。

[![It's all just apples and oranges...and bananas](img/57757eb0ffd776982d9ae61d5b90f43a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vRu2wPbM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/QeMkBmN.png)

我把设计放在一起，并让它在 jQuery 上运行良好，但它并没有很好地运行。我记得听说过 jQuery Masonry，并决定尝试一下。老实说，它很垃圾，感觉很旧。当我读到[同位素](http://isotope.metafizzy.co/)时，我正在寻找最荒谬问题的答案。

我试了一下，它真的很棒！

除了一个小问题，即在进行尺寸计算之前图像没有被加载——这个问题通过使用[大卫·德桑德罗](http://desandro.com/)的 [imagesLoaded](https://github.com/desandro/imagesloaded) 插件很快得到解决——一切都很好。我强烈推荐用同位素，可能又要想出借口用了！

在 GitHub 上找到项目:[https://github.com/Imdsm/jQuery.Categories](https://github.com/Imdsm/jQuery.Categories)
看工作演示:[http://imdsm.github.io/jQuery.Categories/](http://imdsm.github.io/jQuery.Categories/)