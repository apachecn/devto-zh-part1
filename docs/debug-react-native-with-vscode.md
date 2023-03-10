# 使用 VSCode 调试 React-Native

> 原文：<https://dev.to/kayis/debug-react-native-with-vscode>

许多 JavaScript 开发人员习惯于在调试软件时简单地将内容记录到控制台中。这还不错。遗憾的是，React-Native 上的控制台功能似乎没有最先进的浏览器那么复杂。通常，大的输出被简单地吞下，或者你简单地得到心爱的`[object Object]`，它也没有太多的信息提供。有时候你甚至不能用`JSON.stringify()`来绕过这个。

但是正如你所料，有一个更好的方法，一个*调试器*。

## 什么

简单地说，调试器是一个程序，它让你在你的源代码行放置*断点*，并观察特定变量的值。虽然我感觉日志似乎仍然是大多数 JS 开发人员的必经之路，但大多数浏览器和 Node.js 都为您提供了调试界面或完全集成的图形界面。所以不应该是一个外来的概念。

## 为什么

在我看来，当使用你编写代码的工具时，调试带来了最大的乐趣。对我来说，这是当前的 [VSCode](https://code.visualstudio.com/) 。

VSCode 附带了一个巨大的[扩展生态系统](https://marketplace.visualstudio.com/VSCode)，其中一个恰好是专门针对 react-native 的[，甚至是由微软自己创建的，可能是因为](https://marketplace.visualstudio.com/items?itemName=vsmobile.vscode-react-native)[他们也用 RN](https://microsoft.github.io/reactxp/) 构建东西。

虽然它有一大堆特性，但我最关心的是一个很好的调试器集成。

## 如何

我希望您已经安装了 VSCode 和 React-Native CLI。

首先，您需要通过左侧菜单上的*扩展*图标将扩展安装到您的 VSCode 中。这个扩展叫做 *React 原生工具*。

然后你需要创建(或更新)你的`.babelrc`。这些是必须包含的基本设置:

```
{
  "presets": [ "react-native" ],
  "sourceMaps": true
} 
```

之后，您需要创建一个调试环境，方法是点击左侧菜单上的*调试*图标，然后点击 gear。这将为您提供几个调试环境供您选择，在这里您选择 *React Native* 。

这将在一个`.vscode`目录中为您生成一个`launch.json`,当您启动一个调试会话时，其中有一些将被使用的配置。

在这之后，您只需要在代码中的某个地方设置一个断点，在 debug 视图中绿色 run 箭头旁边的选择框中选择一个配置，然后单击该箭头。您会注意到选择框中的名称对应于`launch.json`中的名称。

## 陷阱

虽然这看起来很简单，但我遇到了两个问题。

一个事实是，你需要通过 *React-Native Debug 菜单*在你的应用中明确*启用调试*，在 iOS 模拟器上它是用`cmd+d`打开的。否则，您的断点将是灰色的，调试器不会停止。

另一个是断点只在 React 的组件方法内部起作用。当我在一个`.js`文件/模块的顶层作用域中定义一个变量、类或函数的地方设置一个断点时，它不会被命中。我一时没有绕过这个问题。

## 结论

调试比日志记录好得多。它让您直接了解您的运行时代码，同时让您呆在您喜爱的编辑器中，它也不需要您编写额外的日志记录代码，以后您必须删除这些代码。