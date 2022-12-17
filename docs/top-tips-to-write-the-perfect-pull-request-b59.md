# 撰写完美拉动式请求的顶级技巧

> 原文：<https://dev.to/gifableapp/top-tips-to-write-the-perfect-pull-request-b59>

# 什么是拉取请求？

如果你在一个较小的团队中工作，或者为一个不使用 [Git](https://en.wikipedia.org/wiki/Git) 的组织工作，那么你可能不熟悉拉请求(PR)。简而言之，它是对您希望添加到共享代码库中的变更的简要描述，您试图尽可能多地给出变更的上下文，以帮助开发团队审查您的代码。

# 第一接触点-标题

这是任何开发人员都会阅读的第一件事，用它来设置您的更改的上下文以及为什么它们是必要的。保持简短，但准确描述发生了什么。

**坏**
[![alt text](img/e5d68bef421fef1acefb0cd4ad82cca4.png "Updates to the vc")](https://res.cloudinary.com/practicaldev/image/fetch/s--lMpTQio6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/blkj7pkivrf7iyw4k1jp.png) 
**好**
[![alt text](img/3d4f026fa707d5a7ef84726b7e961c0c.png "Text updates to the view controller")](https://res.cloudinary.com/practicaldev/image/fetch/s--L_KM_ady--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/37kb4zfv07e63ug4w5q6.png)
**好**
[![alt text](img/a0709c3fb08974e00d6b7919eb9478ff.png "Updating the `MainViewController` description text")](https://res.cloudinary.com/practicaldev/image/fetch/s--o5RveHlz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tnsuuwhjeyohblp02ppf.png)

# 清晰是关键

您编写描述的唯一目的是帮助开发人员检查您的更改，包括尽可能多的更新细节。既作为高层次的用户界面描述，也作为对其他人可能难以理解的代码更改的任何部分的详细解释。

# 包括什么

1.  **功能规范的链接** -通常新功能是以可链接到您的 PR 的格式定义的，包括此链接将允许审阅者获得所做更改的完整上下文。

2.  **功能规范的简要总结** -如果你做了一个小的改变，然后通过保存他们的点击来帮助开发者，总结 PR 描述中要求的改变。包括链接，以防万一。

3.  **您采用的方法和原因** -您是否使用递归启发式算法而不是简单的硬编码字符串来实现您的描述文本？你最好解释一下你的理由。

4.  **风险和关注领域**——你不确定你修改的代码是否会对代码库的其他地方产生影响？越早提出这个问题越好，也许戴夫团队摇滚明星会知道得更多。

# 格式为王

在好公关的世界里，漂亮的总是赢家。一般来说 [Markdown](https://en.wikipedia.org/wiki/Markdown) 用于格式化 Git 主机的主要玩家的 PR 描述。利用列表、标题、副标题和链接。如果它易于阅读，你将更有可能获得对你的修改的回顾。

# 图片和 gif

如果一张图片代表一千个单词，那么 Gif 代表一百万个单词。当然，我们会说，与 macOS 上的屏幕录制应用程序相关联，公平地说，图像对于显示 UI 的静态变化是有益的。但是，如果你想给出一个实质性变化的完整背景，那么 Gif 是一个不错的选择，它将把你的 PRs 设置在包的前面，真正帮助开发人员看到发生了什么，而不用自己运行这些变化。

## 可赠与

Gifable 是一个超级简单的屏幕录制工具，可以在几秒钟内为你制作 gif。记录您所做的更改，并将`.gif`文件作为图片链接上传到您的 PR 描述中。

[![alt text](img/e3389f56a759bdd2dce3bad3d79b4d7a.png "Gif of a ascii train driving across the terminal screen")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gFqEwdVB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3tdjln90vxk4s8o1h3xq.gif)

我们希望这个博客对你有所帮助，如果你觉得有用的话，试试这个程序吧，快乐编码！

[可赠送下载(仅限 MAC OS)](https://goo.gl/nEu63Q)
[Gifable.co.uk](https://goo.gl/BSe3ea)
[推特](https://twitter.com/gifableApp)