# 使用 watch 工具加速 dotnet 核心应用程序的开发

> 原文：<https://dev.to/rafalpienkowski/speed-up-development-of-dotnet-core-applications-with-watch-tool-bk8>

首先，我想介绍一下我创建这个例子的动机。我的朋友从 PHP 转到了。Net 堆栈，他们抱怨说，每次他们在源代码中做了一点点改变，就必须重新编译应用程序。他们说:

> “在 PHP 中，我们更改了一个脚本文件，重新加载一个页面，我就可以看到我的更改。在 C#中，我们需要重新编译整个应用程序，这很烦人……”

对于我来说，我自然需要重新编译 ma 应用程序。这是高性能的代价。意外地，我从托马兹·科帕奇那里听说了 T2 的网络手表，我的“小”世界已经改变了。现在，我想与其他开发人员分享关于该工具的信息。

[该存储库](https://github.com/rafalpienkowski/dotnet-watch-example)包含 ASP.Net 核心 2.0 应用程序以及在开发阶段使用的*点网观察*工具。我希望这个工具能提高你的工作效率。

### 设置&运行

* * *

设置 *dotnet watch* 工具超级简单。我们需要添加参考*微软。*中的 Dotnet.Watcher.Tools* 。csproj 文件:

```
<ItemGroup>
    <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
</ItemGroup> 
```

Enter fullscreen mode Exit fullscreen mode

之后，我们需要恢复项目中的包，瞧！现在我们可以使用*点网手表*工具，如:

```
dotnet restore

dotnet watch run

# example output
watch : Started
Hosting environment: Production
Content root path: (...)\dotnet-watch-example\ExampleMvcApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down. 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以在解决方案文件中进行更改。每一个改变都会触发 *dotnet watch* 工具并重建我们的应用:

```
watch : Exited with error code 1
watch : File changed: (...)\dotnet-watch-example\ExampleMvcApp\Startup.cs
watch : Started
Hosting environment: Production
Content root path: (...)\dotnet-watch-example\ExampleMvcApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down. 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已。现在你可以自己玩我的例子了，但是记住每一个变化都会触发一个构建。这会导致我们的应用程序停机。请记住，代码中的每个错误都会破坏应用程序，应用程序将被关闭，直到您修复代码中的错误。下面的例子:

```
watch : Exited with error code 1
watch : File changed: (...)\dotnet-watch-example\ExampleMvcApp\Startup.cs
watch : Started
Startup.cs(43,18): error CS1001: Identifier expected [(...)\dotnet-watch-example\ExampleMvcApp\ExampleMvcApp.csproj]
Startup.cs(43,18): error CS1002: ; expected [(...)\dotnet-watch-example\ExampleMvcApp\ExampleMvcApp.csproj]

The build failed. Please fix the build errors and run again.
watch : Exited with error code 1
watch : Waiting for a file to change before restarting dotnet... 
```

Enter fullscreen mode Exit fullscreen mode

### 测试

* * *

如果代码有任何变化，Dotnet watch 也可用于触发应用程序测试。为此，我们只需要用*手表*工具进行设置测试。执行的例子:

```
dotnet watch test watch : Started
Build started, please wait...
Build completed.

Test run for (...)\dotnet-watch-example\ExampleMvcApp.Tests\bin\Debug\netcoreapp2.0\ExampleMvcApp.Tests.dll(.NETCoreApp,Version=v2.0)
Microsoft (R) Test Execution Command Line Tool Version 15.3.0-preview-20170628-02
Copyright (c) Microsoft Corporation.  All rights reserved.

Starting test execution, please wait...
[xUnit.net 00:00:01.9710205]   Discovering: ExampleMvcApp.Tests
[xUnit.net 00:00:02.0615903]   Discovered:  ExampleMvcApp.Tests
[xUnit.net 00:00:02.1304412]   Starting:    ExampleMvcApp.Tests
[xUnit.net 00:00:02.3174366]     ExampleMvcApp.Tests.SimpleTest.SimpleTest_WithoutConditions_ShouldPass [FAIL]
[xUnit.net 00:00:02.3186889]       Assert.True() Failure
[xUnit.net 00:00:02.3188818]       Expected: True
[xUnit.net 00:00:02.3189584]       Actual:   False
[xUnit.net 00:00:02.3199073]       Stack Trace:
[xUnit.net 00:00:02.3212204]         (...)\dotnet-watch-example\ExampleMvcApp.Tests\SimpleTest.cs(11,0): at ExampleMvcApp.Tests.SimpleTest.SimpleTest_WithoutConditions_ShouldPass()
[xUnit.net 00:00:02.3342243]   Finished:    ExampleMvcApp.Tests
Failed   ExampleMvcApp.Tests.SimpleTest.SimpleTest_WithoutConditions_ShouldPass
Error Message:
 Assert.True() Failure
Expected: True
Actual:   False
Stack Trace:
   at ExampleMvcApp.Tests.SimpleTest.SimpleTest_WithoutConditions_ShouldPass() in (...)\dotnet-watch-example\ExampleMvcApp.Tests\SimpleTest.cs:line 11

Total tests: 1\. Passed: 0\. Failed: 1\. Skipped: 0.
Test Run Failed.
Test execution time: 3,1153 Seconds
watch : Exited with error code 1
watch : Waiting for a file to change before restarting dotnet...

watch : Started
Build started, please wait...
Build completed.

Test run for (...)\dotnet-watch-example\ExampleMvcApp.Tests\bin\Debug\netcoreapp2.0\ExampleMvcApp.Tests.dll(.NETCoreApp,Version=v2.0)
Microsoft (R) Test Execution Command Line Tool Version 15.3.0-preview-20170628-02
Copyright (c) Microsoft Corporation.  All rights reserved.

Starting test execution, please wait...
[xUnit.net 00:00:01.9361049]   Discovering: ExampleMvcApp.Tests
[xUnit.net 00:00:02.0139917]   Discovered:  ExampleMvcApp.Tests
[xUnit.net 00:00:02.0731938]   Starting:    ExampleMvcApp.Tests
[xUnit.net 00:00:02.2357251]   Finished:    ExampleMvcApp.Tests

Total tests: 1\. Passed: 1\. Failed: 0\. Skipped: 0.
Test Run Successful.
Test execution time: 2,9806 Seconds
watch : Exited
watch : Waiting for a file to change before restarting dotnet... 
```

Enter fullscreen mode Exit fullscreen mode

### 提示

* * *

我们可以扩展 *dotnet watch* 工具来触发重建&重启我们的应用程序，而不仅仅是在*发生变化之后。cs 文件，但也存在于其他类型的文件中。在我看来，appsettings 上的文件屏蔽非常有用。这个文件的每一个变化都会触发*dotnet watch* 。重启我们的应用程序所需的时间将比*更改后更短。因为我们不需要重新构建解决方案。下面 appsettings 文件的文件掩码示例:

```
<ItemGroup>
    <!-- extends watching group to include .\appsettings*.json files -->
    <Watch Include=".\appsettings*.json" />
  </ItemGroup> 
```

Enter fullscreen mode Exit fullscreen mode

### 总结

* * *

总而言之，网络手表是一个非常有用的工具，可以提高你的工作效率。在我看来，当你开发一个应用程序或者快速检查某个东西，而我们不想运行 Visual Studio 的时候，这将是特别强大的。

我希望 dotnet watch 工具能提高你的工作效率，就像我的情况一样。

### 有用的链接

* * *

*   [。NET 命令行工具 repo](https://github.com/aspnet/DotNetTools)
*   [使用 dotnet watch 开发 ASP.NET 核心应用文章](https://docs.microsoft.com/en-us/aspnet/core/tutorials/dotnet-watch)