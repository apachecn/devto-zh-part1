# 在 haxe 中创建一个跨平台命令行工具

> 原文：<https://dev.to/khaled_garbaya/create-a-cross-platform-command-line-tool-in-haxe-3p1>

为了帮助你开始使用 haxe，我想到了创建一个简单的(Hello world)跨平台命令行工具，它真的很简单，而且花了我不到 30 分钟的时间。

所以你可能想要一个超级棒的命令行工具来帮助你自动化一些进程，生成文件等等…并且你想要它跨平台，Haxe 可以帮你搞定。

我选择构建针对[猫虚拟机](http://nekovm.org/)的应用程序，这是构建命令行工具的 goto 平台。

让我们看一下这些课程并解释它们。

```
/**
*@author kgarbaya
**/
import net.khaledgarbaya.processor.CmdProcessor;
import net.khaledgarbaya.helper.LogHelper;
class CommandLineTool
{
    /// parse commands and perform action
    private var processor:CmdProcessor;

    /// this is how haxe will know that this is the main class
    public static function main() : Void
    {
        var interpreter = new CommandLineTool();
        interpreter.run();
    }
    public function new()
    {
        processor = new CmdProcessor();
    }
    public function run():Void
    {
        var command = getCommandFromSysArgs();
        try
        {
            var ret = processor.process(command,Sys.args());
            if( ret != null )
                LogHelper.println(ret+"\n");
        }
        catch (ex:CmdError)
        {
            LogHelper.error("Unknown Command");
        }
    }

    public function getCommandFromSysArgs() : String
    {
        for(arg in Sys.args())
        {
            if(arg.charAt(0) == "-")
            {
                /// here you handle command config like -arg
            }
            else
            {
                return arg;
            }
        }

        return "help";
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个主类，你不能通过静态函数 main 来判断，所以在编译的时候 hax 会知道这是一个主类。CommandLineTool 类所做的是从 Sys.args 中获取所有参数，sys . args 是在您的可执行文件名称之后传递的参数，例如:`$mycmdtool arg1 arg2 .....`，将参数传递给一个命令进程，该命令进程将处理这些参数并将它们划分为 command 和 arg，然后如果发现匹配，则执行映射到用户传递的命令的命令。参见下面的代码。

```
/**
*@author kgarbaya
**/
package net.khaledgarbaya.processor;
import net.khaledgarbaya.helper.LogHelper;
import net.khaledgarbaya.command.SayHelloCommand;
import net.khaledgarbaya.command.impl.ICommand;
using Lambda;
using StringTools;
enum CmdError
{
    IncompleteStatement;
    InvalidStatement(msg :String);
}

class CmdProcessor
{
    /** connecting command to a specific function */
    public static var commands : List<{ name : String, doc : String, command : ICommand }>;
    private var currentTime:Float;

    public function new()
    {
        commands = new List();

        addCommand('hello', SayHelloCommand.helpString, new SayHelloCommand());
    }

    function addCommand( name, doc, command ) : Void
    {
        commands.add({ name : name, doc : doc, command : command });
    }
/**
    * process a line of user input
    **/
    public function process(cmd : String, args : Array<String>) :String
    {
        var output:String;
        if( cmd.endsWith('\\') )
        {
            throw "IncompleteStatement";
        }

        / **If the command is help** /
        if( cmd == 'help' )
        {
            return printHelp();
        }

        / **Other commands** /
        for( c in commands )
        {
            if( c.name == cmd )
            {
                currentTime = Date.now().getTime();
                output = c.command.execute(cmd, args);
                LogHelper.println(' Time passed '+((Date.now().getTime()-currentTime)/1000)+' sec for command "$cmd"');
                return output;
            }
        }
        return 'Command ' + cmd + ' Not Found, try to type help for more info';
    }

    public static function printHelp() :String
    {
        var ret : String = 'Awesome Shell \n';

        for (c in commands)
        {
            ret += '\n--------------------------\n\n' + c.doc ;
        }
        ret += '\n--------------------------\n';
        return ret;
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

每个命令基本上都是一个实现 ICommand 接口的类，I Command 接口只包含一个方法 execute(cmd，args):String，但是如果您想添加更多的方法，例如 undo 和 redo 等。为了演示的目的，我刚刚创建了一个简单的命令，在 shell hello 中打印当前时间。

```
/**
*@author kgarbaya
**/
package net.khaledgarbaya.command;

import net.khaledgarbaya.command.impl.ICommand;
class SayHelloCommand implements ICommand
{
    public static var helpString : String = "hello , Just Saying Hello with the current date";
    public function new()
    {}
    public function execute(cmd : String, args:Array<String>) : String
    {
        return "Hello the date is : " + Date.now().toString();
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

在你的命令行程序完成后，你需要做的是使用 neko 来构建。

```
neko Build.hxml 
```

Enter fullscreen mode Exit fullscreen mode

实际上,`Build.hxml`是一个快捷方式，不用输入很多命令，你只需要把它们列在这个文件中，Haxe 就会执行它们。

```
-main CommandLineTool
-neko ../bin/cmd_tool.n 
```

Enter fullscreen mode Exit fullscreen mode

如果你想测试应用程序，用你的终端导航到 bin 文件夹，执行下面的命令

```
neko cmd_tool.n hello 
```

Enter fullscreen mode Exit fullscreen mode

如果你不想再使用猫，你可以使用这个命令
为它创建一个本地可执行程序

```
nekotools boot cmd_tool.n 
```

Enter fullscreen mode Exit fullscreen mode

你可以在 github [这里](https://github.com/Khaledgarbaya/haxe-samples/tree/master/command-line-tool)找到完整的源代码，可以随意使用，让我知道你那令人敬畏的命令行工具。

干杯，

哈立德