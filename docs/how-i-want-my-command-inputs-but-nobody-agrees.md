# 我希望我的命令输入，但是没有人同意

> 原文：<https://dev.to/legolord208/how-i-want-my-command-inputs-but-nobody-agrees>

```
switch cmd {
case "help":
    search := strings.Join(args, " ")
    printHelp(search)
case "exit":
    closing = true
case "exec":
    if nargs < 1 {
        stdutil.PrintErr("exec <command>", nil)
        return
    }
    ... 
```

Enter fullscreen mode Exit fullscreen mode

这是我的蹩脚应用 DiscordConsole 旧版本的一些源代码，用 Go 写的。(正在进行改写。我选择它是因为它完美地展示了我想要的指挥系统，以及语言是如何让这变得困难的。
来吧，我知道你喜欢你的 OOP，但是对
来说是不是太过分了

```
public interface CommandHandler {
    public String[] acceptCommands();
    public int minArg();
    public int maxArg();
    public void handleCommand(String, CommandArgs);
}
public class EchoCommand implements CommandHandler {
    public String[] acceptCommands() {
        return []String{"echo"};
    }
    public int minArg() { return 1; }
    public int maxArg() { return Integer.MAX_VALUE; }
    public void handleCommand(String cmd, CommandArgs args) {
        System.out.println(args.join());
    }
}
... 
```

Enter fullscreen mode Exit fullscreen mode

对我来说，这实在是太丑了。哦，上帝，多么低效。很抱歉混合编程语言，我认为 Java 完美地描述了 OOP，Go 完美地描述了我想要的命令系统，同时仍然使用“switch”关键字，而不是像 Rust 那样“match”。

但是你的系统有一个优点。它允许在多个文件中分割命令。现在你不停地唠叨我，说我的指挥系统有多糟糕。是的，我确实不能把我的文件分成多个文件。确实，一旦你得到超过 3 个命令，那就真的是个问题了。但是这也是 OOP 方式进行不必要的分配和函数调用，并且设置起来很痛苦。

所以让我们跳出任何编程语言的规则来思考。对我来说，什么是完美的指挥系统？

```
match cmd {
    "echo" => {
        usage_min!(1, "No argument specified");
        println!("{}", args.join(" "));
    },
    "some_long_command" => include!("commands/some_long_command.rs"),
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

这个例子是用 Rust 写的。关键词`match`与`switch`相似，但更酷。它完美地展示了宏是如何让廉价的应用程序在代码中看起来很好的，是的，这就是说所有没有宏的语言都很糟糕。我喜欢宏。
这也表明，让一个非面向对象的命令系统看起来更好，并把它分割成多个文件，这几乎是*的*可能。唯一阻止我们的是 Rust 中的`include!`关键字是存在的，但是它看不到局部变量和宏。尽管如此，更多的语言应该有宏，更多的语言应该能够包含文件的内容，而不需要任何卫生之类的东西。

整个帖子本质上是我希望 [#32379](https://github.com/rust-lang/rust/issues/32379) 得到修复...