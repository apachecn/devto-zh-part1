# 延迟执行和 Dart 的并发模型

> 原文：<https://dev.to/jvarness/deferred-execution-and-darts-concurrency-model-27i>

你不得不佩服谷歌:他们用很棒的异步模型制作语言。Go(另一种 Google 语言)因其简单的并发性模型而声名狼藉，该模型使得你的代码很容易异步。

然而，Go 并不是唯一一种想让你在并行世界中的生活更轻松的谷歌语言。Dart 自带范例，使并发变得非常容易。

## 关键词

这个将会是这篇文章的一个很好的参考。如果你还没有读过这本书，我强烈推荐。

Dart 中的异步函数在函数签名中会有`async`或`async*`。这意味着该函数将为`async`返回一个`Future`，或者为`async*`返回一个`Stream`。这两者之间有细微的差别，但前提是相同的:你可以*执行*函数，而*继续*执行，而不需要*等待*函数执行完成。

## `Future`

在用 Dart 写代码的时候，当你想正在等待一个对象回来，并且只有一个对象回来的时候，你应该使用`Future`。也就是说，仍然有可能从你的 API 返回一个`Future<List<int>>`，但是当它有意义的时候，你可能想考虑使用一个`Stream<int>`来代替。我一会儿会解释它们的区别。

当你使用一个返回`Future`的 API 时，有几种不同的方法可以处理它:

```
import 'dart:async';

Future<Null> main() async {
  Future<String> name = futureName();
  print('Waiting for the name to come back...');
  name.then((theName) => print('Hello $theName!'));
}

Future<String> futureName() async {
  return new Future.delayed(new Duration(seconds: 3), () => 'Jake');
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您曾经在 JavaScript 中使用过 promise，您可能会注意到,`Future`的行为与 promise 非常相似。根据你的需求，有各种不同的[方式来处理`Future`。](https://api.dartlang.org/stable/1.24.2/dart-async/Future-class.html)

这是一种处理从`futureName()`返回的`Future`的*好的*方式，但是我更喜欢使用`await`语法:

```
import 'dart:async';

Future<Null> main() async {
  print('Waiting for the name to come back...');
  String name = await futureName();  
  print('Hello $name!');
}

Future<String> futureName() async {
  return new Future.delayed(new Duration(seconds: 3), () => 'Jake');
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们使用了`await`关键字，它实际上告诉 Dart 等待`Future`关闭，然后返回未来应该返回的值。代码仍然是异步的，但是代码将在执行前等待。尝试将第一个打印移动到`await`下面，注意在执行该语句之前需要几秒钟。

有了`Futures` s，也有可能返回`List`s:

```
import 'dart:async';

Future<Null> main() async {
  print('Just waiting for my list...');
  List<int> integers = await deferredExecution();
    for (var x in integers) {
    print(x);
  }
}

Future<List<int>> deferredExecution() async {
  List<int> list = [];
  int totalWait = 0;
    for (var x = 5; x >= 0; x--) {
    // sleep for x seconds...
    list.add(x);
    totalWait += x;
  }
  return new Future.delayed(new Duration(seconds: totalWait), () => list);
} 
```

Enter fullscreen mode Exit fullscreen mode

这不是很优雅，因为我们必须等待很长时间才能返回整数...

## 延期执行和`Stream` s

如果你正在返回一堆需要很长时间才能返回的东西，试着返回一个`Stream`并使用延迟执行来代替。

延迟执行本质上意味着您正在返回一些可以迭代的东西，但是这些项目将异步返回。如果你曾经编写过 C#代码，并且以前使用过延迟执行，Dart 的`yield`关键字的行为与 C#的`yield`完全相同，但是 C# [要求使用`yield`的方法返回`Enumerable<T>`的](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/yield)，Dart 强制返回一个`Stream`。

所以在`deferredExecution()`函数中，我们可以修改代码来使用`yield`关键字更有效地返回我们的整数:

```
import 'dart:async';

Future<Null> main() async {
  Stream<int> integers = deferredExecution();
  print('Just waiting for my stream...');
  await for (var x in integers) {
    print(x);
  }
}

Stream<int> deferredExecution() async* {
  for (var x = 5; x >= 0; x--) {
    yield x;
    // sleep for x seconds...
    await new Future.delayed(new Duration(seconds: x));
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们使用一个`await for`循环来迭代从`Stream`中获取的东西。这个特殊的语法会在检索项目时将项目返回到循环中，直到`Stream`关闭。

在这种情况下，我们仍然等待同样长的时间来获取所有的信息，但是这样效率更高，因为由于延迟执行，我们能够在返回每个元素时处理从函数返回的内容。

我希望这是有意义的。我知道有些例子不是最好的，但我希望这是有教育意义的！