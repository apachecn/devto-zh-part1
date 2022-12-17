# 当你是一个 Rails 开发者，但是你的客户想要 WordPress

> 原文：<https://dev.to/rogerjin12/when-youre-a-rails-developer-but-your-client-wants-wordpress>

*想了解更多类似的内容，请在推特上关注 [ButterCMS，并订阅我们的](https://twitter.com/ButterCMS)[博客](https://buttercms.com/blog/)。*

假设您已经为一个客户端构建了一个 Rails 应用程序。在推出他们的 MVP 几个月后，你的客户想要加强他们的营销，他们要求你在他们的网站上添加一个博客。“如果是 WordPress 就好了”，他们告诉你。"哦，我们的搜索引擎优化顾问告诉我们，它应该生活在我们的主要领域，而不是一个子域."

一想到 WordPress，你就不寒而栗，但是你开始悄悄地研究你的选择。

将 WordPress 集成到你的 Rails 应用程序中是不可能的，但是如果你单独运行 WordPress(或者类似的博客解决方案，比如 Ghost ),它将需要托管，并且必须存在于一个单独的子域中，这将导致你的 SEO 热情的客户非常失望

您探索了设置反向代理的变通方法，但是看起来非常不愉快。即使经历了所有这些麻烦，你仍然需要弄清楚如何定制模板，以使设计符合你漂亮的 Rails 网站——你想知道它们是否可以共享相同的布局和 CSS...

决心找到可以集成到你的 Rails 应用中的东西，你在 Github 中搜寻开源博客引擎。你从一个有希望的仓库跳到下一个。你找到的第一个项目看起来不错但是三年没更新了。另一种方法需要向已经负担过重的数据库中添加大量臃肿的数据库表。你的希望开始消退...

如果这种情况对你来说很熟悉，你并不孤单。

这就是为什么我们在一分钟内创建了一个集成了 Rails 的博客引擎和 CMS。对您的客户友好的用户界面，对您零维护，我们提到过它在**一分钟**内与 Rails 集成吗？

这是证据。

本帖原帖[此处](https://buttercms.com/blog/when-youre-a-rails-developer-but-your-client-wants-wordpress)。