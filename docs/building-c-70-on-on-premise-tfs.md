# TFS 2015 内部 C# 7.0 大楼

> 原文：<https://dev.to/t4rzsan/building-c-70-on-on-premise-tfs>

*这篇文章最初出现在我的[个人博客](http://leruplund.dk/2017/07/22/building-c-7-0-on-on-premise-tfs/)上。*

在工作中，我们运行的是 premise Team Foundation Service 2015(是的，我知道，对吗？).

我们一直在努力在其上构建 C# 7.0 代码。

我们在构建机器上安装了 Visual Studio 2017，但似乎无论我们做什么，我们都只能在构建步骤中的“Visual Studio 2015”和“Visual Studio 15 预览”之间进行选择。正如你可能知道的，后者是 Visual Studio 2017 的预览版，它并没有为我们做好工作。

解决方案很简单，但有点尴尬的是，我们没有更快地发现。

原来你要做的就是用一个 MSBuild 步骤代替 af Visual Studio Build sted。在“Advanced”部分，您在构建机器上放置最新 msbuild.exe 的路径。

如果你把 Visual Studio 2017 或者更好的, [Visual Studio 2017 构建工具](https://www.visualstudio.com/downloads/#build-tools-for-visual-studio-2017)放在你的构建机器上，你应该能在那里的某个地方找到 msbuild.exe。

[![msbuild](img/f17363aecc67b9b7681654aede89825d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SGVQgp_l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://leruplund.dk/wp-content/uploads/2017/07/msbuild.png) 
(截图来自 https://www.visualstudio.com/但是你懂的)。

从 VS 版本切换到 MSBuild 可能无法正常工作。但是我们没有遇到任何问题。