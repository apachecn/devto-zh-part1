# 避免配置中的原始困扰——我的方法

> 原文：<https://dev.to/seankilleen/avoiding-primitive-obsession-in-configuration----my-approach-2c5m>

今天早上醒来，我看到了 Arialdo Martini 的一篇很棒的博客文章,这给了我写博客的灵感(谢谢，Arialdo！)

在这篇文章中，他描述了在类中使用原始变量的问题，以及使用服务定位器模式解决这些问题的缺陷。这是一本好书；我建议去看看。他得出的结论(我在这里过于简化了)是配置设置作为值对象工作得更好，并展示了一些技巧，如何以一种更轻松的方式实现这一点。

我绝对同意，有意义的时候，值对象比原语好。然而，有时候原语集合确实是最有意义的。

所以，我想我应该分享一些当前的想法，关于我如何喜欢把配置注入到我的类中。

[阅读这篇关于 Excella.com 的文章的其余部分](https://www.excella.com/insights/avoiding-primitive-obsession-in-configuration-my-approach)

[On Excella:避免对配置的原始痴迷——我的方法](https://SeanKilleen.com/2016/12/avoiding-primative-obession-configuration-as-contract/)最初由 Sean Killeen 于 2016 年 12 月 27 日在 SeanKilleen.com[发表。](https://SeanKilleen.com)