# “使用系统。WPF 应用程序中的诊断、跟踪和 DbgView”

> 原文：<https://dev.to/kimkulling/use-systemdiagnosticstrace-and-dbgview-within-a-wpf-application>

[这篇博文最初发表在这里](http://kimkulling.de/2017/03/14/c-use-system-diagnostics-trace-and-dbgview-within-a-wpf-application/)

# 使用 C/C++和 Win32-API 进行跟踪

我最喜欢用 UI 调试 Win32/MFC 应用程序的工具之一是 Win32-call:

```
::OutputDebugString( "Test\n" ); 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用它在运行时跟踪应用程序中的调试信息。特别是当你不得不分析一些特定于运行时的错误，而你又没有任何类型的控制台/日志来执行 printf-debugging 这个 Win32-Call case 拯救你的屁股。您只需运行工具 DbgView 来监控这些日志条目(如果您想尝试一下，可以在这里下载 dbg view)。

# 用 C#跟踪 WPF

因为我目前在工作中使用 C#，所以我想将这个工具也用于 WVF 应用程序。当然，在。NET-framework 调用:

```
Trace.WriteLine( string msg ); 
```

Enter fullscreen mode Exit fullscreen mode

你可以在这里找到文档[。](https://msdn.microsoft.com/de-de/library/system.diagnostics.trace.writeline(v=vs.110).aspx)

它很容易使用。只需在代码中插入跟踪日志条目。当您想要查看您的应用程序时，您可以启动 DbgView 来查看正在进行的操作。请确保您已经定义了:

```
#define TRACE 
```

Enter fullscreen mode Exit fullscreen mode

在源文件的开头。这里有一个小例子:

```
#define TRACE 
using System.Diagnosics;

class Test {
    static int main() {
        Trace.WriteLine("Hello, world!" );
        Exit(0);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

要检查这是否工作正常，只需运行 DbgView 并运行您的应用程序。你应该可以看到你的帖子**你好，世界！**那里。