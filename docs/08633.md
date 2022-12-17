# node.js:文本定位 101

> 原文：<https://dev.to/mogery/nodejs-text-positioning-101>

昨天，我们讨论了文本着色，但今天更有趣。我们正在定位文本。

这样做的方法是另一个神奇的字符串。这次是这样的:

```
console.log("\x1b[10;15H%s", "Hello World!"); 
```

Enter fullscreen mode Exit fullscreen mode

这将打印“你好，世界！”在屏幕的第 10 行，从第 15 列开始。

看，公式基本上是这样的:

```
\x1b[r;cH 
```

Enter fullscreen mode Exit fullscreen mode

你只需要把“r”改成指定的行，“c”改成指定的列，嘣！您刚刚放置了文本。