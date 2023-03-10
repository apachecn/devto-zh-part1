# 探索未知的开源基础设施

> 原文：<https://dev.to/teabass/exploring-unseen-open-source-infrastructure>

在开发 [Libraries.io](https://libraries.io) 的时候，我经常偶然发现一些库，它们似乎被大量开源项目所使用，但却没有任何在 GitHub 上成为受欢迎项目的常见迹象。

以 [debug_inspector](https://github.com/banister/debug_inspector) 为例:

*   25 颗星
*   21 次提交
*   4 名贡献者
*   5 名观察者
*   4 个叉子
*   2 个未决问题
*   两年多前的最后一次提交

从表面上看，如果你把 GitHub 页面误认为是一个很少使用的小项目，那是情有可原的，而事实上它被列为超过 [111，000](https://libraries.io/rubygems/debug_inspector/dependent-repositories) 个开源项目的依赖项！

Libraries.io 有许多不同的页面来展示有趣的和意想不到的库列表，包括那些总线因子低的库和那些被 T2 从它们的包管理器中拉出来的库，所以我想我应该添加一个页面来展示那些最不受欢迎但被频繁使用的库。

今天下午我发布了看不见的开源基础设施页面:[https://libraries.io/unseen-infrastructure](https://libraries.io/unseen-infrastructure)

套用 [Arfon Smith](http://www.arfon.org/) 关于[请求提交#3](https://changelog.com/rfc/3) 的话，“GitHub 上的星是对关注度的衡量，更类似于脸书上的赞，而不是对质量或使用的衡量”，该页面显示了数百个项目，这些项目被至少 1000 个其他开源仓库所依赖，但星数不到 100。

这些项目中的任何一个都可能成为下一个 [left-pad](https://www.theregister.co.uk/2016/03/23/npm_left_pad_chaos/) 或 [Heartbleed](https://en.wikipedia.org/wiki/Heartbleed) ，其中一个底层的、关键的库被高度使用，但却很少被关注。一个未被注意的安全问题或被放弃的项目可能会导致成千上万的软件应用程序受到影响。

您可以通过查看这些库的代码、帮助解决未解决的问题、在社交媒体上分享它们，以及感谢维护人员为保持事情在幕后运行所做的努力(通常是没有回报的)来帮助这些项目和依赖它们的社区。

我和本打算在明年及以后做更多的事情，那就是帮助强调和支持对当今技术基础设施至关重要的开源软件。

另一个需要探索的领域是系统级的包管理器，如 apt 和 yum，它们包含更重要但经常被忽略的库，这些库通常不在 GitHub 这样的社交平台上托管，但对软件世界仍然至关重要。

如果你想参与进来，整个项目是[开源的](https://github.com/librariesio/libraries.io)，我们很乐意帮助你开始贡献，或者如果你想在所有这些数据的基础上构建工具，请查看[库. io REST API](http://libraries.io/api)

一如既往地，在推特上关注我们，通过 [@teabass](https://twitter.com/teabass) 、 [@benjam](https://twitter.com/benjam) 和 [@librariesio](https://twitter.com/librariesio) 获取更多更新。