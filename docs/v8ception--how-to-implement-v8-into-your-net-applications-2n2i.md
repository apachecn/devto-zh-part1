# V8Ception |如何将 V8 实现到您的。NET 应用程序。

> 原文：<https://dev.to/jochemstoel/v8ception--how-to-implement-v8-into-your-net-applications-2n2i>

*本文基于我在 2015 年 8 月 23 日发表的旧 V8 文章。*

V8 是为谷歌 Chrome 打造的 JavaScript 执行引擎，由谷歌于 2008 年开源。V8 用 C++编写，将 JavaScript 源代码编译成本机代码，而不是实时解释。

在这篇文章中，我将解释如何

*   从 NuGet 安装所需的依赖项
*   在 C#中创建 V8 脚本引擎的实例。NET 应用程序。
*   评估 JavaScript 输入
*   向 V8 解释器上下文公开主机类型
*   将主机对象暴露给 V8 解释器上下文
*   向脚本上下文公开整个程序集
*   创建一个主机对象并从脚本中调用方法

举例说明如何

*   写一个超级简单的 REPL 应用程序
*   创建一种加载 JavaScript 文件的简单方法
*   让 V8 引用自身(类型和实例)

[![Google V8 Engine](img/f3a487e2aaa2b4aa05f0a736d5056877.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--peSHiwGq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xg2j2tvjv95ighvvh6dl.png)

> 这篇文章是为 00b 而写的，但是假设你对你正在做的事情有一些基本的概念。我已经尽我所能简化了本文中介绍的概念，并且在我的 GitHub gists 上提供了所有的代码片段。

### 项目

启动 Microsoft Visual Studio 并创建一个新的 C#应用程序。(控制台或 Winforms 无关紧要)右键单击项目引用，然后单击“管理 nuGet 包”。启用/安装 Clearscript。V8 然后关闭窗口。

[![Add NuGet package](img/6c08481f703d4d2c0dfe2d1ece3db9bc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KKjvb75t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r1012l2bmgyya5l4dq0a.png)

确保选择正确的包(ClearScript。V8)。

[![Microsoft.ClearScript.V8](img/d5cde650ce84b386117f0ae531bcc745.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--K7OHX-M_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ynihvcb1miuaa1vy4egr.png)

ClearScript 是一个库，可以很容易地将脚本添加到您的。NET 应用程序。它目前支持 JavaScript(通过 V8 和 JScript)和 VBScript。基本上，ClearScript 将控制台、文件甚至 Winforms 组件等应用程序对象分配给 Javascript 对象。您可以将*对象实例*和*对象类型*分配给脚本*上下文*。

添加对 Microsoft 的引用。Clearscript.v8

```
using Microsoft.ClearScript.V8 
```

Enter fullscreen mode Exit fullscreen mode

你可以像这样创建一个新的 V8 实例

```
V8ScriptEngine v8 = new V8ScriptEngine(); 
```

Enter fullscreen mode Exit fullscreen mode

添加一个*对象实例*允许你从你的脚本控制(一个已经创建的)对象实例。如果您分配系统控制台(系统。控制台)到*我的控制台*，你将能够像这样访问控制台

```
myConsole.ReadKey(); /* Read single key input from user */
myConsole.WriteLine(); /* Write something to the console */ 
```

Enter fullscreen mode Exit fullscreen mode

所以。如果你的 Winforms 应用程序有一个按钮，比如说 *Button1* ，然后你把它分配给 V8 上下文，就像这样

```
v8.AddHostObject("btn1", Button1); // variable btn1 reflects Button1 
```

Enter fullscreen mode Exit fullscreen mode

然后在你的脚本中，你可以简单地通过
来改变按钮的值

```
btn1.Text = "exit now"; /* The button text is now changed to "exit now" */ 
```

Enter fullscreen mode Exit fullscreen mode

对象类型(显然)是对应用程序对象类型(换句话说，就是类)的引用，而不是它的实例。对象类型尚未实例化。

```
/* Inside your application */
v8.AddHostType("Button", typeof(Button)); 
```

Enter fullscreen mode Exit fullscreen mode

和

```
/* Inside your script you create multiple instances of Button */
var button1 = new Button();
var button2 = new Button();
var button3 = new Button(); 
```

Enter fullscreen mode Exit fullscreen mode

如果主机对象和对象类型之间的区别现在还不清楚，那么你还没有准备好在你的应用中使用 v8。

ClearScript 包还允许您一次公开整个名称空间。 *HostTypeCollection* 构造函数接受一个或多个名称空间(字符串)参数。HostTypeCollection 位于*微软。ClearScript* 所以除了*微软。ClearScript.V8* 你还需要参考*微软。ClearScript* 。如果您想要导入/访问许多不同的内容，而您不想手动添加，但也可以在动态/编程加载时使用，这将非常有用。DLL 文件。

```
v8.AddHostObject(identifier, new HostTypeCollection(namespaces[])); 
```

Enter fullscreen mode Exit fullscreen mode

```
/* expose entire assemblies */
    engine.AddHostObject("lib", new HostTypeCollection("mscorlib", "System.Core"));
    engine.Execute("console.log(lib.System.DateTime.Now)");
// of course assuming console.log is already implemented 
```

Enter fullscreen mode Exit fullscreen mode

### 例 1 |一个使用 V8 的简单 REPL

与 Node 类似，一个超级简单的 REPL 从 process stdin 读取输入并对其求值。

```
using System;
using Microsoft.ClearScript.V8;

namespace v8repl
{
    class Program
    {
        static void Main(string[] args)
        {
            /* create instance of V8 */
            V8ScriptEngine v8 = new V8ScriptEngine();
            /* assign System.Console to Javascript variable myConsole */
            v8.AddHostType("myConsole", typeof(Console));

            /* */
            bool kill = false; 

            /* keep doing the following while kill = false */
            while(!kill)
            {
                /* get input string from process stdin */
                string input = Console.ReadLine();

                /* using a string literal for simplicity sake */
                if(input == "exit")
                {
                    Environment.Exit(0); /* exit code 0 means no error */
                }

                /* safely evaluate input in a try/catch block */
                try
                {
                    v8.Evaluate(input); /* run the code */
                } catch (Exception e)
                {
                    /* something went wrong, show us the exception */
                    Console.WriteLine(e.Message);
                }
            }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 要旨

*   [超级简单的 REPL](https://gist.github.com/jochemstoel/99784b307d275be41316092b55bf112b)

### 例 2 | REPL 的‘包装器’类/加载文件

这个简单的类包装了 V8 并添加了一个从磁盘加载文件的方法。这不是设计 REPL 的最终方法，但对于本例来说很好。

```
using System;
using Microsoft.ClearScript;
using Microsoft.ClearScript.V8;

namespace v8repl
{
    class REPL
    {
        /* v8 engine outside main loop */
        private V8ScriptEngine v8 = new V8ScriptEngine();
        private bool running = false; /* */

        /* keep reading input from stdin until running = false */
        public void Start()
        {
            running = true;
            while (running)
            {
                string line = Console.ReadLine();

                if (line.Equals("kill"))
                {
                    running = false; /* causes this while loop to stop */
                }
                else {
                    Run(line);
                }
            }
        }

        /* method to read and evaluate JavaScript file */
        public void LoadFile(string inputFile)
        {
            v8.Evaluate(
                System.IO.File.ReadAllText(inputFile)
            );
        }

        /* safely evaluate code like we did before */
        public void Run(string line)
        {
            try
            {
                v8.Evaluate(line);
            }
            catch (System.Exception e)
            {
                Console.Error.WriteLine(e.Message);
            }
        }

        /* this allows us to get current instance */
        public V8ScriptEngine GetInstance()
        {
            return v8;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 要旨

*   [REPL 班](https://gist.github.com/jochemstoel/d3f878fa20bbe2bbc5ee9d32aca9d263)

### 例 3 |初始化脚本

使用上面的 REPL 类，我们加载这个文件 *init.js* ，它包含一个简单的控制台对象，有点像标准化的 JavaScript 控制台对象。

##### 申请

```
using System;
using Microsoft.ClearScript.V8;
using Microsoft.ClearScript;

namespace v8repl
{
    class Program
    {
        static void Main(string[] args)
        {
            var repl = new REPL();
            var v8 = repl.GetInstance(); // shortcut 

            /* assign the whole .NET core library to mscorlib */
            v8.AddHostObject("mscorlib", new HostTypeCollection("mscorlib"));

            /* reference full namespace, for example:
             * mscorlib.System.Console.WriteLine()
             * mscorlib.System.IO.File.WriteAllText()
             */

            /* expose the V8ScriptEngine type to the V8 context */
            v8.AddHostType("V8ScriptEngine", typeof(V8ScriptEngine));

            /* we can now do:
             * var context1 = new V8ScriptEngine()
             */

            repl.LoadFile("init.js"); /* evaluate our file init.js */
            repl.Start();
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

##### 正在加载的 JavaScript 文件 init.js

```
/* imitate standarized console object */
var console = {
    log: string => {
        mscorlib.System.Console.WriteLine(string)
    },
    error: string => {
        mscorlib.System.Console.Error.WriteLine(string)
    }
}

/* 
Mental note:

In JavaScript we can pass multiple variables to console.log doing console.log('string', someVariable, 1234, { a: 1 })
This will not work here because Console.WriteLine expects a string.
You need some logic that will Array.join() the function arguments.
*/ 
```

Enter fullscreen mode Exit fullscreen mode

#### 要旨

*   [Program.cs](https://gist.github.com/jochemstoel/ead8d90613c7d51aeaeddce5902e18ca)
*   [Init.js](https://gist.github.com/jochemstoel/e0a1674725dcba96a2b7e932c49c23b0)

### V8 exception | V8 内部 V8

您甚至可以将 V8ScriptEngine 本身分配给一个 Javascript 对象。

```
v8.AddHostType("V8Engine", typeof(V8ScriptEngine)); 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以从应用程序输入脚本(JavaScript)创建一个新的 V8 实例。这允许您创建新的(子)上下文。新的子上下文意味着没有任何继承变量的新的范围/执行环境。

```
/* Javascript */
var v8 = new V8Engine();
v8.Execute('var x = 3; var y = 5; var z = x+y;'); 
```

Enter fullscreen mode Exit fullscreen mode

注意，如果您想将 V8 实例的值打印到您的进程 stdout，这个 V8 实例也需要它自己的控制台。

### 自我引用

您可以创建对 V8 实例本身的引用。无论在什么情况下，这都是一个糟糕的主意。:P

```
V8ScriptEngine v8 = new V8ScriptEngine();
v8.AddHostObject("self", v8); 
```

Enter fullscreen mode Exit fullscreen mode

```
self.Evaluate("var name = 'Jochem'")
console.log(name) // this will work 
```

Enter fullscreen mode Exit fullscreen mode

```
var myConsole = { 
   log: function() {
      // 
   }
}

self.AddHostObject('parentConsole', myConsole) // sketchy but works 
```

Enter fullscreen mode Exit fullscreen mode

### 还有一件事

理解 V8 只是解释器是很重要的。你知道的很多标准的和非常常见的对象/API*还不存在*。这包括我们在本文中讨论过的*控制台*对象，也包括*事件*类及其子类。

* * *

To learn more about ClearScript, I highly recommend checking out the [official documentation](https://clearscript.codeplex.com/) on CodePlex, this is where I started too.

[![V8Ception](img/ab07da6efea3ba2793dd8c0cea96debb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JgOaV1zX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0mt7aenqb62ouhoc96lt.png)