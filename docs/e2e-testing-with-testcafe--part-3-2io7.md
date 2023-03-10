# 用 TestCafe |多浏览器测试进行 E2E 测试

> 原文：<https://dev.to/chrisvasqm/e2e-testing-with-testcafe--part-3-2io7>

在第 2 部分中，我们学习了如何并行运行测试，但是我们只使用了 Chrome 浏览器。

现在我们将在 Chrome 和 Firefox 上运行我们的测试。

# 要求

*   安装 Firefox 驱动程序(称为 *geckodriver* )。

# 安装火狐驱动

为了安装 geckodriver，我们需要在 CMD 或终端中执行以下命令:

```
npm install -g geckodriver 
```

Enter fullscreen mode Exit fullscreen mode

通过运行
来检查您的`geckodriver`版本

```
geckodriver --version 
```

Enter fullscreen mode Exit fullscreen mode

当我写这篇文章的时候，我的是`0.19.1`。

# 运行 Chrome 和 Firefox

首先，让我们用这个命令一次只运行一个 Chrome 实例和一个 Firefox 实例:

```
testcafe chrome,firefox tests/devto.js 
```

Enter fullscreen mode Exit fullscreen mode

> *确保**而不是**在上面命令中分隔`chrome`和`firefox`的逗号后添加任何空格。*

这将启动您的两个浏览器，并分别在每个浏览器中单独运行我们的两个测试。

太好了！

这将允许我们验证我们的应用程序在多种浏览器中正常工作，而不必为我们想要尝试的每个浏览器运行一个命令。

超过 2 个浏览器怎么办？你可以这样做，只要你在你的机器上安装了所需的驱动程序和浏览器本身(比如分别是 macOS/Windows 专用的 Safari/Internet Explorer ),你就可以开始了。

## 让我们更进一步

如果你遵循了[第二部分](https://dev.to/chrisvasqm/e2e-testing-with-testcafe--part-2-1lfg)中的说明，你可能会记得我们可以添加到`testcafe`中的`-c #`命令，这样它就可以运行同一浏览器的多个窗口，在它们之间分割工作。

好了，现在让我们用 Chrome 和 Firefox 来做吧！

既然我们现在已经安装了`geckodriver`，我们可以继续使用:

```
testcafe -c 2 chrome,firefox tests/devto.js 
```

Enter fullscreen mode Exit fullscreen mode

这将打开 2 个 Chrome 窗口和 2 个 Firefox 窗口。

两种浏览器上的所有测试都通过了吗？

### 牛逼\o/

现在我想你已经开始掌握如何使用一些真正有用的命令了。

这些是您在设置您的 CI 或持续集成系统(如 Jenkins、CircleCI 等)时将使用的，以便每隔 X 时间自动运行这些测试。

在第 4 部分中，我们将重构我们的项目，以支持页面对象模型设计模式，这将帮助我们清理代码，使其在所有测试中更具表现力和可重用性。

[![chrisvasqm](img/e4a5dffbeebdbe975aabe7903c567401.png)](/chrisvasqm) [## 用 TestCafe |重构进行 E2E 测试

### 克里斯蒂安瓦斯奎兹 1 月 16 日 183 分钟阅读

#testing #e2e #testcafe #javascript](/chrisvasqm/e2e-testing-with-testcafe--part-4-1glp)