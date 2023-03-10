# 从 Visual Studio IDE 启动的 PowerShell 控制台中忽略 Ctrl + C。

> 原文：<https://dev.to/j_sakamoto/ctrl--c-is-ignored-in-the-powershell-console-launched-from-visual-studio-ide-1078>

# 一天...

我是一名开发人员，平时在 Windows OS 上使用 Visual Studio IDE。

最近我在 Visual Studio IDE 的【工具】>【外部工具】菜单注册了启动 PowerShell 控制台的命令，键盘快捷键为:Ctrl+P，Ctrl+S。

因此，我可以快速打开 PowerShell 控制台，只需输入两个击键，工作目录就在解决方案文件夹中。

它对我来说非常好和有用。

但是，有一天，我发现了大问题...

我发现从 Visual Studio IDE 外部工具菜单启动的 PowerShell 控制台中总是忽略 **Ctrl + C。**

例如，其中一个场景是这样的:

1.  我用 Visual Studio IDE 中的 Ctrl+P、Ctrl+S 组合键打开 PowerShell 控制台。
2.  我执行一些长时间运行的控制台应用程序，如“科尔多瓦运行浏览器”。
3.  之后，当我想停止正在运行的控制台应用程序(在这种情况下，“cordova”命令)，我通常按 Ctrl + C。

但是不管用，从来没有。没有什么事情会发生。
Ctrl + C 只是忽略。

在 PowerShell 控制台中，从 Visual Studio IDE 打开**而不是**，例如从开始菜单或 Windows 资源管理器菜单或从“运行”框等。，Ctrl + C 运行得非常好。
按 Ctrl + C 键可以终止正在运行的命令。

只有 Visual Studio IDE 的子进程 PowerShell 有问题。

# 发生什么事了？

经过长时间的互联网搜索，我找到了 SetConsoleCtrlHandler Win32 API。

此 Win32 API 允许 Win32 进程自定义按下 Ctrl + C 键的行为。

而且，这是很重要的一点，文档说**改变这个行为的效果会继承到子进程！**

我猜，Visual Studio IDE(或其中的任何扩展)调用 SetConsoleCtrlHandler Win32 API 来忽略 Ctrl + C 键按下的默认行为。

因此，我认为，作为 Visual Studio IDE 子进程的 PowerShell 控制台也忽略了 Ctrl + C。

# 如何解决这个问题？

思考了一会儿后，我尝试通过调用 PowerShell 控制台进程内的 SetConsoleCtrlHandler Win32 API 来解决这个问题。

我们可以用下面的代码将 Ctrl + C 的行为重置为系统默认值。

```
// C/C++ syntax with Win32 SDK
SetConsoleCtrlHandler(NULL, FALSE); 
```

Enter fullscreen mode Exit fullscreen mode

对了，PowerShell 不能直接调用 Win32 API。

相反，我用 C#编写了一个调用 SetConsoleCtrlHandler Win32 API 的包装类库，并将其构建为。NET 程序集文件(。dll)。

PowerShell 可以加载。NET 程序集并调用其中的托管代码。

由于这种机制，PowerShell 可以通过。我写的. NET 程序集。

我在有问题的 PowerShell 控制台上运行了以下命令。

```
Add-Type  -Path  Path/To/MyWrapper.dll  [MyWrapperClass]::MyWrpperStaticMethodToEnableCtrlC() 
```

Enter fullscreen mode Exit fullscreen mode

执行上述命令后，执行一些长期运行的控制台应用程序，如“科尔多瓦运行浏览器”，并按 Ctrl + C，然后...

它可以终止控制台应用程序预期！

我做到了。

# 结论

我把这些作品发布到 GitHub 上，用包装类库汇编文件(。dll)。

[GitHub-Ctrl+C PowerShell 启用程序(https://github.com/jsakamoto/CtrlCEnabler)](https://github.com/jsakamoto/CtrlCEnabler)

您可以将启用 Ctrl + C 默认行为的 PowerShell 代码写入$PROFILE 文件，以便在任何时候应用启用 Ctrl + C。

# 还有一件事...

Visual Studio IDE 已经有很大的扩展来启动 PowerShell 控制台: **[【打开命令行】](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.OpenCommandLine)** 作者 Mads Kristensen。

Ctrl + C 在 PowerShell 控制台中工作正常，该控制台从这个扩展启动。

如果你有从 Visual Studio IDE 快速打开 PowerShell 控制台的需求，一般情况下，我推荐安装这个扩展。

快乐编码:)