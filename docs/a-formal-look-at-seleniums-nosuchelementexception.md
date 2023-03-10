# 正式查看 Selenium 的 NoSuchElementException

> 原文：<https://dev.to/beatngu1101/a-formal-look-at-seleniums-nosuchelementexception>

*原发布于[媒体](https://medium.com/@beatngu13/a-formal-look-at-seleniums-nosuchelementexception-bafce97df2e7)。*

作为 Selenium 用户，您可能知道这个故事:由于臭名昭著的`NoSuchElementException`，您的测试突然失败了。但是这并不仅仅发生在硒元素上。大多数其他的测试自动化框架都有自己的(花哨的)名称，但是潜在的问题总是相同的:找不到特定的 UI 元素。

早在 2016 年，作为我母校项目工作的一部分，我对几个兼容 Swing 的捕获和重放工具做了一个小的回顾。在这项工作中，我偶然发现了 Scott McMaster 和 Atif M. Memon 的[“一个基于启发式的 GUI 测试用例维护的可扩展框架”](http://www.cs.umd.edu/~atif/pubs/McMasterMemonTESTBEDS2009.pdf)。在本文中，作者介绍了一个描述这种情况的形式化模型——*GUI 元素识别问题*。这个问题通常发生在回归测试中，因此，在被测系统(SUT)的版本 *n* 和 *n* + *i* 之间。(请注意，这些版本不一定是稳定版本，构建的每个更改都可能导致该现象。由于麦克马斯特和梅蒙似乎专注于桌面应用，这影响了他们选择的术语。例如，SUT 中的一个单独的窗口表示为 set *W* ，包含各种可操作的 GUI 元素 *e* 。如果您来自 web 应用程序背景，您可能会谈到页面和 UI 元素。有时，您还希望断言或验证标签等不可操作的元素，以确保它们包含特定的字符串。尽管如此，概念本质上是相同的。 *W* 是 *W* 的后续版本，其中元素 *e* 的数量可能会有所不同(即|*W*|≦|*W*' |，如果您不熟悉这种符号，可以看看这些关于[逻辑](http://www.rapidtables.com/math/symbols/Logic_Symbols.htm)和[集合论](http://www.rapidtables.com/math/symbols/Set_Symbols.htm)的快速参考)。现在，GUI 元素识别问题被定义为:

> 对于 *W* 中每个可操作的 GUI 元素 *e* ，在 *W* 中找到一个相应的(可能被修改的)元素 *e* ，其操作实现相同的功能。

为此，每个元素 *e* 、*e*'∈*W*∩*W*'必须被分配给以下三个集合中的一个:

*   **删除: *W* 中的**元素在 *W* 中没有对应的元素，因此在版本 *n* + *i* 中被删除。

[![](img/6c42e912f6589d01062bb9f0079e5cb7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QHugtN7U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A-C4604ZjmRx9MB-MjS4KbA%402x.png)

*   **创建: *W* 中的**元素没有赋值给 *W* 中的元素，因此在版本 *n* + *i* 中创建。

[![](img/a38cff8c7922a570c7ec9746178940d5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VQI_D5T1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2Aj28mAjTQsT96aG4aAuKhBA%402x.png)

*   **已维护: *W* 中的**元素仍在 *W* 中，但可能已被修改。

[![](img/48c4ba9357e5074edd1d49bb45c4da56.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HILKYxqB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AJcNFxfjJcqhH9V0Pvqe1Gg%402x.png)

如果一个测试用例使用 set *D* 中的元素，那么底层脚本必须被修改，因为在新版本中缺少强制元素。来自 *C* 的元素也会影响测试用例；例如，这样的元素可能会改变给定的布局，在其他地方产生不同的 XPath。通常也由开发人员或测试人员来决定是否需要测试这些新元素。因此， *M* 的精确计算是 GUI 元素识别问题的主要目的。根据作者的说法，这“通常需要在缺乏模型的情况下使用启发式方法，其中每个元素都有程序员定义的唯一标识符。”

Selenium 提供了[各种定位器类型](http://www.seleniumhq.org/docs/02_selenium_ide.jsp#locating-elements)(例如 ID、name 或 XPath)，每种都有自己的优缺点。此外，更高级的测试自动化框架有时会应用复杂的算法，而不依赖于单个定位器。例如，[再测试](https://www.retest.de/)——我工作的公司——采用了一种被称为差异测试的新范式，在这种范式中，完整的用户界面状态被捕获。因此，可以同时使用所有可用的属性。另一个有趣的方法是由 [Testim](https://www.testim.io/) 完成的。他们在机器学习的帮助下结合历史数据，为每个元素单独排列定位器，这随着时间的推移稳定了测试。

虽然这些(和其他)技术导致了相当健壮的元素识别，但它们不是银弹…我怀疑当前的技术水平是否提供了银弹。但是它们将 GUI 元素识别问题的负担从人转移到了机器上——减少了开发人员和测试人员必须处理的`NoSuchElementException`(或者你选择的框架所称的任何东西)的数量。