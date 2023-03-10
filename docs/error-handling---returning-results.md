# 错误处理-返回结果

> 原文：<https://dev.to/michael_altmann/error-handling---returning-results>

如果你用谷歌搜索“错误处理”。你得到关于异常和异常处理的结果。因此，我们许多人认为使用异常是现代处理错误的灵丹妙药。Net/Java 应用程序。是的，我们肯定可以对应用程序错误使用异常，但我认为我们可以用一种更干净的方式来做。

本文讨论的想法最初是由马丁·福勒于 2004 年在他的文章《T2 通报》中提出的。

# 异常有什么不好？

例外并不是完全错误的。在他的一些文章中([这里](http://enterprisecraftsmanship.com/2017/03/13/error-handling-exception-or-result/)和[这里](http://enterprisecraftsmanship.com/2015/02/26/exceptions-for-flow-control-in-c/) ) [描述了哪些情况下应该使用异常，哪些情况下不应该。我完全同意他的观点，因此我试着总结一下。](https://twitter.com/vkhorikov)

有两种类型的错误。第一，有错误，我们不知道如何处理。这种类型的错误应该由异常来处理，因为我们不知道如何处理这种情况。最好的选择是终止当前操作。
第二，有错误，我们知道如何处理。例如验证错误或短暂/可恢复的软件和硬件故障。如果你需要帮助来区分这两种错误类型，请阅读[中的](http://enterprisecraftsmanship.com/2017/03/30/what-is-an-exceptional-situation-in-code/)。我们跳过第一种错误类型的处理，因为这不是本文的主题。

# 处理错误我们知道如何处理

好的，我们想要处理我们知道如何处理的错误，但是不使用异常处理。有什么想法吗？在失败的情况下，我们可以返回一个空对象或 false，但这样我们就会丢失大量关于失败原因的信息。因此，一个好的解决方案(深受 Martin Fowler 和 Vladimir Khorikov 的启发)是返回一个指示操作成功或失败的结果对象。下面是一些示例代码:

```
public Result DoSomething()
{
  // do something

  // return success result
  return Results.Ok();

  // return error result
  return Results.Fail("An error occured.");
}

public void CallDoSomethingMethod()
{
  var result = DoSomething();

  if(result.IsFailed)
  {
    // handle error
    Console.WriteLine(result);
    return;
  }

  // continue with program
} 
```

第一个方法返回一个指示成功或失败的结果对象。第二种方法——客户端——检查结果对象，并且必须处理两种情况，错误和成功情况。

## 优点

*   清晰的程序流，从 throw 到 catch 没有不透明的跳转。
*   方法签名显示返回的结果对象。所以从客户的角度来看，错误处理机制是绝对清楚的。返回多个错误是可能的

## 弊

*   客户端必须手动检查操作是否失败。如果使用异常处理，就没有机会忽略异常。
*   C#或 Java 中没有内置语言支持。

我做了一个小的。Net 库提供了一个轻量级的结果对象。您可以返回多个错误或成功消息。每个错误都可以被设计成类，你也可以用层次化的方式描述错误的原因或元数据。当然，返回一个带有值的结果对象也是可能的。

# 链接

*   [GitHub FluentResults](https://github.com/altmann/FluentResults)
*   [获取流量结果](https://www.nuget.org/packages/FluentResults/)

*本帖最初发表于[medium.com](https://medium.com/@michael_altmann/error-handling-returning-results-2b88b5ea11e9)T3】*