# 使用呼叫者信息进行详细日志记录

> 原文：<https://dev.to/adamkdean/using-caller-information-for-verbose-logging-50pd>

Visual Studio 2012 和 C# 5.0 中增加的一个很酷的功能是调用者信息属性。基本上，它允许你看到你的方法是从哪里被调用的，而不需要一个堆栈跟踪一个堆栈跟踪地筛选。

您只需给可选的参数加上以下三个属性之一的前缀:

`[CallerMemberName]`提供调用方的方法或属性名作为`string`。

`[CallerFilePath]`提供包含调用者的源文件的完整路径作为`string`。

`[CallerLineNumber]`提供方法被调用的源文件中的行号作为`int`。

举一反三。这里我做了一个简单的日志记录方法，我们向它传递一个字符串，编译器会填充剩下的内容。确保在你的`using`陈述中包含`System.Runtime.CompilerServices`。

```
static void VerboseLog(string value,
    [CallerMemberName] string callerMemberName = "",
    [CallerFilePath] string callerFilePath = "",
    [CallerLineNumber] int callerLineNumber = -1)
{
    int index = callerFilePath.LastIndexOf(Path.DirectorySeparatorChar);
    string localPath = callerFilePath.Substring(index + 1);
    Debug.WriteLine("{0} {1}:{2} {3}",
        localPath, callerMemberName, callerLineNumber, value);
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们用一个简单的控制台应用程序来测试它。请注意，在上面的方法中，我删除了完整的路径，以使其更具可读性。不一定需要，但仍然令人愉快。

```
static void Main(string[] args)
{
    VerboseLog("this is a test");
    DoSomething();
}

static void DoSomething()
{
    for (int i = 0; i < 5; i++)
    {
        VerboseLog(string.Format("something {0}", i));
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这将输出:

```
Program.cs Main:16 this is a test
Program.cs DoSomething:24 something 0
Program.cs DoSomething:24 something 1
Program.cs DoSomething:24 something 2
Program.cs DoSomething:24 something 3
Program.cs DoSomething:24 something 4 
```

Enter fullscreen mode Exit fullscreen mode

非常简单，但是非常非常有用。