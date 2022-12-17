# 我写第一个 Elm 应用时学到的东西

> 原文：<https://dev.to/mttaggart/things-i-learned-writing-my-first-elm-app-27b>

一些免责声明。我不是专业开发人员；我是一名自学成才的程序员，发现自己不太可能成为一所学校的技术总监。拥有了这些技能，通常情况下，我会看到为我们的问题建立一个解决方案的价值，而不是购买一个。要做到这一点，就意味着要成功通过严格的成本效益分析，即“构建”胜过“购买”。这也意味着我必须以这样一种方式构建，即我不是唯一一个可以维护变得至关重要的应用程序的人。

我想告诉你，我因此遵循每一个最佳实践:彻底的文档化，为每一件事编写测试。我还没到那一步。我还在学习。

大约一年前，我了解了榆树。涉足 Haskell 并编写了一个 React Redux 应用程序后，我完全迷上了函数式编程，尤其是[Elm 架构](https://guide.elm-lang.org/architecture/)。因此，当重写内部日程安排应用程序的机会来临时，我知道我想使用 Elm。它不是一个特别复杂的应用程序，只是一种以六天为周期而不是以每天、每周或每月为周期创建重复事件的方法。尝试一门新语言的完美尺寸。

但是等等，可维护性怎么样？引入一门完全陌生的语言对除了我之外的任何一个不得不看代码的人来说难道不是一场噩梦吗？

这让我想到了我学到的第一件事。

## Elm 制作了一些干净的代码

我喜欢 Elm 代码的阅读方式。`update`函数接收由`view`发送的一组消息，并更新`model`(应用程序/模块的状态)。看起来是这样的:

```
update : Action -> Model -> (Model, Cmd Action)
update action model =
    case action of
        NoOp ->
            model ! []

        RequestCalendars ->
            model ! [ getCalendarList clientId ]

        ReceiveCalendars calendars ->
            { model | calendars = calendars } ! [ getLetterDays letterDayCalendar ] 

        RequestLetterDayEvents ->
            model ! [ getLetterDays letterDayCalendar ] 
```

Enter fullscreen mode Exit fullscreen mode

如果你从来没有读过函数式语言，那可能看起来完全不可思议。首先要知道最上面一行是函数定义。它告诉我们什么类型进入函数，什么类型出来。然后我们给第二行的参数命名，相当于`function update(action, model){`。

这个`action`论点是我自己描述的一种特殊类型。它描述了视图(用户看到的/与之交互的)可以发送给`update`的消息种类。在它下面，你会看到一个`case/of`语句，类似于 JS 中的`switch`语句。在给定`action`的情况下，`->`之后的每一行都表示`update`应该返回什么。

`Action`其实是我学到的第二件事的一部分。

## 我喜欢严格的打字

我的编程之旅从 Python 开始，然后转移到 PHP，再到 JavaScript。所以很长一段时间我对静态类型有点担心，更不用说严格类型了。然后我学习了 Haskell，发现了一种思考编程的新方法。过程语言将程序视为按顺序执行的一组指令——典型的“配方”例子。这没什么，而且通常是构想问题空间的绝对正确的方式。相比之下，像 Haskell 和 Elm 这样的函数式语言要求程序员考虑进入函数的信息的“形状”以及应该从函数中出现的形状。对输入和输出之间的信息应用正确的操作会产生期望的结果。您开始考虑封闭的转换，而不是按顺序考虑步骤。这样做的时候，你*必须*完全清楚输入和输出之间的数据类型。这就是严格打字的用武之地。通过首先根据输入和输出的内容来定义函数，就不会对代码中发生的事情或传递的内容产生任何歧义。

考虑下面这个巨大的人为例子:

假设我想写一个函数，它接受一个数字数组并返回它们的平方。很简单，对吧？下面是 JS 中的这样一个函数:

```
function squareEach(nums) {
    let res = [];
    for (const n of nums) {
        res.push(n * n);
    }
    return res;
} 
```

Enter fullscreen mode Exit fullscreen mode

好吧，我知道有更干净的方法。给你:

```
function squareEach(nums) {
    return nums.map( n => {
        return n * n;
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

问题是，我只能通过评论知道`nums`的类型。虽然注释是好的，但是注释中没有任何东西会阻止我写一段非常糟糕的代码，最终把`undefined`或者更糟糕的东西发送给`squareEach()`。

现在，在 Elm 中有一个相同的函数:

```
squareEach : List Int -> List Int
squareEach nums =
    List.map (\n -> n * n) nums 
```

Enter fullscreen mode Exit fullscreen mode

首先我定义了`squareEach`，告诉 Elm(以及任何阅读我的代码的人)它将接受一个整数列表并返回相同的东西。然后我实现它，使用 lambda(匿名函数)和`List.map`来模拟与第二个 JavaScript 示例中的`Array.map()`相同的行为。

现在假设我在别处写了一个函数，试图传递，比如说，一个`String`到`squareEach`。Elm 编译器会捕捉它并为我提供一个错误。我甚至不能*编译*愚蠢的类型错误。

这是我学到的第三件事。

## 爱重新编译

我和 Elm 编译器吵了很多次。有些早晨，我不得不站起来离开电脑，因为我无法让编译器同意编译我的代码。我的想法总是错误的，花时间以适当的方式纠正我的错误会产生更干净、更优雅的代码。花大量时间阅读文档很有帮助。当 Elm 代码失败时，我的终端屏幕上总是出现红色文本，这种感觉并不好。但是你知道什么是难以置信的感觉吗？

没有运行时错误。

这是一种权衡:即使通过测试，也不要因为一个意外的值错过了我而在产品中找到大量的错误，而是不断地处理编译器错误会产生一个相当稳定的应用程序。破坏你的运行时间并不奇怪。这并不是说 Elm 开发了不会出错的应用程序——任何语言都取决于它的用户。然而，编译器捕获的问题数量大大减少了实时调试。向这种组合中添加好的测试是对稳定性的更大保证。

我说“稳定”，但是...

## 榆树还没完

毫无疑问，这种语言正在走向世界，我从未像现在这样有兴趣组装一个前端。然而，`core`库中仍然缺少一些东西，比如合理的`Date/Time`工具。我使用 Justin Mimb 优秀的 [date-extra](http://package.elm-lang.org/packages/justinmimbs/elm-date-extra/) 包来处理日期/时间解析。但是我不应该为 ISO 字符串安装第三方依赖，或者比较(`<`、`>` ) `Date` s

另一方面，社区套餐质量很高。要知道，如果你正在用 Elm 开发一个重要的应用程序，你最终会安装一些第三方的依赖项，或者编写一堆你自己的助手代码。

所以，如果你想改变你思考编程的方式，我强烈建议你去看看函数式语言。而且如果你想重新爱上 frontend，我强烈推荐 Elm。