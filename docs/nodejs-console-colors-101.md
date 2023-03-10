# node.js:控制台颜色 101

> 原文：<https://dev.to/mogery/nodejs-console-colors-101>

我们都见过一个模块或 node.js 应用程序改变了命令提示符字体的颜色。见鬼，即使是 npm 也会改变文本的颜色！

我会告诉你怎么做。

有两种方法可以做到这一点:

*   使用模块
*   不使用模块

## 简单易行的方法(带模块)

你可以去拿粉笔了:

`npm install chalk`

用粉笔很简单！例如，如果您想用蓝色显示 console.log，那么可以这样做:

```
const chalk = require('chalk');

console.log(chalk.blue('Hello world!')); 
```

Enter fullscreen mode Exit fullscreen mode

很简单，不是吗？

欲了解更多文档，请访问指南。

## 不容易的方式(无模块)

想减少依赖列表吗？没有人希望看到有太多需求的代码！走吧，跟我来。

这有点乱，但这基本上是其他模块所做的:

[https://repl.it/@moriczgergo/Nodejs-Colors-101-1?lite=true](https://repl.it/@moriczgergo/Nodejs-Colors-101-1?lite=true)

是的，那根长绳子会上色。这里有一个解释:

“\x1b[36m”部分会使您的文本变成青色，“%s”部分会被您的文本替换，而“\x1b[0m”部分会将颜色还原成它们应该的样子。

但是别担心，你不必记住颜色代码。相反，这里有一个参考！

```
Reset = "\x1b[0m"
Bright = "\x1b[1m"
Dim = "\x1b[2m"
Underscore = "\x1b[4m"
Blink = "\x1b[5m"
Reverse = "\x1b[7m"
Hidden = "\x1b[8m"

FgBlack = "\x1b[30m"
FgRed = "\x1b[31m"
FgGreen = "\x1b[32m"
FgYellow = "\x1b[33m"
FgBlue = "\x1b[34m"
FgMagenta = "\x1b[35m"
FgCyan = "\x1b[36m"
FgWhite = "\x1b[37m"

BgBlack = "\x1b[40m"
BgRed = "\x1b[41m"
BgGreen = "\x1b[42m"
BgYellow = "\x1b[43m"
BgBlue = "\x1b[44m"
BgMagenta = "\x1b[45m"
BgCyan = "\x1b[46m"
BgWhite = "\x1b[47m" 
```

Enter fullscreen mode Exit fullscreen mode

希望这篇教程能帮助到其他人。感谢阅读！