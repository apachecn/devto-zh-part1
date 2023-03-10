# C#中使用 async/await 的计算表达式

> 原文：<https://dev.to/garuma/computation-expressions-in-c-using-async-await-1jdn>

我已经用 C# 7.1 新的可定制的异步/等待管道做了一些有趣的事情:

*   [ActivityTask，Android 上异步/等待的助手](https://dev.to/blog/2017/05/22/activitytask-async-await-android-cornercases/)
*   [重新创建 F#可能是 C#中的计算表达式](https://dev.to/blog/2017/04/26/maybe-computation-expression-csharp/)

在这篇文章中，我将描述我如何利用这些过去的经验，并概括第二个条目的核心思想，使用 async/await 机制，通过(ab)提供与 C#中的 F#计算表达式有些等价的构造。

如果你以前从未听说过 F#计算表达式，我推荐你阅读关于它的有趣和收益系列。否则 TL；DR 在于，没有了一元的包袱，计算表达式本质上允许你定制如何将一系列“语句”链接在一起，并在它们之间插入你自己的逻辑。

这种计算表达式的一个著名例子是[异步工作流](https://docs.microsoft.com/en-us/dotnet/fsharp/language-reference/asynchronous-workflows)，它首先导致了 C# async/await 的创建(C# async/await 是它的计算表达式父代的一种更加专门化/优化的形式)。

在某种程度上，我在这篇文章中描述的基本上是我们如何设法使用更专业的 C# async/await 接口返回到 F#计算表达式的原始定义。

如果你想直接跳到代码[，在 GitHub](https://github.com/garuma/Neteril.ComputationExpression) 上抓取它。该库还包含一个[工作簿文件](https://github.com/garuma/Neteril.ComputationExpression/blob/master/Examples.workbook)，其中有几个利用 API 的例子，这样你就能感受到它允许你做什么。

对于其他人来说，这里是存储库自述文件中给出的 API 的示例:

```
Option<int> TryDivide (int up, int down)
    => down == 0 ? None<int>.Value : Some.Of (up / down);

class MaybeBuilder : IMonadExpressionBuilder
{
    IMonad<T> IMonadExpressionBuilder.Bind<U, T> (IMonad<U> m, Func<U, IMonad<T>> f)
    {
        switch ((Option<U>)m) {
          case Some<U> some: return f (some.Item);
          case None<U> none:
          default: return None<T>.Value;
        }
    }

    public IMonad<T> Return<T> (T v) => Some.Of (v);
    public IMonad<T> Zero<T> () => None<T>.Value;
    // We don't have optional interface methods in C# quite yet
    public IMonad<T> Combine<T> (IMonad<T> m, IMonad<T> n) => throw new NotSupportedException ();
}

// Returns `Some 15`
ComputationExpression.Run<int, Option<int>> (new OptionExpressionBuilder (), async () => {
    var val1 = await TryDivide (120, 2);
    var val2 = await TryDivide (val1, 2);
    var val3 = await TryDivide (val2, 2);
    return val3;
})

// Returns `None`
ComputationExpression.Run<int, Option<int>> (new OptionExpressionBuilder (), async () => {
    var val1 = await TryDivide (120, 2);
    var val2 = await TryDivide (val1, 0);
    var val3 = await TryDivide (val2, 2);
    return val3;
}) 
```

在这个片段中，我将[举一个我之前提到过的可能/选项单子](https://dev.to/blog/2017/04/26/maybe-computation-expression-csharp/)的例子，并通过计算表达式机制对其进行编码，以达到相同的结果。在这种情况下，`await`关键字用于表示等价的 F# `let!`关键字，它是计算表达式生成器的`Bind`方法的简称(`Bind`是核心的一元操作，它允许所有这些“语句”链接在一起)。

要实现自定义计算表达式，您必须做两件事:

1.  定义一个实现空`IMonad`接口的类型。这只是作为一个标记，以便能够使用库的自定义异步机器。
2.  更有趣的是，提供一个`IMonadExpressionBuilder`接口的实现，您可以在其中放置代码来定制计算表达式块的执行行为。

在上面的例子中，主要的定制是在`Bind`方法中，我们获取执行前一条语句的结果，然后根据结果或者将结果向下传递，或者通过返回`None`来简化所有的事情。

另一个 C#专门实现了 F#中也编码为计算表达式的内容的例子是通过`yield`操作符的`IEnumerable<T>`状态机。

有了这个库，我们可以使用构建器的`Combine`方法来表达它，实现类似这样的东西(摘自示例工作簿):

```
using static Neteril.ComputationExpression.ComputationExpression;
// The Enumerable monad is given in the library
using Neteril.ComputationExpression.Instances;

var result = ComputationExpression.Run<int, EnumerableMonad<int>> (new EnumerableExpressionBuilder (), async () => {
    var item = await (EnumerableMonad<int>)new [] { 1, 2, 3 };
    var item2 = await (EnumerableMonad<int>)new [] { 100, 200 };
    // We want back a enumeration containing the concatenation of (item, item2, item1 * item2)
    // for all successive values of item1 and item2
    await Yield (item);
    await Yield (item2);
    return item * item2;
});
string.Join (", ", result.Select (i => i.ToString ()));
// => [1, 100, 100, 1, 200, 200, 2, 100, 200, 2, 200, 400, 3, 100, 300, 3, 200, 600] 
```

在这个新示例中，monad 以第一个`await`(同样等价于 F# `let!`)绑定到给定集合的每个后续元素的方式实现。结果是一个`EnumerableMonad<T>`，它本身是一个`IEnumerable<T>`(也就是说，你可以在它上面进一步使用 LINQ)。

因为在这种情况下，没有办法直接劫持 C# `yield`运算符(就像 F#会做的那样),所以这里用表达式`await Yield`表示中间值，用表达式`return`表示最后一个值。

下面是示例工作的构建器的实现(注意现在实现的`Combine`方法):

```
public class EnumerableExpressionBuilder : IMonadExpressionBuilder
{
    IMonad<T> IMonadExpressionBuilder.Bind<U, T> (IMonad<U> m, Func<U, IMonad<T>> f)
    {
       var previousEnumerableMonad = (EnumerableMonad<U>)m;
       return new EnumerableMonad<T> (previousEnumerableMonad.SelectMany (u => (EnumerableMonad<T>)f (u)));
    }

    IMonad<T> IMonadExpressionBuilder.Return<T> (T v) => new EnumerableMonad<T> (Enumerable.Repeat (v, 1));
    IMonad<T> IMonadExpressionBuilder.Zero<T> () => new EnumerableMonad<T> (Enumerable.Empty<T> ());

    IMonad<T> IMonadExpressionBuilder.Combine<T> (IMonad<T> m, IMonad<T> n)
    {
       var enumerableMonad1 = (EnumerableMonad<T>)m;
       var enumerableMonad2 = (EnumerableMonad<T>)n;
       // Simply use LINQ Concat method
       return new EnumerableMonad<T> (enumerableMonad1.Concat (enumerableMonad2));
    }
} 
```

有趣的是，我记得在早期的著作中读到过单子及其与 C#的关系，LINQ `SelectMany`操作符是单子意义上的`Bind`操作的一个例子。有了这个我们就有证据证明它确实是真的。

实现这一切的实际代码相当简单[异步方法构建器实现](https://github.com/garuma/Neteril.ComputationExpression/blob/master/Neteril.ComputationExpression/MonadAsyncMethodBuilder.cs)。如果你读过我在本文开头提到的任何一篇文章，它本质上是做同样的事情，用更多的技巧来处理在`IMonad<T>`接口中增加的泛型类型的间接性。

它还通过 [`Machinist`类](https://github.com/garuma/Neteril.ComputationExpression/blob/master/Neteril.ComputationExpression/Machinist.cs)更深入地查看编译器生成的异步状态机的内部结构，以便它可以控制存储在其中的实际步进和计数器值。例如，这允许相同的状态机被“倒带”,这对于支持类似上述可枚举示例的情况是必不可少的。