# 我喜欢的几件事反应自然。还有一些我不知道。

> 原文：<https://dev.to/brightdevs/a-few-things-i-like-about-react-native-and-a-few-i-dont-4n1>

React Native 仍然是公司的热门话题。在花了一些时间之后，我们许多人都有一些想法，我认为关于它还有很多要说的，所以我想分享一些我的观察和感受。

这篇文章非常主观，所以请不要把它当作唯一的真理。实际上，有多少人使用过 RN，就有多少不同的观点...甚至更多:)

### 文档

或者我应该说:“缺少它”？并不是完全没有文档。它只是很差，不完整，因此有时不是特别有用。
一个简单的例子:试着理解`KeyboardAvoidingView` [行为](https://facebook.github.io/react-native/docs/keyboardavoidingview.html#behavior)。

### *逮到你了！*

即使你真的理解了`KeyboardAvoidingView`，让我补充一下，它在 Android 和 iOS 上的工作方式不同，所以几个小时后，你要么以一些奇怪的解决方案告终(比如把你所有的视图放在另一个视图中，实验证明这是有帮助的，但仍然需要一些调整)，要么你放弃，说:*让我们希望用户足够聪明，隐藏键盘，找到屏幕底部的登录按钮*。

有趣的事实:在某个时候，我发现键盘回避行为工作得很好，直到我打开下一个屏幕，另一个，最后回到第一个——然后就不行了。魔术！

(现在我开始认为键盘回避功能应该有自己的博客...)

### 社区驱动发展

我是开源项目的忠实粉丝，但 RN 在这方面有点令人失望，尤其是考虑到它是由脸书维护的。不幸的是，维护人员跟不上大量的报告问题和请求。他们甚至承认，通过发布像[这个](https://github.com/facebook/react-native/issues/5988#issuecomment-185384590)这样的自动评论:

> 你可能已经听说过，React Native 正变得越来越受欢迎，事实是我们正被围绕它的活动搞得有点不知所措。有太多的问题需要我们妥善处理。

而且还挺能理解的，但是你在看这个帖子之前了解过吗？很容易被炒作，然后后悔。

我不喜欢的另一点是 GitHub 问题的处理方式。他们中的许多人被关闭，并转移到另一个问题跟踪(但并不总是)。我知道使用比 GitHub 更好的工具来管理这些问题可能会更容易，但是，嘿，你为什么要关闭它们呢？有几次我听到类似于“RN 在 GitHub 上只有 500 个开放的问题，考虑到它的规模，它一定是相当稳定的”。没有。([例 1](https://github.com/facebook/react-native/issues/15154#issuecomment-335220801) ，[例 2](https://github.com/facebook/react-native/issues/9866#issuecomment-260227960) )
而且通常你甚至不知道这个问题是否被修复了——它在任何地方都没有被提及(无论是 GitHub 还是 [ProductPains](https://react-native.canny.io/feature-requests) )。

### 调试

Eliasz 已经在他的文章中提到了我们的一些调试经验，但是让我插一句。

*   该应用程序在调试时可能会慢得多(事实上，当您启用调试所需的 *dev* 模式时，它会慢下来)。在我们切换到不同的导航库之前，从一个屏幕导航到另一个屏幕需要花费 2 分钟(！).
*   在设备上访问应用内开发者菜单是一件痛苦的事情。如果您使用的是模拟器，您可以使用键盘快捷键调出开发人员菜单，但是在设备上您使用的是摇动手势。现在，想象一下你每天摇动平板电脑几十次(甚至更多)。损坏的设备不能退款。
*   我不知道其他调试器是否也有这个问题，但在使用基于浏览器的调试器时，我遇到了打印到控制台的陈旧或不完整的数据。打印整个对象与逐个打印其属性会产生不同的结果。

### 一个共同的代码库

分享 Android 和 iOS 的代码真是太棒了。不再有遗漏翻译、复杂算法、颜色主题、字体等问题。真是松了一口气！

### 创建 ui

如果我可以把一个 RN 特性带到一个废弃的本地开发岛上，那就是[布局](https://facebook.github.io/react-native/docs/flexbox.html)。现在，再次从事一个非注册护士项目，我必须承认我想念它。这可能是我见过的最令人愉快的代码-XML 混合。

### 故事书

[Storybook](https://github.com/storybooks/storybook/tree/master/app/react-native) 是一个很棒的工具，可以帮助你在不运行应用程序的情况下设计和开发 UI 组件。从 Android 开发人员的角度来看，它甚至比 Android Studio 中的布局预览模式更好，因为使用 Storybook，您还可以轻松地用一些真实数据填充您的组件，并查看它的外观。
在某个时候，我同时使用了两个 Android 模拟器和一个 iOS 模拟器，多亏了故事书，我几乎立刻就在 UI 中测试了我所有的代码更改。

### 总结

我知道读完这篇文章后，你可能会觉得 RN 有太多可怕的问题，你应该不惜一切代价远离它，但我真的不是这个意思。就是喜欢抱怨很多:)

如果你决定试一试，相信我，这是值得的，那么现在你准备得更充分了。即使你决定不再坚持，你将获得的经验也是无价的。

我很乐意看到 RN 成熟到你不必如此纠结的地步。

(我希望它用的是 Kotlin 而不是 JS/TS...)

最初发布于 [brightinventions.pl](https://brightinventions.pl/blog/)

作者 Andrzej Zabost，光明发明
[博客](https://azabost.com/)，[电子邮件](//andrzej.zabost@brightinventions.pl)的安卓开发者