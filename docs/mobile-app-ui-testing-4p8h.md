# 移动应用程序 UI 测试

> 原文：<https://dev.to/funkysi1701/mobile-app-ui-testing-4p8h>

自从我开始创建一个 android 应用程序以来，我一直在编写简单的 UI 测试。

我一直在利用 Visual Studio 应用中心，它允许你在**测试云**中针对数百种不同的设备进行测试。

[![Xamarin UI tests](img/81a32f8d61f99f44e1fa7217597e771f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--V0RvS4t1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/www.funkysi1701.com/wp-content/uploads/2018/01/01-newproject-vs.png%3Fresize%3D300%252C182%26ssl%3D1) 为了编写一个 UI 测试创建一个 UI 测试 App，这就利用了 nuget 包 Xamarin UI Test。默认情况下，你现在会有一个名为 pLaunches 的测试，它会在启动后对你的应用程序进行截图。

现在，您可以对 Visual Studio 中的任何设备运行这个测试，前提是您已经将它物理地插入到您的计算机中。但是，如何与测试云赛跑呢？

要针对测试云运行，您需要首先安装 node.js。

```
**npm** install -g appcenter-cli 
```

Enter fullscreen mode Exit fullscreen mode

要在测试云中运行测试，运行下面的命令

```
**appcenter** test run uitest --app "<username>/<appname>" --devices "<username>/<deviceset>" --app-path _pathToFile.apk_ --test-series "master" --locale "en\_US" --build-dir _pathToUITestBuildDir_ 
```

Enter fullscreen mode Exit fullscreen mode

其中是您的 appcenter 用户名，是您在 appcenter 中的应用的名称，是您在 app center 中创建的用于测试的应用组。

在 appcenter 的“测试”部分查看设备集，然后单击“新建设备集”按钮。然后，您可以搜索任何您喜欢的设备，并将其添加到一个集合中，当我写这篇文章时，有 244 个设备可供您测试。

目前，在 app center 中无法针对新版本运行测试，但是，如果您也在 VSTS 构建应用程序，则可以创建运行它的版本或发布步骤。由于 Visual Studio 应用程序中心仍在开发中，如果在某个时候添加它，我不会感到惊讶。

在 VSTS，在定义中查找移动中心测试，您可以指定与上面命令行中指定的相同的变量。

现在，如何编写一个有用的 UI 测试呢？我上面提到你得到一个默认测试，它包含以下代码，这是你的应用程序的截图。然而，当使用测试云时，你不必包括这个，因为截图是免费的。

```
app.Screenshot("First screen."); 
```

Enter fullscreen mode Exit fullscreen mode

让我们看看 app
中还包括什么

```
app.Repl(); 
```

Enter fullscreen mode Exit fullscreen mode

这将启动一个交互式 REPL(读取-评估-打印-循环),让您探索应用程序屏幕上的内容并暂停执行。我没有在我的测试中包括这一点，但是我确实利用它来探索屏幕上有什么，以及我可能会利用哪些测试。

```
app.Tap(c => c.Marked("Button")); 
```

Enter fullscreen mode Exit fullscreen mode

点击屏幕上一个叫做按钮的元素。还有一种叫做 TapCoordinates 的方法，可以让你点击任何你想点击的地方。

```
app.WaitForElement(c => c.Marked("View")); 
```

Enter fullscreen mode Exit fullscreen mode

点击一个按钮后，你可能想等待应用程序加载额外的数据或一个新的屏幕。WaitForElement 等待一个元素出现在屏幕上。还有一些方法会等待一段时间，或者一直等到某个元素不再存在。

这些是我迄今为止使用的主要方法，然而还有一个广泛的列表，包括滚动，滑动，挤压和调节音量按钮的方法。因此，您应该能够测试所有方式的应用程序功能，如果您利用测试云，将知道哪些设备导致了问题。

关于手机应用 UI 测试的帖子最先出现在 [Funky Si 的科技谈话](https://www.funkysi1701.com)。