# 除了引号之外，正则表达式由空格分隔

> 原文：<https://dev.to/adamkdean/regex-split-by-spaces-except-inside-quote-marks-5g92>

这是我在编写控制台输入处理程序时写的一小段代码。有时你会想用一个空格分割一个字符串，但是你如何输入一个以上的单词作为一个参数呢？你把它放在引号里，但是你怎么把它们分开呢？像这样:

```
MatchCollection matches = Regex.Matches(p, "[^\\s\"']+|\"[^\"]*\"|'[^']*'"); 
```

Enter fullscreen mode Exit fullscreen mode

是的，一行代码。不要忘记包括系统。文本。正则表达式！

让我们进一步做一个小测试(实际上是我正在使用的测试代码):

```
MatchCollection matches = Regex.Matches(p, "[^\\s\"']+|\"[^\"]*\"|'[^']*'");
WriteToConsole("Match Count: {0}", matches.Count);

foreach (Match match in matches) WriteToConsole(match.ToString()); 
```

Enter fullscreen mode Exit fullscreen mode

输入:

`The "quick brown fox" jumps over "the lazy dog"`

输出:

`Match Count: 5
The
"quick brown fox"
jumps
over
"the lazy dog"`

现在你知道了，这可能是最简单的方法。

享受