# Whoops！PHP 中错误的直观显示

> 原文：<https://dev.to/phpprofi/whoops-----php-5fl2>

当我们遇到 PHP 中的错误时，每一次，婉转地说，都是无声的恐怖。如果你记得你第一次看这封信，想知道"你想要什么"，想知道阅读和理解这一奇迹，你一定会同意我的意见。不是这些错误信息不翔实——那里没有足够的信息——而是几乎无法阅读。好吧，至少很不方便，而且由此导致的时间很长，这直接影响了开发速度。当然，如果你有，并且配置了 T0，这有点挽救了情况，但不是特别的。

此外，由于这些邮件不可读，也不容易理解，因此您需要很长时间(比您希望的时间)在您最喜欢的 IDE 中查找代码。最后，当您打开代码中的正确位置时，您立即意识到，错误显然不是发生在这里，而是发生在堆栈上或下的某个位置。并需要重新打开出错的机座，学习调用秒，并重新搜索所需的代码段。令人不快和浑浊的职业。

在这里，我们可以解决所有这些问题。

[![](img/8150307a2f7c32d250a903c1a13bf164.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mlLch-gB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://phpprofi.ru/resources/img/blogs/ee7f40e6-9ea8-4827-bc90-eff9bf860dcf.jpeg)

## t0t 1 什么是 Whoops

http://phpprofi.ru/blogs/post/77 的читать