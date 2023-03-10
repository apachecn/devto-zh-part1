# 使用 Google Chrome 调优您的 Web 应用程序，第一部分

> 原文：<https://dev.to/skamansam/using-google-chrome-to-tune-your-web-app-part-i-45ii>

#### 开发工具审计简介

<figure>[![](img/74f4429e73c732940dfbf132fba9eb91.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Am6eADOa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ATb91bmXUYR5OQDrAcqZsJg.jpeg) 

<figcaption>照片由[米切尔引导](https://unsplash.com/photos/hOf9BaYUN88?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上[下](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

在这个系列中，我将试图通过揭开 Chrome 60 中引入的新审计面板的神秘面纱来帮助新的 web 开发人员。请喝杯茶，和我一起去参加绩效审计吧！

### 什么是绩效审计？

我在网上找不到关于*绩效审计*，甚至*审计*的可接受定义，所以我必须在这里创建自己的定义。

> **性能审计**:名词:对一个事物或事物的一部分的适当性、功能性、可访问性和行为的评估，通常但不限于软件和/或其组件，以及这些度量如何影响软件整体的感知或实际速度。

让我们为外行人把它分解成几个部分。

1.  **适当性** —是否适合软件的上下文。这是一个非常有偏见的度量，应该通过代码审查或用户测试手动完成。
2.  **功能** —应用程序是否以其预期的方式运行。该指标还包括它是否有任何副作用或不需要的功能。例如，用户可以删除整个数据库吗？该指标可以通过自动化测试套件和手动测试。这有点偏颇，但确实应该用来防范不必要的破坏性行为。
3.  **可访问性** —是否所有身体类型的人都可以访问您的内容。色盲可以看到你的网站并很好地使用它吗？听力或视力受损者可以使用您的网站吗？
4.  **行为** —网站的组织和设计方式；它是如何工作的，用户是如何与之交互的。网站运行良好吗？用户能相对快速地在你的网站上执行任务吗？他们会四处寻找菜单和动作吗？

### DevTools 迎来新成员！

谷歌的网络浏览器 Chrome 今年 60 岁了(是版本，不是年份)！随着这次更新，[谷歌将 Lighthouse 直接添加到其免费浏览器](https://developers.google.com/web/updates/2017/05/devtools-release-notes#lighthouse)中！您可以使用`Shift+Ctrl+i`通过 DevTools 的 Audits 选项卡访问这个神奇的审计工具。现在就试试吧！

Lighthouse 是一个审计你的网站或应用程序的绝佳工具。它对扫描和发现的问题有(相对)清晰的描述，以及如何修复这些问题区域的链接。目前有 4 个主要领域已经过检查和评分，我们将详细介绍。然而，在我们开始之前，我们需要先弄清楚几件事。

### 我需要审计吗？

这是你在继续之前需要回答的最基本的问题。这个问题的答案几乎总是“是”*差不多*。如果你正在为当地的钩针俱乐部或高中乐队做一个项目，你真的不应该担心这个。在这种情况下，性能调优可能需要更多的工作。然而，如果你正在设计一个高流量的网站或一个具有复杂的后端或前端的 web 应用程序，你肯定应该关注你的前端性能，即使只是为了消除它作为缓慢响应的问题来源。

### TL；博士:最快的快速开始

如果你不想再阅读，或者想直接进入，这里是 Chrome 新审计工具的最快运行方式。

打开审计面板并单击按钮。接下来，从给定列表中选择所需的审计，并点击*运行审计*按钮。几分钟后，测试将完成，您将获得所有已执行检查的概要。未通过检查的名字右边会有一个红色的 *X* 。要修复此测试并了解更多信息，请单击测试名称左侧的展开箭头，并阅读描述。然后点击*了解更多信息*链接，阅读更多关于这项检查的信息以及它的重要性。跟随这些页面上的每一个链接，你会明白为了让你的网站更有性能，你需要做些什么。

### 5000 英尺概述

现在，您已经对如何使用新的审计面板有了基本的了解，我将开始本指南的手把手部分。

打开审计面板后，您将看到一个按钮，允许您通过选择要执行的审计来启动审计。这些审核将在结果窗格中排名靠前。运行审计需要一段时间，您的网页可能会做一些奇怪的事情，但是让它运行吧。

当审计完成时，审计面板将填充一堆数据。在这个面板的最顶端，您将找到您选择的各种审计的分数，从 0 到 100。之后，每个审计被分解成该审计的各个部分。要了解它们是什么，请单击展开箭头，将会显示更多信息。其中大部分都有一个*了解更多*的链接，它会带你到关于那个特定测试的各种文档。该文档通常会有更多文章和文档的链接，这些文章和文档是关于如何修复导致测试失败的任何问题的。你应该阅读所有这些，但我在这里帮助你消化这个系列。

### 什么最重要

在当今高速互联网和即时访问的世界里，保持速度是非常重要的，即使你的应用程序没有看起来那么快。这个标准通常被称为“第一个绘画”最好的方法是尽可能快地加载对你的应用程序至关重要的东西。我再怎么强调这一点也不为过——永远不要使用加载屏幕！这可能会愚弄一些审计工具，但这从来都不是一个好主意(你不能愚弄你的用户！).如果应用程序的某些部分需要一段时间才能加载，请单独加载这些组件。例如，如果你有很多数据需要加载到一个列表中，那么就分别加载列表中的每一项，然后只加载你的用户当时可以看到的内容。如果用户然后滚动，加载更多的项目。这种技术通常被称为“无限滚动”，可以使用[交叉点观察器 API](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API) 在现代浏览器上实现。稍后会有更多的介绍。

为了显得快速，你的网站需要加载一个用户可以很快看到的 UI。如果你想让你的应用更快，你需要关注一个不同的指标，用户能够真正与你的应用互动。这个指标被简单地称为“互动优先”您可以使用指标(Google Analytics 可以做到这一点)来确定用户访问您的网站时最常做的事情。一旦确定了这一点，就应该关注这个动作或组件的加载时间。

这两个想法都存在于渐进式 Web 应用程序的想法中。在这个上下文中，渐进式是指网页的加载。根据需要逐步加载资源，而不是一次性加载。这通常意味着你应该主要使用 web 组件、普通的 JS 和解耦的 CSS。您还应该减少这些资源以加快加载时间，因为它们往往会在加载之前阻止页面的呈现。我将在本系列的后续文章中更详细地介绍渲染阻塞的含义。

### 更多阅读

我希望你现在对如何使用 Google Chrome 新的审计面板来提高你的网站性能有了基本的了解。这最多只是一个粗略的概述，所以请阅读本系列的其他文章，了解更多关于 web 应用程序性能调优的细节和注意事项！

1.  本系列的第二部分，剖析渐进式 Web 应用程序(即将推出！)
2.  本系列的第三部分，剖析 Web 应用程序性能(即将推出！)
3.  本系列的第四部分，剖析 Web 可访问性(即将推出！)
4.  本系列的第五部分，Web 应用程序最佳实践(即将推出！)

### 仍更阅读

网上还有很多其他的很棒的资源，包括[Mozilla Developer Network documentation for web standards](https://developer.mozilla.org/en-US/docs/Web)，这可能是关于 Web 的最棒的资源。以下是我的其他几个最爱。

1.  [灯塔文档](https://developers.google.com/web/tools/lighthouse/)
2.  [谷歌开发者网络基础](https://developers.google.com/web/fundamentals/)
3.  [谷歌 Chrome 开发工具文档](https://developers.google.com/web/tools/chrome-devtools/)
4.  [完全工具提示:灯塔](https://www.youtube.com/watch?v=d98CAgQNeaM) (youtube 视频)

更多即将推出…