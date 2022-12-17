# 如何在 jquery 中删除多个属性？

> 原文：<https://dev.to/moreonfew/how-to-remove-multiple-attributes-in-jquery-589h>

很多时候，您可能会遇到希望从一个元素中移除多个属性的情况。例如，考虑下面的 DIV :

```
<div id="#rightSection" data-id="xyz" data-module="abc"> .... </div> 
```

Enter fullscreen mode Exit fullscreen mode

现在使用 jQuery 的 removeAttr()方法，您希望删除 data-id 和 data-module 属性。我们中的许多人试图通过使用 jQuery 的链接特性来多次调用 removeAttr()方法。类似于$('#rightSection ')。removeAttr('data-id ')。removeAttr('数据模块')。然而，尽管这可能行得通，但这并不是编写代码的最佳方式。

## 那么，我怎么去掉多个属性或者我怎么组合多个。removeAttr()语句？

答案很简单。在 jQuery 中，您可以使用空格来分隔属性，从而**删除多个属性。这意味着您必须将所有想要移除的属性传递给 removeAttr()方法，用空格分隔。所以现在你的代码应该看起来像下面这样:** 

```
$("#rightSection").removeAttr('data-id data-module'); 
```

Enter fullscreen mode Exit fullscreen mode

这样，你就不必重复。removeAttr()方法分别移除每个属性。而现在你其实可以少写多做 [![🙂](img/29b3aa14527a87608de7afa1cf6d4c59.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QEVhyXtP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s.w.oimg/core/emoji/13.1.0/72x72/1f642.png)

如果你喜欢这个解决方案，请告诉我们。请喜欢和分享，并帮助您的朋友了解这一点！

帖子[如何在 jquery 中删除多个属性？](https://www.moreonfew.com/how-to-remove-multiple-attributes-in-jquery/)最早出现在 [MoreOnFew](https://www.moreonfew.com) 上。