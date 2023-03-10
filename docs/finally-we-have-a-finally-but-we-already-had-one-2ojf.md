# 最后，我们终于有了一个。但是我们已经有一个了。

> 原文：<https://dev.to/michaeljota/finally-we-have-a-finally-but-we-already-had-one-2ojf>

[![Promise](img/9bda55cbf68241ee5592a0740be93cf0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kLGNNi4r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://78.media.tumblr.com/625dcb0918e78fdf75030507f8085039/tumblr_n05mepfYgB1tq4of6o1_500.gif) 
我喜欢挖掘 JS 即将推出的新功能，即使大多数时候我们需要将它们转移下来，但你应该知道，你可以而且应该使用 ES2015+语法将它们部署到生产中。在第三阶段的当前提案列表中，有一件事引起了我的注意。那就是[诺言。](https://github.com/tc39/proposal-promise-finally)

# 终于！

所以我们现在有了一个最终的方法来实现承诺。finally 方法将被调用 always，并且不关心承诺是被实现还是被拒绝。它不会改变决心承诺的价值，但可以用新的理由拒绝承诺。目前在 NPM 的其他未来图书馆中有几个实现，正如在当前的提议中所写的。然而，这将很快成为标准，允许在 evergreen 浏览器中使用该方法。

# 何时使用

finally 语句在编程中非常常见，尤其是在处理即使在不成功的操作中也必须关闭的外部连接时，例如读取外部文件或数据库操作。这样的连接需要关闭，不管操作是否成功，我们只需要关闭连接。

# 如何使用

finally 方法非常简单，每个承诺都有一个，它也会返回一个承诺，所以你可以像往常一样链接。

```
Promise
    .resolve(3)
    .finally(
        () => {
            console.log('FINALLY!');
            return 4;
        }
    )
    .then(
        value => console.log(value) // Output: 3
    ); 
```

Enter fullscreen mode Exit fullscreen mode

您可能会注意到，在 finally 中解析一个值没有任何效果。但是，您应该知道，根据规范，Promise 可能会被 finally 方法拒绝。

```
Promise
    .resolve(3)
    .finally(
        () => {
            console.log('FINALLY!');
            throw 4;
        }
    )
    .then(
        value => console.log(value) // Does not print
    ).catch(
        error => console.log(error) // Output: 4
    ); 
```

Enter fullscreen mode Exit fullscreen mode

# 如何使用它，今天

嗯，你可以使用不同的未来库之一，比如[蓝鸟](https://github.com/petkaantonov/bluebird)，或者 finally shim 并将其用于 ES2017，它们有一个新的语法来处理承诺，`async` / `await`。我真的很喜欢它们，它们在处理承诺方面非常得心应手，编写的代码看起来非常干净。这种耦合使得每个函数，一个异步函数，都被解析为一个常规承诺。所以，你可以在异步函数声明之后做 finally。

```
const promiseA = async () => {
    return await 3;
}
promiseA.finally(() => console.log('The promise either was a success or a failure')); 
```

Enter fullscreen mode Exit fullscreen mode

按照规范，将调用 finally 方法。但是，如果我们需要在异步函数中使用`try` / `catch`(你应该这样做)。然后，我们可以使用`finally`语句。

```
const aSuperUsefulPromise = async () => {
  try {
    return await PromiseThatCouldThrow();
  } catch (error) {
    console.log(error);
    // We don't modify the promise here, just catch the error and handle it.
  } finally {
    return await 4;
  }
}

console.log(await aSuperUsefulPromise()) // Outputs: 4 
```

Enter fullscreen mode Exit fullscreen mode

[![Wait... What?](img/c581d491a9191ce8afe86d667aa25321.png)](https://i.giphy.com/media/CDJo4EgHwbaPS/giphy.gif) 
嗯，在处理`try` / `catch` / `finally`的时候，你可以修改一个已解决承诺的值。注意和理解这一点非常重要，因为你可能会期待一个不同的结果。因此，永远不要在 finally 语句中返回。这可能对你来说很奇怪，因为 JS 有一个隐式的 return 语句，但是这个 return 将在 finally 语句之后调用，而不是之前。

## 结论

Promises 中的`finally`方法将会是某种东西，但是我们可以使用当前的`try` / `catch` / `finally`语句完成或多或少相同的行为，但是我们需要小心。

写这篇文章让我学到了很多，我希望你能对新的`finally`方法有更多的了解。感谢您的阅读，和往常一样，如果我犯了错误，请在下面的评论中指导我，我将深表感谢。