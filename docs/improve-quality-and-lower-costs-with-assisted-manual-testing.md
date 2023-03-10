# 通过辅助手动测试提高质量并降低成本

> 原文：<https://dev.to/mortoray/improve-quality-and-lower-costs-with-assisted-manual-testing>

测试图形用户界面是一项挑战。全自动化很吸引人，但它很昂贵，而且不会产生好的结果。UI 是一个真正的人应该参与的东西。通过使用增强的测试应用程序，我们可以以一种经济高效的方式利用手动测试。

我在 TestTalks 的[播客上讨论了这种测试方法。这是对该主题的更深入的探索。](https://joecolantonio.com/testtalks/153)

## 手动测试 app

真正的测试人员，人类，有惊人的发现问题的能力。一个人不受自动化脚本执行的有限数量的检查的约束。相反，他们会持续关注布局、图形故障、数据异常、性能问题、设计缺陷和粗略的用户交互等问题。辅助手工测试利用了这种能力。

手动测试应用程序是专门为手动测试构建的自定义程序。该应用程序展示了真人将执行的几个步骤。我们在 [Fuse](https://fusetools.com/) 有三个这样的应用。

[![Sample page](img/6bd7d5e6576fd9fa0bc2c8b0052fd7bc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4RsVsvCY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://mortoray.files.wordpress.com/2017/05/screen-shot-2017-05-22-at-09-11-12.png)

甚至这个标题屏幕也测试功能。徽标部分(这是一种特殊的图像格式)至少遇到了一个平台特定的问题。

我们的主要测试应用程序简称为“手动测试应用程序”。测试人员将它安装在我们支持的任何平台上，并按照测试中的说明进行操作。这本书有 40 多页，涵盖了我们提供的各种功能。

## 自备机组

每个页面都是一个独立的测试，防止一个页面上的错误泄漏到另一个测试中。我们不想让测试人员为一个单一的缺陷提交一连串的问题。

[![Screenshot](img/b5b4fc8a9a62e75b72287e4195c6c9fe.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fy1tlHp4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://mortoray.files.wordpress.com/2017/05/screen-shot-2017-05-22-at-09-13-33.png)

动画测试包括基本绘图、触发响应、变换和动画计时。这揭示了一些设备的性能问题，以及背景渐变的渲染问题。

注意右上角的小`i`图形。这些是对每个页面测试人员的指示:做什么和期望什么。这消除了提供冗长测试脚本的需要。在大多数手工测试中，维护脚本是一项巨大的成本，所以最好避免它们。

我们不希望测试人员每次运行应用程序时都阅读这些说明，也不希望他们花费太多的大脑空间来理解测试。许多测试是不言自明或显而易见的。测试人员可能只需要第一次阅读说明，或者在发生可疑情况时参考说明。

[![Screenshot](img/db321598507677672f4805e04f58a8ff.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--14bzZEDR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://mortoray.files.wordpress.com/2017/05/screen-shot-2017-05-22-at-09-32-32.png)

在这里，说明嵌入在测试本身中。虽然是基本的，但它涵盖了系统中的一些重要功能。幸运的是，这个页面已经很久没有显示任何错误了。

## 只有不可自动化的

创建一个增强的测试应用程序需要足够模块化的软件设计。我们需要使用测试应用程序中的所有功能。我们不必担心额外的工作，因为模块化只是好的开发实践，不管测试如何。

测试应用不应该成为垃圾场。我们只想包括一组眼球有帮助的测试。关注那些人类可以处理，但是很难编写单元测试的事情。具有视觉效果、动画或用户交互的功能是很好的选择。

起初，在手动测试应用程序中测试一切是很诱人的。从短期来看，它似乎减少了程序员的工作量，但是却损害了测试工作。如果有太多相似的测试，或者只是太多的测试，测试人员就很难识别问题。他们被迫更频繁地查阅说明书，并且很难记住他们上次看到的内容。这降低了测试人员的速度，妨碍了他们识别缺陷的能力。

[![Screenshot](img/24623dbfa46be904f57d53d993d3c47d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VXgQvmi5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://mortoray.files.wordpress.com/2017/05/screen-shot-2017-05-22-at-09-32-51.png)

虽然很容易理解，但这个页面结合了大量的功能。我在编写它的时候修复了几个缺陷，但是我不相信它在测试的时候还会发现任何新的错误。回归测试也不错。

手动测试也不应该表现得像单元测试。我们并不试图孤立一个单独的特性，也不试图覆盖所有的 API。我们通过将几个功能合并到一个页面中来减少测试的总数。测试人员使用他们惊人的生物神经网络同时处理许多事情。

[![Screenshot](img/9747e20a80a3e938eb06ed0a269fef55.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--W2foNocY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://mortoray.files.wordpress.com/2017/05/screen-shot-2017-05-22-at-09-33-27.png)

本页测试了许多矢量绘图功能的组合。虽然不详尽，但这是一个很好的检查。我们有一个单独的测试应用程序，涵盖更多这些功能。我们可以安排在不同的时间间隔和不同的设备上进行测试。

## 议题多

一个写得好的辅助测试应用程序应该包含在任何有图形用户界面的应用程序中。这给了我们一个优势，让一个真实的人来分析应用程序，而不需要维护昂贵的测试脚本。它避免了自动化 UI 测试的负担和成本。

甚至程序员也可以使用该应用程序快速验证他们的更改没有破坏一些明显的东西。放在专门的测试人员手中，它会产生一个可靠的回归测试。这对软件的质量是一个福音。