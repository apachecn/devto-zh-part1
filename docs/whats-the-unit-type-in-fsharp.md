# F#中的单位类型是什么？

> 原文：<https://dev.to/josegonz321/whats-the-unit-type-in-fsharp>

*最初发布于:[http://www . mindbodysouldeveloper . com/2017/04/08/whats-the-unit-type-in-f-sharp/](http://www.mindbodysouldeveloper.com/2017/04/08/whats-the-unit-type-in-f-sharp/)*

我的 F# [冒险继续](http://www.mindbodysouldeveloper.com/2017/01/07/intro-functional-programming-resources/)。

我很快了解到，在函数式编程中，每个函数都必须返回值。

例如:

```
let Area length height =
    length * height 
```

上面的`Area`函数将总是返回`length * height`的任何值。

如果你不想让你的函数返回任何东西呢？ðÿ”

假设您有下面的 C#代码

```
void PrintNumbers(int min, int max)
{
    for (int i = min; i < max; i++)
        Console.WriteLine(i);
} 
```

F#中的相同代码:

```
let PrintNumbers(min max)
    for x in min..max do
        printfn "%i %i" x 
```

(**旁注**:我爱 F#的简洁优雅)。

我们的`PrintNumbers`函数没有返回任何东西。它只是把数字打印在屏幕上。

> 搞什么鬼？我以为在函数式编程或 F#中，每个函数都必须返回一些东西。

你可能会想...你是正确的！

吃块饼干吧。ðŸ

哦，但是我不能通过电脑屏幕给你。哦，好吧，我替你吃了它！(好吃，好吃)

## 什么是单位类型？

等一下。在我继续之前，让我清理一下这些饼干屑。

好吧，酷。所以，如果你把光标放在`PrintNumbers`函数上。注意工具提示:

[![Unit Type](img/51a8a777767b6b5b81a6623cd05cb8a0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CtDa_Bxi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.mindbodysouldeveloper.com/wp-content/uploads/2017/04/UnitType.png)

它说，这个函数接受两个整数，返回一个`unit`值或者什么也不返回。

换句话说，`unit`相当于 C#中的`void`。

现在是官方的(干的定义)ðÿ™„.

[MSDN 说](https://docs.microsoft.com/en-us/dotnet/articles/fsharp/language-reference/unit-type):

> 单位类型是指示缺少特定值的类型；单位类型只有一个值，当不存在或不需要其他值时，它充当占位符。

## TL；速度三角形定位法(dead reckoning)

这是对`unit`类型的一个超级简短的介绍。我确信有比我在这篇文章中解释更多的东西。

希望，当你在 F#冒险中看到`unit`时，你不会感到奇怪。

再次重申:

F# `unit` = C# `void`

你对 F# `unit`有什么体验？你还能和我们分享什么？请在下面的评论中告诉我们！

下次再见，保重。

又及:我给你吃的饼干真是美味。ðŸ˜Š

*特别感谢 [Kit Eason](https://twitter.com/kitlovesfsharp) 启发了这篇文章。在 Pluralsight 查看他令人敬畏的 [F# Jumpstart](https://app.pluralsight.com/library/courses/fsharp-jumpstart/) 课程。*