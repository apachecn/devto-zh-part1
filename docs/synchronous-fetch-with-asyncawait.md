# 带异步/等待的“同步”提取

> 原文：<https://dev.to/johnpaulada/synchronous-fetch-with-asyncawait>

*最初发布于[媒体](https://medium.com/programmers-developers/synchronous-fetch-with-async-await-b45d87a16177)。*

## TL；速度三角形定位法(dead reckoning)

> `async` / `await`允许我们使用现代版本的 Javascript 以“同步”方式使用异步请求进行编程。

## 一种假想的介绍

作为 web 开发人员，我们对 API 提出很多请求——不仅是对我们自己的 API，也对其他人的 API。众所周知，他们真的很讨厌。

如果我们想向一个假想的 API `https://api.com`发出请求，该怎么办？

如果我告诉你，你可以用 Javascript 发出这样的请求呢？

```
const response = fetch('https://api.com/values/1');
const json = response.json();
console.log(json); 
```

Enter fullscreen mode Exit fullscreen mode

通常，这是不可能的，通常你会这样做:

```
fetch('https://api.com/values/1')
    .then(response => response.json())
    .then(json => console.log(json)); 
```

Enter fullscreen mode Exit fullscreen mode

这比前一个例子可读性差。

虽然，如果你尝试前一个例子，它不会工作。你会问为什么？伙计们，我们错过了咒语！

## 等待

允许我们等待异步请求的响应。

为了在我们假设的代码中使用`await`，我们可以这样做:

```
const response = await fetch('https://api.com/values/1');
const json = await response.json();
console.log(json); 
```

Enter fullscreen mode Exit fullscreen mode

我们来分析一下。

在第一行中，我们向`https://api.com/values/1`发出一个 GET 请求。我们不是继续下一行，而是等待请求完成，因此是 wait。完成后，它将解析后的值传递给`response`变量。

在第二行，我们得到了`response`的 JSON 版本。同样，我们使用`await`,这样我们可以等待它完成(或者失败),然后将结果传递给`json`变量。

最后，在最后一行，我们将`json`变量的值记录到控制台。

这使我们不必编写可读性不够好的代码，让我们能够编写更简洁的代码。

这是一种更直观的处理请求的方式。

为了帮助你更好地理解它，这里有另一种看待它的方式:

> `await`允许我们等待`Promise`来解析一个值。

只有在解析了`Promise`之后，`await`才会返回值。

抱歉，我是多余的，但这是你应该明白的。ðŸ˜„ ðŸ˜…

## 异步

但是等等，还有呢！不太清楚，但有些事我还没告诉你。你知道我们用`await`重写的代码吗？现在还不行。

什么！？Ikr。

为了让它工作，你需要把它包装在一个`async`函数中！

你是这样做的:

```
const request = async () => {
    const response = await fetch('https://api.com/values/1');
    const json = await response.json();
    console.log(json);
}

request(); 
```

Enter fullscreen mode Exit fullscreen mode

你只需要在函数声明前加上`async`关键字就可以运行了！EZ！

但不要只是从我这里拿走！在这里试试吧！只需点击运行按钮:

[异步/等待运行工具包示例](https://runkit.com/johnpaulada/async-await-example)。

> 如果你喜欢这篇文章，点击下面的心形、独角兽或举手按钮！ðÿ'™
> 谢谢！ðŸ'